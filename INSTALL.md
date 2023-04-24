# COMPILATION AND INSTALLATION PROCEDURE

## QUICK OVERVIEW
MLIP has a serial and parallel version. Building the serial version of MLIP
requires a modern C++ compiler (supporting c++11), parallel version requires
an MPI C++ compiler, as well as C and FORTRAN compilers.

The functionally MLIP is independent from other libraries like BLAS or LAPACK.
MLIP can work without this libraries using only the embedded version of BLAS
(although you are advised to use the high-performance libraries, e.g.,
Intel MKL or OpenBLAS).

MLIP build system consists of two step: configuration and compilation.
The configuration is performed by running:
```bash
./configure [OPTIONS]
```

Detailed descriptions of configuration options can be viewed by:
```bash
./configure --help
```

After configuration, the executables can be built with make command:
```bash
make <target-name>
``` 

For information about the build executables, execute
```bash
make help
```

## DETAILED INSTRUCTIONS

1. Configure the MLIP package build
First, start with executing the configuration script
```bash
./configure
```
The configuration attempts to set the correct values for various
system-dependent variables for compilation. The values related to BLAS are
autodetected in the case if Intel MKL is installed on the system.
If not, then the "embedded" BLAS (supplied with MLIP) will be used.
You can force to use the embedded BLAS with the `--blas=embedded` option:
```bash
./configure --blas=embedded
```
By default, the parallel version of the MLIP is compiled. Compilation of serial
version can be done by specifying the `--no-mpi` option to configure':
```bash
./configure --no-mpi
```

2. Build the MLIP executables
You can compile and build the MLIP executable by command:
```bash
make mlp
```

3. Install OpenBLAS (optional)
You may want to download and install the OpenBLAS library:
```bash
git clone https://github.com/xianyi/OpenBLAS.git
make -C OpenBLAS 
make PREFIX=./ -C OpenBLAS install
```
(You, of course, can download it directly from 
https://github.com/xianyi/OpenBLAS).
You should then configure MLIP with
```bash
./configure --blas=openblas --blas-root=<OpenBLAS path>
```

## BUILDING MLIP WITH A LAMMPS EXECUTABLE
1. Download the LAMMPS software:
```bash 
git clone https://github.com/lammps/lammps.git
```
(Or your other favorite way of downloading)

2. Configure MLIP to be (also) used within LAMMPS
```bash
./configure --lammps=<LAMMPS path>
```
`<LAMMPS path>` is where you have downloaded LAMMPS (it should have the src/
folder inside `<LAMMPS path>`.)

3. You can attempt to automatically make LAMMPS
```bash
make lammps
```
This will create two executables, `lmp_serial` and `lmp_mpi` in the `bin/` folder
of MLIP (if you are lucky, if not - follow the manual installation guide below)

4. Build the MLIP-for-LAMMPS library (if auto build fails):
```bash
make liblammps 
```
This library is needed to build LAMMPS with MLIP
Then copy it to the LAMMPS sources:
```bash
mkdir -p <LAMMPS path>/lib/mlip
cp lib/lib_mlip_lammps.a <LAMMPS path>/lib/mlip
```

5. Copying MLIP-LAMMPS interface to LAMMPS (if auto build fails):
```bash
mkdir -p <LAMMPS path>/src/USER-MLIP
cp make/LAMMPS/Install.sh <LAMMPS path>/src/USER-MLIP
cp make/LAMMPS/README <LAMMPS path>/src/USER-MLIP
```

You will also need to create  `<LAMMPS path>/lib/mlip/Makefile.lammps`
with the following content:
```
mlip_SYSINC =
mlip_SYSLIB = -lgfortran -lpthread 
mlip_SYSPATH = 
```

Make sure that you are in `LAMMPS` directory and type:
```bash
make -C ./src yes-user-mlip
make -C ./src serial
make -C ./src mpi
```
    
If you get no error and the LAMMPS executables `lmp_serial` and `lmp_mpi` are
produced in the `<LAMMPS path>/src` directory. Copy them over to `<MLIP path>/bin/`


## BUILDING MLIP WITH A LAMMPS SHARED LIBRARY

1. Configure mlp as in the previous section but then run
```bash
make lammps-shlib
```
This will create `lib/liblammps_serial.so` and `lib/liblammps_mpi.so`


## TESTING THE MLIP BUILD
The build can be tested by running:
```bash 
make test
```
This launches a number of tests, including test of LAMMPS if it was instlaled
(i.e., if `bin/lmp_serial` and `bin/lmp_mpi` exist).

## PYTHON LIBRARY (mlippy)

To build mlippy, from the root directory execute:
```bash
make mlippy
```
You will need to install "Cython","MPI4py","ASE" packages to build mlippy.

This will create a file 'lib/mlippy.so'

To make the mlippy library importable, put the 'mlippy.so' file into the $PYTHONPATH directory
or near your running *.py file



