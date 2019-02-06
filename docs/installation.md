After making sure that the Makefile is set up acording to your system, the compilation of the code is done by simply running  
``` bash
make all
```
in the command prompt while standing in SUMO's main directory.

!!! info "gfortran"
    The compilation is performed with gcc/gfortran per default. Make sure you have the latest version of `gfortran` installed. If you would like to use another compiler, modify the Makefile acordingly. Further instructions are found in this file.

!!! info "Lapack and Blas"
    The codes depend on Lapack and Blas[^1] so make sure you have these installed. If not, then install `liblapack-dev` and `libblas-dev` or similar.

!!! info "OpenMPI"
    The codes also depend on MPI for paralellization. Make sure you have some implementation of MPI installed, e.g. OpenMPI: `libopenmpi-bin`, `libopenmpi-dbg` and `libopenmp-dev`.


The installation may be cleaned up (i.e. remove redundant files) in a standard manner.
``` bash
make clean 
```  
[^1]: [www.netlib.org](http://www.netlib.org/)
