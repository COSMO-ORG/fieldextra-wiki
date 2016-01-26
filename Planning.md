### Fieldextra planning, with priorities and assigned tasks

#### Release planning
* **v12.2.0** : February 2016
* **v12.3.0** : May 2016

#### Agreed milestones
* **2015Q2, COSMO PT CORSO-A & INCA & BAFU** : extended re-gridding operator (T2m)
* **2015Q3, Sinergia** : NetCDF on input

#### Code development
* [Bug corrections](#bug)
* [Code consolidation](#code_consolidation)
* [Code optimization](#code_optimization)
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

     + [1w] Check and debug multi-pass mode (or remove functionality)
       (nc_out and gb_out must be saved in cache)
       (compatibility with fieldextra.diagnostic extended mode)
       (add support for input files also used as output?)

###### Priority low



---------
##### Code consolidation <a name="code_consolidation"></a>
---------

###### Priority high

     + [-> 12.2.0] [bap;3d] Consolidated wind shear operators
       > surface boundaries specified as namelist arguments
       > in GRIB 2, only use short names WSHEAR_INT and WSHEAR_DIFF
       > consider using the same kernel in procedures wind_shear() and
         wind_shear_differential()
     + [-> 12.3.0] [jmb;3d] Add global memory monitoring of non instrumented libraries
       (currently icontool, rttov)
     + [1w] Consolidated usage of meta-information undef value
       > Define iundef (rundef) as the smallest integer (real) which can be represented
       > Always use iundef for 'undefined' (currently -1 is used in some cases);
         this requires for all meta-information: (1) consistent translation between
         GRIB and internal representation, in both directions, (2) for other format,
         translation from undef to output representation (backward compatibility!),
         (3) consistent translation from user defined value in internal representation.

###### Priority medium

     + [1d] Check internal coding of gamma angle for rotated lat/lon grids
     + [1d] Operator new_field_id: check that dictionary characteristics are compatible
       with actual properties of field (in particular tri and genproc_type), reset
       the field properties
     + [2d] Update imported COSMO modules
     + [2d] Clean-up copen_c.c, add compatibility with Mac OS X (statfs --> statvfs ?)
     + [2d] Split fxtr_operator_generic (e.g. level reduction or not, cache or not)
     + [1w] Re-organize ty_fld_origin and ty_fld_product
       > this is required to support observations
       > field_origin should contain all meta-information which is not essential
         for the correct behaviour of fieldextra - should be renamed to
         field_additional_minfo
       > field_additional_minfo should be splitted in data depending only on field
         and data depending on field and validation date
       > rename sc_pcat_{radar,satellite} to sc_pcat_obs 
         (use new 'class' to differentiate)
     + [1w] Systematically check and track fields units
     + [2-4w] Common library of services for COSMO software, which is efficient for all
       COSMO codes (incl. ICON) and which minimizes the number of modules to import
       for accessing a requested functionality
       > would replace the currently imported COSMO modules
       > see README.cosmo_api
       > see minutes of 18.12.2013 meeting
       > should be discussed at the COSMO TAG level

###### Priority low

     + [1d] Systematic check of staggering information in all operators
     + [1d] Check that all meta-information of a multi-levels field are
       consistent (e.g. units, origin ...); otherwise, vertical operators may
       lead to unpredictable meta-information values
     + [1d] Keep track of memory usage for all repositories used in fxtr_storage
     + [1d] Diagnostic from field operator: use extend tag 'procedure [operator]: '
     + [1d] Implement 'azimut_class' as a named operator (instead of poper)
     + [2d] Unified interface to decide when two fields represent the same quantity
       (used in field_compare, get_ic_field, get_fields, ...)
     + [2d] Introduce in fxtr_operator_support an operator to compute the scalar product
       of a vector field with the gradient of a scalar field; use it in
       geostrophic_wind() and  geostrophic_vorticity_advection()
     + [2d] Replace call to external C procedures with BIND (see mail Oli on 22.09.14)
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
     + [3d] INCORE storage
       > unified access to incore fields; curently access is provided by one of
         incore%*, incore_fields(:), get_incore_field(), get_incore_with_tag()
       > support multiple instances of special fields (HSURF ... -> multiple grids)
     + [1w] Replace POINTER with ALLOCATABLE in TYPE when possible (safer)
     + [1w] Review stack usage
       > Evaluate compiler options
         (e.g. Intel -heap-arrays, see http://software.intel.com/
         sites/products/documentation/doclib/stdxe/2013/composerxe/compiler/fortran-lin/
         GUID-0C5CC04A-C8CB-49BF-9382-03799AFD688B.htm)
       > Avoid silent stacksize overflow (could lead to invisible corrupted data)
     + [1w] GRIB 1 / GRIB 2 implementation
       > support_grib1 error handling similar to support_grib2 (more verbose)
       > write_grib1 error handling similar to write_grib2 (more verbose)
       > decoding of model type and model name in support_grib1 similar to
         support_grib2 (table based, use common table where possible)
       > externalize look-up table for model name
     + [2w] KIND specified for all REAL and all INTEGER, REAL explicitely set to
       32 bits (possible in fortran 2008), real_in_byte set accordingly

---------
##### Code optimization <a name="code_optimization"></a>
---------

 Remark about MeteoSwiss COSMO NExT system:
 * Both COSMO-1 (grid size 1158x774x80) and COSMO-E (grid size about 520x350x60x20) are
   a postprocessing challenge for time critical production: (1) data throughput between
   COSMO and fieldextra, (2) memory footprint, (3) stack usage, (4) time to solution.
 * Furthermore, the KENDA system presents additional challenges due to the large amount
   of simultaneous I/O. In this case, a ScaleMP solution could be beneficial.

###### Priority high

     + [-> 12.2.0] [jmb;3d] Improve flexibility in defining partition of threads between
       inner and outer loop, in order to improve the load balance of a mix of products
       containing a few expensive and many cheap products (e.g. RTTOV based products)
       > flag expensive products, use n_openmp_outerthread*n_openmp_innerthread
         threads in the inner loop for these expensive products, compute expensive
         products sequentially, at the outer loop level, before the other products
     + [3d] Code profiling of COSMO-NExT system
       > Find possible bottlenecks, define optimization approaches
     + [2-3w] I/O optimization, file based
       > Asynchronous read (read in advance)
       > Parallel read, in particular of partial model output

###### Priority medium

     + [3d] Optimize stab_lookup (search algorithm, create different storage classes)
     + [1w] Evaluate use of accelerator (GPU)
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
     + [1w] Evaluate implementation of additional MPI based parallelization
     + [1w] Avoid using strings as much as possible (tag component of ty_fld_id ...)
     + [1-2w;with CSCS support] Evaluation of ScaleMP software, to use multiple
       nodes as a single virtual shared memory system
       > more threads (but how is the scaling?)
       > relax the constraints on both available memory and memory throughput

###### Priority low

     + [with CSCS support] I/O optimization, im memory communication
       > Some possibilities :
         - In-memory communication using a NetCDF framework available at CSCS
           (require to implement NetCDF input, which would be a side benefit)
         - In-memory communication with ADIOS API (support: Jean Favre)
         - In-memory communication with OASIS coupler
         The first and second options are the prefered one, also because they are
         compatible with the Sinergia project where Oli is involved (resources from
         Sinergia could be available beginning 2015)
       > Evaluate potential performance gains
       > Evaluate ADIOS API
     + [1-2w] Memory footprint optimization :
       > Just on time output also when time operator is used
       > Optimize internal memory usage for reduction operators acting in the time
         dimension

---------
##### Feature consolidation <a name="feature_consolidation"></a>
---------

###### Priority high

     + [-> 12.3.0] [jmb;1w] Consolidate computation of SYNSAT products
       > see modifications in COMO release 5.3, procedure prepare_rttov_input()
     + [2d] Add/consolidate support for
       > multi-layers snow model (fxtr, GRIB1/2, NC)
       > coding of kilometric grids (GRIB2 - Need of WMO extension?)
     + [2w] Consolidate N_TUPLE, BLK_TABLE, XLS_TABLE, FLD_TABLE and DAT_TABLE
       > are FLD_TABLE / DAT_TABLE obsolete?
       > declare out_type_undefcode as string, to allow 'NaN', '' or other literal
         string to flag undefined values
       > uniform header (in addition: when present, vcoord on a single line)
       > missing value code in header
       > model name in header
       > correctly flag meta-information which is undefined
         (e.g. probability threshold high is reported as -1.E-6 in cvs ouput when not
          defined, which could be misleading)
       > re-evaluate meta-information (block keader, additional info with v=2)
       > format independent level information (do not use get_legacy_level())
       > support verbosity setting (or versioning)
       > split fxtr_write_generic:write_xls_table in two routines:
         (1) old atab
         (2) csv (but keep a single user interface).
       > optimize XLS_TABLE csv output (a factor of at least 2 can be achieved)
       > XLS_TABLE with all possible data mapping (?)
         (some clients wish for same format as observations)

###### Priority medium

     + [1d] Implement numberOfVgridUsed
       (old ivctype; set lowest_full_level and sc_vc_type_* ...)
     + [2d] Extend logical mask syntax (implemented in 'build_condition_mask')
       > DEFINED for fields /= rundef (e.g. DEFINED(TOT_PREC))
       > support comparison between two fields (e.g. f1 < f2)
     + [2d] Update algorithms to compute HPBL and BRN (according to COSMO)
     + [2d] Review gradient(), calc_sqrt(), and metric_coefs(); remove some restrictions
     + [2d] Add/consolidate support for
       > tiles (fxtr, GRIB1/2, NC)
       > coding of EPS perturbation (GRIB2)
       > coding of EPS quantiles difference (GRIB2)
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
     + [2w; support by F. Prill required] Consolidate ICON support
       >  integrate memory reservation and monitoring
       > introduce graceful exception handling in icon tools
         (currently exceptions detected in icon tools generate a program stop)
       > introduce integrated measurement of omp speedup
       > add information on grid point neighbourhood for global grid

###### Priority low

     + [1d] Add additional options for GRIB 2 packing algorithm
       (depending on ECMWF feedback, see SUP-1482)
     + [2d] Add/consolidate support for
       > coding of isentropic and pv surfaces (GRIB1) (?)
       > generalized height coordinates UUID (GRIB1) (?)
       > unstructured grid (GRIB1) (?)
     + [1w; request from SRNWP-I] Adaptor for SRNWP interoperability
       > Full tests and necessary modifications
       > Extend regression suite (see mail M.Bush)

---------
##### New features <a name="new_feature"></a>
---------

###### Priority high

     + [-> 12.2.0] [jmb;2d;COSMO PP CORSO-A,INCA,BAFU] Extend poper='hcor'
       > introduce multiple options
       > add model lapse rate as option
     + [-> 12.2.0] [jmb;3-5d] Implement RTTOV release 7 (besides release 11.2)
     + [-> 12.2.0] [jmb;3-5d;request from Daniel] Add support for ASCII input
       (to use fieldextra as a tool to create a GRIB or NetCDF file, with the correct
        meta-information)
       > based on BLK_TABLE format (with possible adaptation if needed...)
     + [-> 12.2.0] [jmb;2w;COSMO PP CORSO-A,INCA,BAFU] Re-gridding using 3D information
       > support vertical correction of field to interpolate before applying re-gridding
       > add weighted average based on generalized distance (dh aware)
       > special application to interpolate T2m on 'real' topography (BAFU)
       > PBL translation (INCA, TBD with Matteo)
     + [1d;bap;request from Christoph Spirig] Support ECMWF monthly and seasonal
       forecasts
       > new bi-linear interpolation algorithm for location_to_gridpoint, taking
         into account land-sea mask (Christoph Spirig will provide the algorithm)
     + [1w; request from led; for R&D, for seamless forecast, for radar verification]
       Graceful handling of missing input files in a temporal serie; option to fill
       gaps with constant values, with previous value (persistence), with
       interpolated values, or with undef (use temporal operator; set values,
       field_trange and field_origin). Also, do not raise an exception when some
       fields are missing for some time slots.

###### Priority medium

     + [1d;bap;request by Data for Web] EPS based geometric median
     + [3d;request from Lucio Torisi] Storm relative helicity
     + [3d;request from waa] Transfer COSMO smoother() in fieldextra
     + [3d;interest by O.Liechti] New output type for vertical profiles @ location
     + [2-4w;request form project Sinergia] Support NetCDF on input
         For processing of gridded observations (radar, satellite), for interoperability
         with other MCH groups, for EMPA, for in-memory communication (special NetCDF
         library available at CSCS), for R&D, for the CLM community... (but the StC has
         decided that fieldextra is not supported/available for the CLM community).
         EMPA would be interested using fieldextra for postprocessing of model output
         once this feature is available.
         Another usefull extension, easy to add once NetCDF is supported, would be the
         possibility to give to fieldextra a data array as a list of numbers with the
         associated meta-information.
       > define set of dimensions supported
       > define required / optional meta-information
       > follow CF standard

###### Priority low

     + [2d] Automatic generation of short names and (internal) dictionary entries
       for unrecognized fields
     + [2d] Make pressure@p-levels and height@h-levels automatically available when
       computing a new field; remove ad hoc solution in write_l2e.
     + [2d] Evaluate coupling of fieldextra with COSMO observation operators library
     + [3d] Support bitmap for coding/decoding undefined values in GRIB 1
       (introduce global switch in &GlobalSettings to choose mode of undef coding;
        default value is bitmap)
     + [1w] location oriented output based on interpolation of grid neighbours
       > keep all relevant neighbours in data store
       > add final data reduction
     + [2w] Consolidate horizontal re-gridding
       > mask to select source point (e.g. source has same characteristics as target)
       > consolidate weighted average and exp-weight by using a weighting function
         which is flat in the neighbourhood of the target grid point (to not over-
         emphasize the influence of the nearest grid point when interpolating from
         fine to coarse grid)
       > consolidate 'dominant' by using exactly the number of classes present in the
         source data set for building the histogram
       > introduce spline based interpolation (from coarse to fine)
     + [4-8w] Full support of unstructured ICON grid
       > adapt interface (e.g. imin, jmin)
       > remove any implicit assumption on regular grid
       > add support for vector fields (VN interpolation â~@¦)
       > add support for more fieldextra operators

---------
##### User interface <a name="interface"></a>
---------

###### Priority high

     + [-> PERM] Improve clarity of diagnostic
     + [2d;required by waa] Add mechanism to automatically create a set of location
       oriented output on the basis of a single namelist (use list of locations ana
       add a special keyword in the out_file name)

###### Priority medium

     + [1d] Consolidate usage of operators supporting multiple options for parent fields
       (an exception should be raised when the choice is ambigous, use_tag should
        be used to discriminate between different possibilities; see e.g. relhum)
     + [1d] Improve diagnostic on field size estimation (compare effective size to
       estimated size and issue a usage diagnostic when they differ; this
       makes diagnostic when using __ALL__ ... redundant)
     + [2d] Search algorithm used to associate geo. coordinates with grid points
       is not robust with respect to rounding errors (selected grid point may
       jump with infinitely small differences of geo. coordinates); such grid
       points should be flagged
     + [3d] Simplify usage of dictionaries
       > only allow default_dictionary and output_dictionary
       > evaluate merging gme / cosmo / icon dictionaries

###### Priority low

     + [2d;required by led] Evaluate possibility to simplify namelist in order to have
       easy access to incore derived fields (e.g. HHL)
     + [3d] Make namelist and resources case independant (i.e. normalize all strings)



---------
##### Environment <a name="environment"></a>
---------

###### Priority high

     + [ok 12.2.0] [jmb;<1d] Short name changes for compatibility with DWD:
       SNOW_% --> SNOW_PERCENT, CONV_% --> CONV_PERCENT
     + [ok 12.2.0] [jmb/bap;2w] Migration from SVN to Git / GitHub
       > Learn tools
       > Define policy
       > Automatic generation of Git revision in fxtr_main
     + [-> 12.3.0] [jmb/bap;2w] Consolidate regression suite
       > Add tests of fx tools
       > Only compare min, max, mean, std, #missing
       > Large differences for non-significative values should be disgarded
         (in particular values which are almost 0)
       > Systematic test of all GRIB meta-information
       > Enforce strict testing of meta-information (no tolerance)
       > Reduce size of tests when meaningfull (but keep some large tests)
       > Integrate in new distributed environment (use Jenkins?)
       > Use / merge with COSMO model regression suite (?)
     + [2w;with support of CSCS] Additional compilator (ifort ...)

###### Priority medium

     + [<1d] Add fieldextra admin documents to version control system
     + [1d] Check in Makefile that compiler supports nested OMP (minimum release value)
     + [1d] Consolidate the information on the release of the different pieces of code
       (introduce RELEASE file containg target release tag, use this information in
       setup/setup_environment.bash, tools/create_distribution_package, fxtr_main.f90;
       included release information in fxtr_main during build process ...)

###### Priority low

     + [2d] Setup script to build the GRIB API definition files for a specified
       local center, using COSMO definition files as template



---------
##### fx tools <a name="fxtool"></a>
---------

###### Priority high

     + [<1d] add option --record to fxfilter, to select a specific record
     + [1d] grins default dictionary should be dictionary_default.txt
     + [3d] New fxcompare tool
       > consolidate support of time loop in relation with INCORE output
       > consolidate 'compare_with' operator, in particular to compare fields
         at the same validation times
     + [5d] New tool to extract data from model output at specified locations
       and for specified fields (and ...), used within R to facilitate access
       to archived model data (see mail Andreas Pauling)

###### Priority medium

     + [2d] Improve grins
       > standard output: expend level information, add EPS information, format
       > evaluate current options, ask users for feedback
       > add option to produce a sorted TOC, using short name and level as keys
         (modify fxtr to print short name and level as 2 first values when this
          option is used, in wrapper script pipe fxtr output to sort
          -k1,1 -k2.1nb,2.5nb ... or something similar)
     + [2w] Re-write in Python

###### Priority low

     + [3d] New fxderive [--epsmean | --epsmedian | --quantile --percentile=p]
       > add possibility to keep the list of short names when deriving the
         file content with grins

---------
##### Documentation <a name="docu"></a>
---------

###### Priority high

     + [ok 12.2.0] [jmb;1d] COSMO web documentation:
       > Integrate planning in the framework provided by the COSMO web pages
         (could be a link to some other pages, or at least an e-mail address)
       > Update fieldextra specific page
     + [-> 12.3.0] [jmb/bap;2w] Provide more problem based solutions
       > extend cookbook (monitoring product ...)
       > reference file describing as many applications as possible, with keywords
         and link to namelists
     + [-> 12.3.0] [jmb/bap;2-3w] Common information platform, based on GitHub and
       other tools, with, in particular, support for code review, bug tracking, feature
       request, roadmap and history

###### Priority medium

     + [4w] Consolidate / extend developer documentation

###### Priority low

     + [<1d] Update and merge PLATFORMS and USERS documents
     + [2w] Documentation in LaTeX
     > Write a tutorial (use intro ppt as basis, COSMO technical report)
     > README.user as full feature document (COSMO Part VI documentation)


---------
##### Administrative <a name="admin"></a>
---------

###### Priority high

     + [-> 12.3.0] Planning beyond 12.3.0
       > feedback from users : APN, CRS, H.Asensio, M.Denhard ...
       > possible significant developments : code consolidation, code optimization,
         NetCDF input, full ICON support
       NOTE: wish from waa to focus on input optimization

###### Priority medium

###### Priority low



