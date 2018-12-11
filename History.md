_See file admin/HISTORY for a detailed list!_

### All releases
* [v11.0.0](#v11.0.0), [v11.0.1](#v11.0.1), [v11.0.2](#v11.0.2)
* [v11.1.0](#v11.1.0)
* [v11.2.0](#v11.2.0)
* [v11.3.0](#v11.3.0), [v11.3.1](#v11.3.1), [v11.3.2](#v11.3.2), [v11.3.3](#v11.3.3)
* [v12.0.0](#v12.0.0)
* [v12.1.0](#v12.1.0)
* [v12.2.0](#v12.2.0)
* [v12.3.0](#v12.3.0), [v12.3.1](#v12.3.1), [v12.3.2](#v12.3.2)
* [v12.4.0](#v12.4.0)
* [v12.5.0](#v12.5.0)
* [v12.6.0](#v12.6.0)
<<<<<<< HEAD
* [v12.7.0](#v12.7.0), [v12.7.1](#v12.7.1), [v12.7.2](#v12.7.2), [v12.7.3](#v12.7.3), [v12.7.4](#v12.7.4)
* [v12.8.0](#v12.8.0)
=======
* [v12.7.0](#v12.7.0), [v12.7.1](#v12.7.1), [v12.7.2](#v12.7.2), [v12.7.3](#v12.7.3), [v12.7.4](#v12.7.4), [v12.7.5](#v12.7.5)
>>>>>>> 01e5fed73a673a31584480163e04ce7d10c3074b

<a name="v11.0.0"></a>
### v11.0.0, released on 21.12.2012 
* Extend support for COSMO-LEPS products  
  (e.g. control run)
* Introduce support for SLEVE 2 vertical coordinates
* Introduce support for MOS corrections, extend support for Kalman corrections
* New operator to create a field by cloning the meta-information of a specified field
* New operator to compute relative humidity over mixed phase (RH_MIX)
* New operators to compute components, direction, and speed of geostrophic
  wind on model surfaces (U_G, V_G, FF_G, DD_G)
* New operators to compute absolute and relative geostrophic vorticity on
  model surfaces (RELV_G, ABSV_G)
* New operators to compute absolute and relative geostrophic vorticity
  advection on model surfaces (RELV_ADV_G, ABSV_ADV_G)
* New operator to compute geostrophic thickness advection (THICK_ADV_G)
* New operator to compute 3d wind field divergence on model surfaces (WDIV)
* New operator to compute horizontal moisture flux convergence on model surfaces (MCONV)
* New operator 'set_packing' to define field specific packing method
* New operator 'integ_sfc2h' to integrate from the surface to a specified height above ground
* New operator 'intpl_k2theta' to interpolate from model to potential temperature surfaces
* New operator 'intpl_k2pv' to interpolate from model to potential vorticity surfaces
* New operator 'destagger' to interpolate a staggered field to mass points
  (the previous method based on the generic interpolation operator was limited in scope)
* Implement NetCDF output, CF1.6 compliant
* User interface improvement: re-gridding operator
* User interface improvement: meta-information in region files
* Optimization: memory footprint, to support large values of mx_field  
  (now set to 15000)
* Optimization: performance analysis
* Optimization: critical algorithms, some ASCII output
* Optimization: shared memory parallelism, implemented with OpenMP
* Environment: improved library configuration scripts, improved Makefiles
* Environment: extended and consolidated test environment at CSCS
* Environment: add support for INTEL compiler
* Documentation: new FAQ, new cookbook, improved README.install

<a name="v11.0.1"></a>
### v11.0.1, released on 15.02.2013 
* More robust parsing of namelist : skip product whose definition is incorrect,
  instead of exiting at the first exception detected in the namelist
* Code optimization for cases where a large number of input files are processed
* Code clean-up and bug corrections  
  (in particular to support IBM compiler)


<a name="v11.0.2"></a>
### v11.0.2, released on 22.04.2013 
* Support model names with originating center set to Zurich and ARPA-SIMC
* Support of ECMWF seasonal forecasts, incl. monthly mean
* Support screening of input file using eps member, lead time, or validation date
* New switch out_type_novcoord to skip coding vcoord information in GRIB output
* Replace grins functionality with fieldextra and a wrapper script
* Environment: version control of full environment 
  (with SVN at CSCS)
* Environment: clone of master installation at ECMWF  
  (rsync script)
* Environment: add support for IBM compiler
* Bug corrections


<a name="v11.1.0"></a>
### v11.1.0, released on 31.05.2013 
* Complete re-factoring of DWD GRIB API
* Support model names with originating center set to Moscow
* More robust support of unregistred COSMO centers
* Support lagged and other types of composed EPS
* Support missing EPS members
* Support selection of EPS members
* Define and implement default GRIB 1 coding for COSMO EPS
* New operator to compute EPS perturbation  
  (i.e. member - mean)
* Add preliminary support of GRIB 2 generalized vertical coordinates
* Tools: implement support of GRIB 2 in grins
* Tools: new utilities using fieldextra as computation engine  
  (fxconvert, fxcrop)
* Environment: add standard Ubuntu and Redhat platforms in test procedure
* Environment: add DWD FABEC tests in standard test environment
* Environment: add support of SUN platform in install scripts and Makefile
* Environment: link with GRIB API 1.10.4
* Environment: consolidate dictionaries information
* Bug corrections  
  (in particular quantile computation)


<a name="v11.2.0"></a>
### v11.2.0, released on 20.11.2013 
* Consolidate fxconvert  
  (NetCDF target and duplicate field names in input file)
* Simplify usage of dictionaries  
  (EPS derived and neighbourhood probability)
* Finalize support of GRIB 2 generalized vertical coordinates  
  (with GRIB API 1.11; see remark about usage in HISTORY)
* Usage of in_grid to crop fields now also supports staggered fields
* Product subdomain can now be specified in lat/lon, independently of the grid
* New operator to compute height of zero degree  
  (with option to extrapolate below topo)
* New operators to transform precipitation amount in fresh snow density and depth
* Use corrected estimator to compute EPS standard deviation  
  (factor 1/(n-1) instead of 1/n)
* Consolidate code  
  (compilation warning, horizontal grid operators)
* Bug corrections and many other improvements (see HISTORY)


<a name="v11.3.0"></a>
### v11.3.0, released on 14.03.2014 
* Remove limitation on domain subset usage for operators requiring a lateral halo
* Extend support of MOS estimator  
  (gridded, logistic regression ...)
* New operator to compute vertical derivative
* New operator to define an arbitrary surface, specified by a condition on some field
* New operator to compute a vertical integral between two arbitrary surfaces
* Consolidated GRIB 2 support  
  (tablesVersion, localTablesVersion ...)
* Many other operators improvements (see HISTORY)
* User interface and diagnostic improvements (see HISTORY)
* Bug corrections, internal code improvements, code optimization (see HISTORY)


<a name="v11.3.1"></a>
### v11.3.1, released on 15.04.2014 
* Remove limitation on domain subset usage for operators requiring a lateral halo
* Extend support of MOS estimator  
  (gridded, logistic regression ...)
* New operator to compute vertical derivative
* New operator to define an arbitrary surface, specified by a condition on some field
* New operator to compute a vertical integral between two arbitrary surfaces
* Consolidated GRIB 2 support  
  (tablesVersion, localTablesVersion ...)
* Many other operators improvements (see HISTORY)
* User interface and diagnostic improvements (see HISTORY)
* Bug corrections, internal code improvements, code optimization (see HISTORY)


<a name="v11.3.2"></a>
### v11.3.2, released on 25.06.2014 
* Option to specify vertical_coordinate_coef in &ModelSpecificatons
* Add support of COSMO nudgecast and IFS-ENS mean
* Add support of compact coding of GRIB constant fields
* Improve support of multiple grib_definition_path
* New operator to compute EPS normalized standard deviation
* Optimization of memory usage and of outer loop parallelism
* Some diagnostic improvements
* Bug corrections, miscelleanous (see HISTORY)


<a name="v11.3.3"></a>
### v11.3.3, released on 01.09.2014 
* Improve interface for re-gridding input fields
* Support field specific re-gridding method
* Support native ICON grid on input - limited implementation
* New operator set_vcoord in &Process to re-set vertical coordinates info
* New operator PP to compute pressure deviation, using P as parent field
  (COSMO model)
* Support complex logical masks in 'merge_with' and 'compare_with'
* Consolidate NetCDF output
  (relax some restrictions on multi-level fields)
* New fxfilter tool
* Bug corrections, miscelleanous (see HISTORY)


<a name="v12.0.0"></a>
### v12.0.0, released on 01.03.2015 
*Products*
* Consolidate support of ICON grid  
  (missing values, kilometric grids, diagnostic)
* Full support of synthetic satellite fields  
  (GRIB1, GRIB2, NetCDF)
* Support setting generalized height based coordinates characteristics through namelist
  (allows hybrid to generalized height based coordinates transformation)

*Operators*
* Support EPS member to member transformation using a global EPS operator
  (e.g. reset EPS by adding a more recent determinist forecast to each EPS perturbation)
* Improve 'fill_undef' operator to have a more targeted effect
  (ignore regions where too many undef are present)
* Operators 'merge_with' and 'compare_with' can now act on a collection of fields
  (e.g. transformation of all members of an EPS system)
* New operator to find the surface where a 3D field reaches its maximum or minimum
  (condition is eveluated independently in each column)
* Possibility to interpolate to an arbitrary surface
* New operator to compute an arbitrary sum of fields
* New operator to compute the (differential) vertical wind shear
* New operators to compute the divergence and the deformation of the horizontal wind
* New operator to compute the Gradient Richardson number RI
* New operators to compute CAT indices
* Preliminary support to compute synthetic satellite fields  
  (RTTOV lib)

*Optimization*
* Support duplicated records in input data
  (memory optimization for output not compatible with just on time, using a temp GRIB)
* New variable out_production_granularity in &RunSpecification
  (control balance bewteen memory footprint and OMP speedup)
* Memory and compute time optimization for large problems

*Resources, tools*
* Change format of dictionary  
  (use key/value pair)
* New fxclone tool
* New dictionary_mill tools  
  (convert between GRIB API def files and dictionary)

*Others*
* New 'debug' value of verbosity in &RunSpecification
* Extend information in fieldextra.diagnostic when additional_diagnostic=.true.
* Bug corrections, miscelleanous (see HISTORY)


<a name="v12.1.0"></a>
### v12.1.0, released on 10.12.2015 
*Products*
* Full support of GRIB 2 local use section  
  (Offenbach, Zurich, COSMO-LEPS, COSMO default)
* Full support of GRIB 2 chemical constituents
* Further consolidation and extension of GRIB 2 support   
  (Full compatibility with ICON and COSMO-EU tested)

*Operators*
* Definitive implementation of synthetic satellite operators  
  (based on RTTOV 11.2.0)
* New operator to compute Eddy Dissipation Parameter EDP  
  (ICON only)
* New operator to compute WSHEAR_0-1km
* Improved computation of fresh snow density RHO_SNOW
* New poper 'regsum'
* New poper 'product' and 'quotient'
* Add field related condition to  poper 'create_cond' and 'replace_cod'  
  (support complex merge of multiple fields)
* New voper 'max_sfc2h', 'max_h2h' ...  
  (extremum in a column)
* New voper 'find_nonzero'
* New voper 'find_condition'  
  (e.g. to define tropopause using condition on T, P)
* New voper 'intpl_k2khalf'
* Add mode='nearest' to voper interpolation
* New hoper 'offset_to_reference'  
  (offset with respect to a fix point)
* New hoper 'sqcnt%', 'tiled_sqcnt%', and 'diskcnt%'  
  (frequency of active points)
* New 'data_type' in &GlobalSettings
* All local keys, and some more keys, can be reset by user  
  (see 'auxiliary_metainfo', 'out_auxiliary_metainfo', 'set_auxiliary_metainfo')

*Interface*
* Possibility to reset the default values of out_type_* and out_mode_*
* Improved some namelist naming conventions

*Resources, tools*
* Upgrade GRIB API environment to be compatible with COSMO GRIB 2 policy  
  (unique set of def files for all COSMO software and all COSMO members)
* Upgrade dictionaries to be compatible with COSMO GRIB 2 policy  
  (common GRIB API package for all COSMO software and all COSMO members)
* Upgrade fx tools  
  (e.g. support duplicated records in input, grins --summary)

*Optimization*
* New 'in_read_order' in &Process  
  (memory optimisation)

*Internal*
* Surface values (e.g. levlist) are now associated with their physical units
* More complete coding of time range information

*Others*
* New 'stop_flag' in &RunSpecification
* New 'out_filter_time' to filter the output data in the time dimension
* Improved N_TUPLE output type
* Improved README.user
* Improved diagnostic
* Bug corrections, miscelleanous (see HISTORY)


<a name="v12.2.0"></a>
### v12.2.0, released on 31.03.2016 
*Products*
* Add support for ASCII input  
  (based on BLK_TABLE format)
* Add support for EPS probability defined with respect to a reference distribution  
  (IFS products)
* Introduce some improvements for BLK_TABLE format

*Operators*
* New way to compute location dependent height correction  
  (development within COSMO PT CORSO-A)
* Improve support of field upscaling
* Improve support for re-setting field meta-information

*Optimization*
* Improve flexibility in defining partition of threads between inner and outer loop
* Optimize RTTOV complex clouds computation

*Others*
* Exception by input file processing is now non fatal
* Bug corrections, miscelleanous (see HISTORY)


<a name="v12.3.0"></a>
### v12.3.0, released on 09.06.2016 
*Operators*
* Point operator can be iterated up to 3 times in each processing iteration
* New poper hshift_loc, inverse, ratio, logarithm_e, exponential, polynomial
* New voper upscale_grad_min, upscale_grad_max, intpl_k2h++, intpl_k2z++  
  (support extrapolation, support definition of target levels through external field)
* New hoper regfit  
  (linear regression within geographical regions)
* New operator to compute TD_2M using T_2M, QV_2M and PS
* Projection of model output on a different topography is now possible  
  (fieldextra is used at MCH to interpolate COSMO-1 on INCA grid,
   where INCA is the nowcasting tool from ZAMG)

*Optimization*
* Extend and optimize cache usage
* Optimize arrays initialization
* Optimize detection of input file type
* Extend functionality of intermediate files  
  (supports namelist optimization)
* Automatic data reduction when out_regrid_target is specified
* Level of diagnostic and profiling can now be controled separately

*Others*
* Workaround for unreliable time to solution when initializing large arrays
* Additional tips on namelist optimization in README.user
* See HISTORY for a full list of modifications


<a name="v12.3.1"></a>
### v12.3.1, released on 12.08.2016 
*Products*
* Add support to process INCA input   
  (in GRIB)
* Add support to generate LAGRANTO compatible output   
  (in NetCDF)

*Operators*
* Update EDP   
  (processing of ICON model)   
* New OMEGA_SLOPE   
  (to use fieldextra as FLEXPAR pre-processor)   

*Others*
* Consolidate regression suite
* Correct critical bug introduced in 12.3.0
* Other bug corrections (see HISTORY)


<a name="v12.3.2"></a>
### v12.3.2, released on 06.10.2016 
*Products*
* Support 'levelOfMaxWind' and 'typeOfEnsembleForecast'
* Support NetCDF standard name for hybrid sigma pressure

*Operators*
* Consolidate tiled operators (upscaling)
* New 'skip_time' and 'toper_mask' (recommended alternative to 'ignore_tri')
* Extends 'sum', 'inverse' and 'ratio' to support an additional constant term

*Others*
* fx tools : add support for input with mixed GRIB and with discontinuous layers
* Correction of a critical bug : make constant field attribute a dynamic feature   
  (e.g. computation of tropopause height incorrectly produced a constant field)
* Other correction of bugs


<a name="v12.4.0"></a>
### v12.4.0, released on 02.12.2016 
*Products*
* Correct some GRIB 1 and GRIB 2 codes

*Operators*
* New operator to compute the 2d advection of an arbitrary tracer
* Extend out_shuffle_time to filter input fields on the basis of the reference year
* Automatic generation of HEIGHT on full levels when HEIGHT on half levels is present

*Optimization*
* Optimization of collect field step (in particular for EPS)

*Others*
* __Externalize & consolidate GRIB API environment (-> common COSMO environment)__
* Consolidate versioning of external resources
* Consolidate installation step (documentation, setup scripts)
* Correction of bugs and miscelleanous consolidations


<a name="v12.5.0"></a>
### v12.5.0, released on 21.02.2017 
*Products*
* Improve compatibility with GRIB 2 generated by COSMO model
* Improve compatibility with GRIB 2 generated by IFS model

*Operators*
* Support unconnected geographical regions
* Support any probability interval
* Operator to compute non normalized vertical integral
* Operator to compute QV at saturation
* Operator to use GRIB 1 alternate coding
* Additional functionality of operator 'out_filter_time'
* Global output definition for product category, vertical coordinates and model name

*Others*
* Miscelleanous code consolidations  
  (BLK_TABLE as input, n2geog cache, GRIB 2 local table version, field level...)
* Correction of bugs


<a name="v12.6.0"></a>
### v12.6.0, released on 25.07.2017 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Products*
* Update ECMWF GRIB API to 1.20.0, add support for adaptative entropy coding
* Add GRIB API definitions for pollen and COSMO 5.05+
* Add GRIB 2 support for interquantile range
* Add GRIB 1 support for ECMWF EFI and for ECMWF large data set
* Add GRIB 1 support for isentropic and PV surfaces
* Add support of isothermal surfaces
* Add support of COSMO-IT-EPS
* Add option to produce simplified NetCDF output
* More consistent setting of constant fields meta-data    
  (now also controlled by out_type_mapcst)

*Operators*
* New operator to compute maximum and relative sunshine duration   
  (with or without horizon effect)
* Consolidate thermodynamic operators   
  (wet bulb temperature, quantities computed at 2m above ground...)
* Add support for cloning missing input files   
* Add support for interpolation in the time dimension   
  (new toper "fill_undef")
* Consolidate support for processing inhomogeneous data in the time dimension   
  (remove ignore_tri, introduce keep_time...)
* Consolidate implementation of toper   
  (handling of undefined, meta-data of transformed field...)
* New toper "mask_all"   
  (together with toper_mask supports selective setting of undefined values)
* Consolidate implementation of new_field_id   
  (merge with pre-existing field, extended meta-data resetting)
* Extend functionality of set_leadtime, set_time_range and set_level_property

*Others*
* Extend grins default output
* Consolidation of dictionary usage   
  (short name derivation, complement missing meta-data with dictionary information)
* Consolidate user interface   
  (namelist parsing, ambiguous definition of parents...)
* Consolidate and extend README.user
* Correction of bugs


<a name="v12.7.0"></a>
### v12.7.0, released on 02.02.2018 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Products*
* New subset type 'frame of width frame_ngp'
* New subset type 'vertical slice'
* Full support of originating center 'Roma'
* Add support for cosmo_d2 and cosmo_d2-eps
* Add support for ART products
* Add support of surface types 'mass density exceeds the specified value'
* Consolidate GRIB 1 support (bitmap, some field short names)

*Operators*
* New toper 'date_of_min', 'date_of_max', 'value_at_date'
* New poper 'stretch'
* New mode 'linear in height' for some vertical interpolation operator
* Vertical operators 'find_*' may be restricted to any user specified interval 

*Others*
* New enable_exit_status to control exit process status
* Improve OpenMP performances
  (in particular parallel execution of toper, poper, voper)
* Code consolidation
  (domain subset processing, no more limitations on number of locations,
  more robust implementation of out_type_mapcst, remove tmp1_dictionary...
  and tmp1_gp_partitioning..., and more)
* Correction of bugs
  (in particular generation of file name when multiple time stamps are used)


<a name="v12.7.1"></a>
### v12.7.1, released on 09.05.2018 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Products*
* Recognize icon-eps
* Consolidate some ASCII output (XLS_TABLE, DAT_TABLE, and FLD_TABLE)

*Operators*
* Point operator can now be iterated up to 5 times (poper ... poper5)
* Modified behaviour of set_vcoord

*Others*
* New fxdestagger
* Consolidated and extended cookbook
* Update documentation
* Correction of bugs    
  (in particular related to vertical operator in product using multiple EPS members)


<a name="v12.7.2"></a>
### v12.7.2, released on 23.05.2018 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Products*
*Others*
* Correction of bugs    
  (GRIB2 : correct coding of scaleFactorOfLowerLimit=-1 and scaleFactorOfUpperLimit=-1)


<a name="v12.7.3"></a>
### v12.7.3, released on 02.07.2018 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Others*
* Increases maximum allowed number of in_file/out_file pairs


<a name="v12.7.4"></a>
### v12.7.4, released on 23.07.2018 

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Others*
* The "stop_flag" now immediately interrupts any wait loop


<a name="v12.7.5"></a>
### v12.7.5, released on 27.09.2018 
>>>>>>> 01e5fed73a673a31584480163e04ce7d10c3074b

**ATTENTION: poor OpenMP performances of GRIB API 1.20.0, resulting in**
**poor performances of GRIB 2 parallel write (ECMWF ticket SUP-2089).**

*Others*
* Increases maximum allowed number of input files


<a name="v12.8.0"></a>
### v12.8.0, released on 14.12.2018 
*Products*
* Add NetCDF import
* Consolidate NetCDF export
* Add support of EPS spread products
* Add support of non standard use of GRIB 2 second surface (e.g. COSMO HHL)

*Operators*
* Extend lateral re-gridding     
  (generalized distance, filter set of contributing grid points)
* New operators for QV_SAT and QV_SAT_2M
* New operator to compute the maximum or the minimum of two fields
* New operator to project a field on a staggered grid

*Tools*
* All fx tools compatible with NetCDF / GRIB 1 / GRIB 2 / BLK_TABLE    
  (option -N cosmo | -N extpar required when working with NetCDF)
* New grins --ntuple
* New grins --toc

*Documentation*
* New cookbook   
  (with 55 commented cases)
* Improve some diagnostics

*Others*
* Reduce binary size    
  (now 2/3 smaller)
* Some code re-structuration    
  (support_gis, support_vertical_mesh, fxtr_write_obsolete)
* Correction of bugs
