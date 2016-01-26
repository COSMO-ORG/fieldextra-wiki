__See file admin/HISTORY for a detailed list!__

### v12.0.0
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


### v12.1.0
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
