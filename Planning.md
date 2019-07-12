### Fieldextra planning, with priorities and assigned tasks

#### Release planning
* **v13.1.0** : July 31, 2019
* **v13.2.0** : September 15, 2019
* **v13.3.0** : January 31, 2020
* **v13.4.0** : April 30, 2020

#### Code development
* [Bug corrections](#bug)
* [Code optimization](#code_optimization)
* [Code consolidation](#code_consolidation)
* [Feature consolidation](#feature_consolidation)
* [New features](#new_feature)
* [User interface](#interface)
* [Environment](#environment)
* [fx tools](#fxtool)
* [Documentation](#docu)
* [Administrative](#admin)

---------
##### Bug corrections <a name="bug"></a>
---------

###### Priority high

###### Priority medium
     + [3d] Consolidate computation of product halo in fxtr_control:set_data_reduction
       > currently the algorithm is not 100% robust, e.g. when neighbourhood 
         probability or use_operator is used
       > halo may be overestimated, leading to a too large memory footprint;
         this should be catched, and the user should have the opportunity to
         set a halo manually
     + [3d] Computation of neighbourhood in regridding algorithm does not take into
       account metric terms (euclidian distance in target coordinates system is used);
       this is a problem when interpolating to a (rotated) lat/lon grid with grid pole
       within the original grid

###### Priority low


---------
##### Code optimization <a name="code_optimization"></a>
---------

###### Priority high

     + [-> 13.2.0] [jmb/CSCS;3d] [issue #40]
       Detailed code profiling of COSMO-NExT system
       > Find possible bottlenecks, optimize
     + [-> 13.2.0] [jmb;3w] [issue #39]
       Optimization of input
       > reading and decoding input records is sequential
       > proposed approache: read in advance

###### Priority medium

     + [3d] Move sorting along time dimension from store_field() to generate_output()
       > in case the input records are sorted in decreasing date, the current 
         implementation is very inefficient: records are internally re-shuffled
         for each new contributing time plan (this is the case for some IFS files)
     + [3d] Optimize stab_lookup (search algorithm, create different storage classes)
     + [1w] Avoid using strings as much as possible (tag component of ty_fld_id ...)
     + [1w] Optimize fxtr_control
       > use on-line cache of processed namelist
       > review algorithms
       > code profiling to detect possible optimizations
     + [1-2w;with CSCS support] Evaluation of ScaleMP software, to use multiple
       nodes as a single virtual shared memory system
       > more threads (but how is the scaling?)
     + [with CSCS support] I/O optimization
       In memory communication, avoid unnecessary encoding / decoding cycle
       > Some possibilities :
         - In-memory communication using a NetCDF framework available at CSCS
           (require to implement NetCDF input, which would be a side benefit)
         - In-memory communication with ADIOS API (support: Jean Favre)
         - In-memory communication with OASIS coupler

###### Priority low

     + Extend just on time mode
       > Just on time also when time operator is used (at least a sub-category)
       > Memory footprint optimization
       > Load balance optimization
       (note that load balance and memory footprint optimization can also be
        achieved by using temporary files for temporal reduction)
     + [2-4w] Introduce MPI based parallelism (maybe at product level first)
     + Full code review, update/rewrite memory management
       > to support very large problems (>2.E7 hgrid size, >100 members)
       > to better use processor cache and to reduce memory stress 
       > to make the code GPU capable
       > review stack usage, avoid silent stacksize overflow
         (could lead to invisible corrupted data)
     + Evaluate use of accelerator (GPU)
       > [mikko.partio@fmi.fi:
          We decode GRIB 1 and GRIB 2 simple packing with GPU's.
          The reason why we don't already do the encoding in GPUs is that decoding is
          much easier to implement than encoding. The performance gain is also much
          larger in decoding: we have measured up to 100x increase in performance when
          compared to CPU implementation, whereas the performance gain of encoding is
          only around 6x. These numbers include the time that it takes to move the data
          to and from device memory. The performance gain is dependent on grib size: the
          larger the grib, the better the GPU performs. We regularly handle Hirlam and
          IFS gribs ranging from ~1000000 to ~4000000 grid points per field.
          Our implementation is quite simple and it is based on grib_api. There has been
          some talk of releasing the code as it is or to provide a patch to grib_api, but
          nothing has been decided so far.]


---------
##### Code consolidation <a name="code_consolidation"></a>
---------

###### Priority high

     + [jmb;1w] [issue #117]
       Consolidated usage of meta-information undef value
       > Define iundef as the smallest integer which can be represented
       > Always use iundef for 'undefined' (currently -1 is used in some cases);
         this requires for all meta-information: (1) consistent translation between
         GRIB and internal representation, in both directions, (2) for other format,
         translation from undef to output representation (backward compatibility!),
         (3) consistent translation from user defined value in internal representation.
       > systematically use grib_is_missing() and grib_set_missing() when working
         with GRIB 2 meta-information

###### Priority medium

     + [bap;3d] [issue #2]
       Consolidated wind shear operators
       > surface boundaries specified as namelist arguments
       > in GRIB 2, only use short names WSHEAR_INT and WSHEAR_DIFF
       > consider using the same kernel in procedures wind_shear() and
         wind_shear_differential()
     + [jmb;1d] 
       Introduce a second optional argument to the class of hoper sqmax..., taking the
       value 'tiled' or 'shifted' (i.e. one would have hoper='sqmax,r', or 
       hoper='sqmax,r,tiled', or hoper='sqmax,r,shifted')
     + [1w] [issue #8]
       Consolidate memory monitoring
       > Add global memory monitoring of non instrumented libraries
         (currently icontool, rttov)
       > Keep track of memory usage for all repositories used in fxtr_storage
     + [2d] GRIB 2 implementation
       > systematically use grib_is_missing() and grib_set_missing()
     + [2d] Clean-up copen_c.c, add compatibility with Mac OS X (statfs --> statvfs ?)
     + [2d] Split fxtr_operator_generic (e.g. level reduction or not, cache or not)
     + [2d] Replace call to external C procedures with BIND (see mail Oli on 22.09.14)
     + [3d] Consolidate access to INCORE
       > unified access to incore fields; curently access is provided by one of
         incore%*, incore_fields(:), get_incore_field(), get_incore_with_tag()
     + [1w] Re-organize ty_fld_origin and ty_fld_product
       > this is required to support observations
       > field_origin should contain all meta-information which is not essential
         for the correct behaviour of fieldextra - should be renamed to
         field_additional_minfo
       > field_additional_minfo should be splitted in data depending only on field
         and data depending on field and validation date
       > rename sc_pcat_{radar,satellite} to sc_pcat_obs 
         (use new 'class' to differentiate)
     + [2w] KIND specified for all REAL and all INTEGER, REAL explicitely set to
       32 bits (possible in fortran 2008), real_in_byte set accordingly
     + [2d] Introduce in fxtr_operator_support an operator to compute the scalar product
       of a vector field with the gradient of a scalar field; use it in
       geostrophic_wind() and  geostrophic_vorticity_advection()

###### Priority low

     + [2d] Unified interface to decide when two fields represent the same quantity
       (used in field_compare, get_ic_field, get_fields, ...)
     + [3d] Re-structure read_location_additional()
     + [3d] Clean-up support_misc
       > Replace parse_operator_definition(), parse_transformation() and
         parse_prototype() with a generic procedure, replace corresponding
         types with a generic type
     + [3d] Clean-up fxtr_control
       > handle io specifications as field specifications:
         introduce io_spec, extend io_spec_default, extend only_for_inout(),
         add populate_io_spec()
       > default format for generic output should be set in the corresponding
         write routine and not in this module
       > less module variables, use procedure arguments instead
     + [1w] Replace POINTER with ALLOCATABLE in TYPE when possible (safer)
     + [1w] GRIB 1 / GRIB 2 implementation
       > support_grib1 error handling similar to support_grib2 (more verbose)
       > write_grib1 error handling similar to write_grib2 (more verbose)
       > decoding of model type and model name in support_grib1 similar to
         support_grib2 (table based, use common table where possible)
       > externalize look-up table for model name
     + [2w; support by F. Prill required] Consolidate ICON support
       > integrate memory reservation and monitoring
       > introduce graceful exception handling in icon tools
         (currently exceptions detected in icon tools generate a program stop)
       > introduce integrated measurement of omp speedup
       > add information on grid point neighbourhood for global grid
     + [2-4w] Common library of services for COSMO software, which is efficient for all
       COSMO codes (incl. ICON) and which minimizes the number of modules to import
       for accessing a requested functionality
       > would replace the currently imported COSMO modules
       > see README.cosmo_api
       > see minutes of 18.12.2013 meeting
       > should be discussed at the COSMO TAG level


---------
##### Feature consolidation <a name="feature_consolidation"></a>
---------

###### Priority high

     + [jmb,Tanja] 
       Consolidate ASCII_TABLE output 
       > improve support of meta-information when mixing PDF products
       > new out_type_undeflabel, to allow 'NaN', '' or other literal
         string to flag undefined values (?)
     + [jmb] 
       Remove BLK_TABLE (obsolete option), DAT_TABLE, FLD_TABLE and XLS_TABLE
     + [jmb;1w] [issue #9]
       Consolidate computation of SYNSAT products
       > see modifications in COSMO release 5.3, procedure prepare_rttov_input()
       > add latest rttov library release
     + [jmb;2d] [issue #130]
       Update algorithms to compute HPBL and BRN (according to COSMO)

###### Priority medium

     + [jmb,Tanja] 
       Review and consolidate BLK_TABLE output 
       > global & data header
       > efficiency
       > code clean-up
     + [jmb,Tanja] 
       Review and consolidate ASCII output format
       > consider GeoTIFF (fortran library available by Davide Cesari)
       > just on time for some ASCII_TABLE layout (?)
     + [jmb] 
       Improve handling of NetCDF dimensions
       > keep original dimension names when possible
       > add possibility for user defined dimension names
     + [jmb] 
       Add minimal support for unregistred field names
       (to support e.g. fxfilter or other simple operations)
       > use NetCDF variable name when present
       > use UNKNOWN_i, i=1...n otherwise
     + [1d] Check that all meta-information of a multi-levels field are
       consistent (e.g. units, origin ...); otherwise, vertical operators may
       lead to unpredictable meta-information values
     + [1d] Systematic check of staggering information in all operators
     + [1w] Systematically check and track fields units
     + [2d] Review gradient(), calc_sqrt(), and metric_coefs(); remove some restrictions
     + Use analytical formula to compute hydrostatique inter-/extrapolation
       (mail U.Blahak 09.2017, implemented in INT2LM)

###### Priority low

     + [1d] Check internal coding of gamma angle for rotated lat/lon grids
     + [2d] Make pressure@p-levels and height@h-levels automatically available when
       computing a new field; remove ad hoc solution in write_l2e.
     + Evaluate use of COSMO observation operators library
     + Evaluate use of Davide GIS library


---------
##### New features <a name="new_feature"></a>
---------

###### Priority high

     + [4-8w] Full support of unstructured ICON grid
       > adapt interface (e.g. imin, jmin)
       > remove any implicit assumption on regular grid
       > add support for vector fields (VN interpolation â~@¦)
       > add support for more fieldextra operators
     + [1d;bap;request from Christoph Spirig] Support ECMWF monthly and seasonal
       forecasts
       > new bi-linear interpolation algorithm for location_to_gridpoint, taking
         into account land-sea mask (Christoph Spirig will provide the algorithm)
     + [3d] add possibility to restrict any operator to some geographical subdomain
        (e.g. region, frame; with identity operator outside of the subdomain)

###### Priority medium

     + [1d] Implement numberOfVgridUsed
       (old ivctype; set lowest_full_level and sc_vc_type_* ...)
     + [2d] Add/consolidate support for
       > tiles (fxtr, GRIB1/2, NC)
       > coding of EPS perturbation (GRIB2)
       > coding of generic EPS quantiles difference (GRIB2)
       > coding of EPS probabilities with respect to reference distribution (GRIB2)
       > multi-layers snow model (fxtr, GRIB1/2, NC)
       > coding of kilometric grids (GRIB2 - Need of WMO extension?)
     + [2d] Extend logical mask syntax (implemented in 'build_condition_mask')
       > DEFINED for fields /= rundef (e.g. DEFINED(TOT_PREC))
       > support comparison between two fields (e.g. f1 < f2)
     + [2d] Automatic generation of short names and (internal) dictionary entries
       for unrecognized fields
     + [3d;request from waa] Transfer COSMO smoother() in fieldextra
     + [3d] Support bitmap for coding undefined values in GRIB 1
       (introduce global switch in &GlobalSettings to choose mode of undef coding;
        default value is bitmap)
     + [1w] [issue #49]
       New import / export format for temporary files
       > instead of GRIB
       > support for arbitrary list of points (list of locations)
       > implementation: Fortran unformatted files, each field & validation date
         coded using an extended ty_gb_field type
     + [jmb;1w;CORSO-A,DATA4WEB;to be evaluated] [issue #30] 
       Add support for combining location dependent height correction and lateral
       weighted average
     + [1w; request from Daniel] Consolidate support of gridded observations
       > add support for satellite and radar (meta-information, GRIB1/2)
       > correct setting in support_grib1:decode_product_origin and transcode_grib1_pds
         (MSG : NEW: pds(4) = 150 [satellite]
                     pds(2) = 252, pds(7) = 1 - 8
                                 OLD: pds(7), PDS(10)                pds(2) = 205
                                            4  1 SEVIRI 4 IR3.9
                                            4  2 SEVIRI 5 WV6.2
                                            4  3 SEVIRI 6 WV7.3
                                            4  4 SEVIRI 7 IR8.7
                                            4  5 SEVIRI 8 IR9.7
                                            4  6 SEVIRI 9 IR10.8
                                            4  7 SEVIRI 10 IR12.1
                                            4  8 SEVIRI 11 IR13.4
                     pds(41) = 1 - 4
                               1 cloudy brightness temperature
                               2 clear-sky brightness temperature
                               3 cloudy radiance
                               4 clear-sky radiance                     )
       > consistent values of model_name and model_type (namelist, resources ...)
       > distinct dictionary? if yes, automatic detection of dictionary (grins)
     + [2w] Consolidate horizontal re-gridding
       > consolidate user interface
         + same for in_regrid as for out_regrid
         + split *_regrid_method in *_regrid_method and *_regrid_method_source
         + add interface to support multiple methods, with applicability depending on field
       > consolidate weighted average and exp-weight by using a weighting function
         which is flat in the neighbourhood of the target grid point (to not over-
         emphasize the influence of the nearest grid point when interpolating from
         fine to coarse grid)
       > consolidate 'dominant' by using exactly the number of classes present in the
         source data set for building the histogram
       > introduce spline based interpolation (from coarse to fine)

###### Priority low

     + [2d] Add/consolidate support for
       > generalized height coordinates UUID (GRIB1) (?)
     + [1w] Support time granularity up to 1 second
       > currently one minute is the smallest granularity
       > useful for model output expressed in time steps


---------
##### User interface <a name="interface"></a>
---------

###### Priority high

     + [-> PERM] Improve clarity of diagnostic
     + [<1d] Check that iteration keywords are properly ordered in namelist
       (copy&paste easily leads to mixing iteration keywords) 
     + [2d] Evaluate merging cosmo & icon dictionaries
     + [3d] Consolidate and simplify usage of INCORE
       > check & improve diagnostics 
         (e.g. multi-time levels usage)
       > remove 'compare_with' and 'merge_with' operators (?)
         (can be replaced with poper=..., with less limitations than INCORE usage)
       > make computation of derived fields more intuitive (led)
       > propagate tags defined in INCORE
       > on-demand computation of derived field use the information of 'use_tag'
         when choice of parent is ambiguous (instead of reference_field)
       > make sure that get_incore_field() returns the correct HSURF field 
         (currently the selection is based on grid compatibility and unicity,
          but it is not automatic that the returned HSURF corresponds to the
          topography of the data being processed, e.g. when a different topo
          on the same grid is stored in INCORE)
      

###### Priority medium

     + [1d] Improve diagnostic on field size estimation (compare effective size to
       estimated size and issue a usage diagnostic when they differ; this
       makes diagnostic when using __ALL__ ... redundant)
     + [2d] Search algorithm used to associate geo. coordinates with grid points
       is not robust with respect to rounding errors (selected grid point may
       jump with infinitely small differences of geo. coordinates); such grid
       points should be flagged

###### Priority low

     + [1d] Diagnostic from field operator: use extend tag 'procedure [operator]: '
     + [3d] Make namelist and resources case independant (i.e. normalize all strings)


---------
##### Environment <a name="environment"></a>
---------

###### Priority high

     + [-> 13.2.0] [jmb/bap;2w] [issue #149]
       Continuous adaptation of operational environment
     + [-> 13.2.0] [1w;with support of CSCS/DWD] [issue #48]
       Additional compiler for regression suite (ifort, PGI ...)
     + [-> 13.2.0] [jmb/bap;4w] [issue #10]
       Consolidate regression suite
       > Add tests of fx tools
       > Only compare min, max, mean, std, #missing
         (use grins for GRIB 1, GRIB 2, _and_ NetCDF)
       > Large differences for non-significative values should be disgarded
         (in particular values which are almost 0)
       > Systematic test of all GRIB meta-information
       > Enforce strict testing of meta-information (no tolerance)
       > Reduce size of tests when meaningfull (but keep some large tests)
       > Integrate in new distributed environment (use Jenkins?)
       > Use / merge with COSMO model regression suite (?)
     + [-> 13.2.0] [jmb/APND;1w] [issue #159]
       > CMake based installation and packaging

###### Priority medium

     + [1d] Check in Makefile that compiler supports nested OMP (minimum release value)

###### Priority low


---------
##### fx tools <a name="fxtool"></a>
---------

###### Priority high

     + [2w] Re-write in Python
     + [3d] New fxcompare tool
     + [<1d] add option --record to fxfilter, to select a specific record
     + [1d] grins default dictionary should be dictionary_default.txt
     + Adapt all tools to use the same packing algorithm on output as on input
       (best solution is to add a fieldextra functionality for that, e.g.
        out_type_packing = "keep")

###### Priority medium

     + [5d] New tool to extract data from model output at specified locations
       and for specified fields (and ...), used within R to facilitate access
       to archived model data (see mail Andreas Pauling)
     + [2d] Improve grins
       > standard output: extend level information, format
       > evaluate current options, ask users for feedback
       > add option to produce a sorted TOC, using short name and level as keys
         (modify fxtr to print short name and level as 2 first values when this
          option is used, in wrapper script pipe fxtr output to sort
          -k1,1 -k2.1nb,2.5nb ... or something similar)
     + Adapt all tools to support ICON on native grid
       (problem: automatic access to NetCDF file describing the icosahedral grid)

###### Priority low

     + [3d] New fxderive [--epsmean | --epsmedian | --quantile --percentile=p]

---------
##### Documentation <a name="docu"></a>
---------

###### Priority high

     + [2w] Better integrate docu in GitHub (ask Carlos)
       > README.user integrated in GitHub with MarkDown language
       > cookbook integrated with active links to README.user for namelist keys

###### Priority medium

     + [4w] Consolidate / extend developer documentation

###### Priority low

     + [2w] Documentation in LaTeX
       > Write a tutorial (use intro ppt as basis, COSMO technical report)


---------
##### Administrative <a name="admin"></a>
---------

###### Priority high

     + [-> 13.2.0] [jmb;3d] [issue #12]
       Fieldextra life cycle
       > stakeholders: MeteoSwiss, COSMO (in particular DWD, USAM, ARPAE)
       > collect feedback from users 
       > evaluate software life cycle in view of future applications  
         (e.g. horizontal grid with O(10^7) points, new hardware architectures)
       > possible significant developments : code consolidation, full ICON support,
         IO optimisation, MPI parallelism, re-write of memory management...
       > do not forget to remove unused features
     + [-> 13.2.0] [jmb;3d] [issue #152]
       Fieldextra licensing issues 

###### Priority medium

###### Priority low
