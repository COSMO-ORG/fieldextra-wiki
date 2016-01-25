### Fieldextra planning, with priorities and assigned tasks

#### Table of content
* [Release planning](#release)
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


---
##### Release planning <a name="release"></a>
---

* **v12.2.0** : February 2016
* **v12.3.0** : May 2016

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
     + [-> 12.2.0] [jmb;3d] Add global memory monitoring of non instrumented libraries
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
       > rename sc_pcat_{radar,satellite} to sc_pcat_obs (use new 'class' to differentiate)
     + [1w] Systematically check and track fields units
     + [2-4w] Common library of services for COSMO software, which is efficient for all
       COSMO codes (incl. ICON) and which minimizes the number of modules to import
       for accessing a requested functionality
       > would replace the currently imported COSMO modules
       > see README.cosmo_api
       > see minutes of 18.12.2013 meeting
       > should be discussed at the COSMO TAG level

