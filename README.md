# Yuan's notes
- This repository is from Keer Zhang's work, developing transient urban with revised code for mksurfdata_map.

## Port
### Archer2
```
export CESM_ROOT=/work/n02/n02/yuansun/cesm
module load cray-python/3.9.13.1
cd ${CESM_ROOT}
source /work/n02/n02/yuansun/shell_scripts/archive/port_cesm/setup/setup_ctsm50.sh -p ${CESM_ROOT} -l my_cesm_sandbox_clm50_mksurfdata
cd ${CESM_ROOT}/my_cesm_sandbox_clm50_mksurfdata

# modify Externals.cfg
tag = maint-5.6
protocol = git
repo_url = https://github.com/ESMCI/cime
local_path = cime
required = True

./manage_externals/checkout_externals
```

### Error

**Error1**: yuansun@ln04:/work/n02/n02/yuansun/cesm/my_cesm_sandbox_clm50_mksurfdata> ./manage_externals/checkout_externals
Processing externals description file : Externals.cfg
Processing externals description file : Externals_CLM.cfg
Checking status of externals: clm, fates, dictionary keys changed during iteration

**solved**: [ref](https://bb.cgd.ucar.edu/cesm/threads/known-issue-running-manage_externals-with-python-3-8-and-later.5072/)



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
export TOOL=${CESM_ROOT}/my_cesm_sandbox_clm50_mksurfdata/tools

# modify /mksurfdata_map/src/Makefile.common and compile mksurfdata_map
cd ${TOOL}/mksurfdata_map/src
gmake USER_FC=gfortran USER_CC=gcc USER_FFLAGS="-fallow-argument-mismatch -fallow-invalid-boz -Wno-error" LIB_NETCDF="${NETCDF}/lib" INC_NETCDF="${NETCDF}/include" USER_CPPDEFS="-I${NETCDF}/include" USER_LDLAGS="-L${NETCDF}/lib -lnetcdff -lnetcdf"         

# gmake clean
# if gmake fails, use 'gmake clean' to clean up the object files and then recompile

# after gmake, the execuatble file is in ${TOOL}/mksurfdata_map 
# export GRID=${CESM_ROOT}/cesm_inputdata/lnd/clm2/mappingdata/maps/0.9x1.25/map_0.25x0.25_nomask_to_0.9x1.25_nomask_aave_da_c200309.nc
cd ${TOOL}/mksurfdata_map

# modify mksurfdata.pl, the input path:
# my $CSMDATA = "/work/n02/n02/yuansun/cesm/cesm_inputdata";
# my $urbanyr = "/work/n02/n02/yuansun/cesm/cesm_inputdata/lnd/rawdata/gao_oneill_urban/ssp3/urban_properties_GaoOneil_05deg_ThreeClass_ssp3_".$yr."_cdf5_c20220910.nc";
./mksurfdata.pl -res 0.9x1.25 -ssp_rcp SSP3-7.0 -glc_nec 10 -years 2015-2100


# download urban raw data from 2015-2100
```

### Error

**error**./mksurfdata.pl -res 0.9x1.25 -ssp_rcp SSP3-7.0 -glc_nec 10 -years "2015-2100" returns: ** Invalid simulation simulation year range: 2015-2100

**note**: in the bld/namelist_files/namelist_definition_ctsm.xml中

```
<entry id="sim_year_range" type="char*9" category="default_settings"
       group="default_settings" valid_values=
"constant,1000-1002,1000-1004,850-1850,1850-1855,1850-2000,1850-2005,1850-2100,1980-2015,2000-2025,2000-2100">

# 添加2015-2100
```

