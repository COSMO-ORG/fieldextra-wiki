__See file admin/HISTORY for a detailed list!__

### Releases
* [v11.0.0](#v11.0.0), [v11.0.1](#v11.0.1), [v11.0.2](#v11.0.2)
* [v11.1.0](#v11.1.0)
* [v11.2.0](#v11.2.0)
* [v11.3.0](#v11.3.0), [v11.3.1](#v11.3.1), [v11.3.2](#v11.3.2), [v11.3.3](#v11.3.3)
* [v12.0.0](#v12.0.0)
* [v12.1.0](#v12.1.0)

### v11.0.0, released on 21.12.2012 <a name="v11.0.0"></a>
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


### v11.0.1, released on 15.02.2013 <a name="v11.0.1"></a>
* More robust parsing of namelist : skip product whose definition is incorrect,
  instead of exiting at the first exception detected in the namelist
* Code optimization for cases where a large number of input files are processed
* Code clean-up and bug corrections  
  (in particular to support IBM compiler)


### v11.0.2, released on 22.04.2013 <a name="v11.0.2"></a>
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


### v11.1.0, released on 31.05.2013 <a name="v11.1.0"></a>
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


### v11.2.0, released on 20.11.2013 <a name="v11.2.0"></a>
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


### v11.3.0, released on 14.03.2014 <a name="v11.3.0"></a>
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


### v11.3.1, released on 15.04.2014 <a name="v11.3.1"></a>
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


### v11.3.2, released on 25.06.2014 <a name="v11.3.2"></a>
* Option to specify vertical_coordinate_coef in &ModelSpecificatons
* Add support of COSMO nudgecast and IFS-ENS mean
* Add support of compact coding of GRIB constant fields
* Improve support of multiple grib_definition_path
* New operator to compute EPS normalized standard deviation
* Optimization of memory usage and of outer loop parallelism
* Some diagnostic improvements
* Bug corrections, miscelleanous (see HISTORY)


### v11.3.3, released on 01.09.2014 <a name="v11.3.3"></a>
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


### v12.0.0, released on 01.03.2015 <a name="v12.0.0"></a>
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


### v12.1.0, released on 10.12.2015 <a name="v12.1.0"></a>
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