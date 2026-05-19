# preBrams - BRAMS Pre Model

Create Initial Conditions files and contour files to BRAMS Model

## Prerequisites

This software needs NetCDF (C and Fortran) and WGRIB2 files

Before You install the prerequisites You must have at least one version of gfortran and gcc compiler. The instalation depends of the version of Linux and Kernel You have.

   The necessary packages are:

| Package        | Site                                               | File                        |
| -------------- | -------------------------------------------------- | --------------------------- |
| NetCDF - C     | https://downloads.unidata.ucar.edu/netcdf-c/       | netcdf-c-4.10.0.tar.gz      |
| NetCDF-Fortran | https://downloads.unidata.ucar.edu/netcdf-fortran/ | netcdf-fortran-4.6.2.tar.gz |
| wgrib-2        | https://www.ftp.cpc.ncep.noaa.gov/wd51we/wgrib2/   | wgrib2.tgz                  |

### Building NetCDF libraries

```bash
tar -xzvf netcdf-c-4.8.1.tar.gz 
cd netcdf-c-4.8.1/
CPPFLAGS=-I{YOUR_DIR}/include LDFLAGS=-L{YOUR_DIR}/lib CFLAGS='-O3'  CC={YOUR_DIR}/bin/mpicc ./configure --prefix={YOUR_DIR} --enable-netcdf4 --enable-shared --enable-dap
make
make install
cd ..

tar -xzvf netcdf-fortran-4.5.3.tar.gz
cd netcdf-fortran-4.5.3/
CPPFLAGS=-I{YOUR_DIR}/include LDFLAGS=-L{YOUR_DIR}/lib CFLAGS='-O3' FC={YOUR_DIR}/bin/mpif90  CC={YOUR_DIR}/bin/mpicc ./configure --prefix={YOUR_DIR}
make
sudo make install
cd ..
```

### Building grib2 libraries and API

```bash
tar -xzvf grib2.tgz
cd grib2
```

   Before compile You must modify the makefile. <u>Edit the makefile</u>, find and change the following variables (use the values as show bellow):

```makefile
USE_NETCDF3=0
USE_NETCDF4=0 
USE_REGEX=1
USE_TIGGE=1
USE_MYSQL=0
USE_IPOLATES=3
USE_SPECTRAL=0
USE_UDF=0
USE_OPENMP=0
USE_PROJ4=0
USE_WMO_VALIDATION=0
DISABLE_TIMEZONE=0
USE_NAMES=NCEP
MAKE_FTN_API=1
DISABLE_ALARM=0
MAKE_SHARED_LIB=0
USE_G2CLIB=0
USE_PNG=0
USE_JASPER=0
USE_OPENJPEG=0
USE_AEC=0
```

Now You can compile wgrib2 and move the libraries to lib dir:

```bash
make CC=<Your_C_compiler> FC=<Your_Fortran_compiler>
make CC=<Your_C_compiler> FC=<Your_Fortran_compiler> lib
sudo cp wgrib2/wgrib2 {YOUR_DIR}/bin/
sudo cp wgrib2/libwgrib2.a {YOUR_DIR}/lib/
sudo cp ./lib/*.a {YOUR_DIR}/lib/
sudo cp ./lib/*.mod {YOUR_DIR}/include/
cd ..
```

## Build preBrams

Now, go to preBrams directory end build the app:

```bash
fpm build --flag "-I<Your Include dir>/include" --flag "-L<Your_lib_dir>/lib"
fpm install
```

---



### The app will be installed in  **YourHomeDirectory/.local/bin/preBrams***

---



## Namelist

The app reads the namelist *pre.nml*. The namelist have the following data:

```
$ARGS_INPUT

!!!!! DATE !!!!!
init_year  = 2026,
init_month = 05,
init_day   = 19,
init_hour  = 0,
final_year = 2026,
final_month= 05,
final_day = 21,
final_hour = 00,

!!!!! TIME STEP !!!!!!!

step  = 6, !Timestep in hours

!!!!! ATMOS !!!!!
atmos_type   = 1, !0=DP, 1=GFS Grib2, 2=ERA5
atmos_prefix ='gfs.t00z.pgrb2.0p25.f',
atmos_sufix  ='',
atmos_idir   ='./datain/GFS/',
levels = 23,
initial_latitude = -70., !initial latitude for domain of model (-90 to 90)  
final_latitude  = 20., !final latitude for domain of model(-90 to 90)
initial_longitude = 250., !initial longitude for domain of model (0 to 360)
final_longitude = 358., !Final longitude for domain of model (0 to 360)

!!!!! CHEM !!!!!! 
chem_type     = 1, !0 = no Chem, 1 = CAMS 
chem_idir  = "/data/datafix_cams/",
chem1_prefix ='',
chem1_sufix  ='-CAMS-EC-2010-2019-AMS',

!!!!! OUTPUT !!!!!
out_type   = 2, !0=text, 1=VFM, 2=Grads
out_prefix = 'IC',
out_sufix  = '',
out_dir    = './datain/GRADS/',

$END
```

After You create the namelist, just run the app and the CI/CC files will be created.



```
YourHomeDirectory/.local/bin/preBrams
```



Att: To run the app You must have the GFS or ERA5 data. If You will use chemistry background You must have datafix_cams data.