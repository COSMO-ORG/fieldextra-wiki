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

