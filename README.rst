# Yuan's notes
- This repository is from Keer Zhang's work, developing transient urban with revised code for mksurfdata_map.

## Port
### Archer2
```
export CESM_ROOT=/work/n02/n02/yuansun/cesm
module load cray-python/3.9.13.1
cd /work/n02/n02/yuansun/cesm
source /work/n02/n02/yuansun/shell_scripts/archive/port_cesm/setup/setup_ctsm50.sh -p /work/n02/n02/$USER/cesm -l my_cesm_sandbox_CLM50_mksurfdata
cd /work/n02/n02/yuansun/cesm/my_cesm_sandbox_CLM50_mksurfdata

# modify Externals.cfg
local_path = cime
protocol = git
repo_url = https://github.com/ESMCI/cime
tag = cime5.6.28
required = True

./manage_externals/checkout_externals
```


## mksurfdata_map
```
module load PrgEnv-gnu
module load cray-hdf5-parallel
module load cray-netcdf-hdf5parallel
module load cray-parallel-netcdf

export NETCDF=/opt/cray/pe/netcdf-hdf5parallel/4.9.0.1/gnu/9.1
export LIB_NETCDF="${NETCDF}/lib"
export INC_NETCDF="${NETCDF}/include"
export USER_FC=gfortran
export USER_CC=gcc
export USER_FFLAGS="-fallow-argument-mismatch -fallow-invalid-boz -Wno-error"
export USER_CPPDEFS="-I${NETCDF}/include"
export USER_LDLAGS="-L${NETCDF}/lib -lnetcdff -lnetcdf"

LDFLAGS="-L$NETCDF/lib -lnetcdff -lnetcdf"
CPPFLAGS="-I$NETCDF/include"

export LD_LIBRARY_PATH=$NETCDF/lib:$LD_LIBRARY_PATH
export PATH=$NETCDF/bin:$PATH
export CESM=/work/n02/n02/yuansun/cesm
export TOOL=${CESM}/my_cesm_sandbox_CLM50_mksurfdata/tools
cd ${TOOL}/mksurfdata_map/src
# modify Makefile.common
gmake USER_FC=gfortran USER_CC=gcc USER_FFLAGS="-fallow-argument-mismatch -fallow-invalid-boz -Wno-error" LIB_NETCDF="${NETCDF}/lib" INC_NETCDF="${NETCDF}/include" USER_CPPDEFS="-I${NETCDF}/include" USER_LDLAGS="-L${NETCDF}/lib -lnetcdff -lnetcdf"         

# gmake clean
# if gmake fails, use 'gmake clean' to clean up the object files and then recompile

# after gmake, the execuatble file is in ${TOOL}/mksurfdata_map 
cd ${TOOL}/mkmapdata
export GRID=${CESM}/cesm_inputdata/lnd/clm2/mappingdata/maps/0.9x1.25/map_0.25x0.25_nomask_to_0.9x1.25_nomask_aave_da_c200309.nc
./mkmapdata.sh -f ${GRID} -res <res> -type global
```


====
CTSM
====

The Community Terrestrial Systems Model.

This includes the Community Land Model (CLM5.0 and CLM4.5) of the Community Earth System Model.

For documentation, quick start, diagnostics, model output and
references, see

http://www.cesm.ucar.edu/models/cesm2.0/land/

and

https://escomp.github.io/ctsm-docs/

For help with how to work with CTSM in git, see

https://github.com/ESCOMP/ctsm/wiki/Getting-started-with-CTSM-in-git

and

https://github.com/ESCOMP/ctsm/wiki/Recommended-git-setup

To get updates on CTSM tags and important notes on CTSM developments
join our low traffic email list:

https://groups.google.com/a/ucar.edu/forum/#!forum/ctsm-dev

(Send email to ctsm-software@ucar.edu if you have problems with any of this)
