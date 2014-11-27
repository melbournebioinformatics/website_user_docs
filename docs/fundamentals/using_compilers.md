# Using compilers on VLSCI systems

## Compilers on the x86 clusters

These machines have a wide range of libraries available, the modules avail lists most of them.

Intel provide a useful page for building a compile line at the 
[Intel Math Kernel Library Link Line Advisor](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor).

Machines have both GCC and the Intel compilers available.

* To select the default GCC compiler, type `module load gcc`
* To Select the default Intel compiler, type `module load intel`

Please use the `module avail` for details of versions installed. You can request a specific version by giving the module command the full name as show by `module avail`.

## Compilers on the BlueGene/Q

There are different compilers available on the BlueGene front-end node used for different purposes. There are compilers used for an application to run on the Blue Gene compute nodes and then there are compilers for code to run on the front-end node. There are also the GNU set of compilers (V4.4.6) and the IBM XL Compilers (V12.1 for C/C++ and V14.1 for Fortran). Below is a list of the available compilers:

* mpicc - GNU or XL Re-entrant (Thread Safe) C Compiler
* mpicxx - GNU or XL Re-entrant (Thread Safe) C++ Compiler
* mpif77 - GNU or XL Re-entrant (Thread Safe) Fortran77 Compiler
* mpif90 - GNU or XL Re-entrant (Thread Safe) Fortran90 Compiler
* mpixlc - XL C Compiler
* mpixlc_r - XL Re-entrant (Thread Safe) C Compiler
* mpixlcxx - XL C++ Compiler
* mpixlcxx_r - XL Re-entrant (Thread Safe) C++ Compiler
* mpixlf77 - XL Fortran77 Compiler
* mpixlf77_r - XL Re-entrant (Thread Safe) Fortran77 Compiler
* mpixlf90 - XL Fortran90 Compiler
* mpixlf90_r - XL Re-entrant (Thread Safe) Fortran90 Compiler
* mpixlf95 - XL Fortran95 Compiler
* mpixlf95_r - XL Re-entrant (Thread Safe) Fortran95 Compiler
* mpixlf2003 - XL Fortran 2003 Compiler
* mpixlf2003_r - XL Re-entrant (Thread Safe) Fortran 2003 Compiler

Modules are used to control which compiler is used. By default the mpi-xl module is loaded at login; this will make all the XL compilers listed above available. To use the GNU compilers the mpi-gcc module must be loaded in place of the mpi-xl module. Use the module switch mpi-xl mpi-gcc command to swap between the XL and GNU compilers.

With the mpi-xl module, by default, the 2003 standard for Fortran will be used. If you wish to use a different standard for Fortran, such as F77 or F90, then the appropriate module needs to replace the 2003 version. Use the module switch mpi-xlf2003 mpi-xlf77 command to swap from the 2003 to 1977 standard version of Fortran. Other standards available are mpi-xlf90 and mpi-xlf95 .

If the code does not use MPI, but is multi-threaded and/or embarrassingly parallel and needs to run as a high-throughput application on the Blue Gene, the MPI compilers should still be used as they are configured to generate code specific for the Blue Gene and will link to all the necessary Blue Gene libraries. For multi-threaded code, make sure to use the re-entrant, thread safe versions of the compilers. The MPI compilers are basically wrappers to the standard compilers with the appropriate flags to generate the correct code and link to the correct libraries for Blue Gene execution.

### Advanced Compiler Usage

If you have used some of the optimisation options with the building of your applications, some of these might have changed with the XL compilers. If you have used the `-qarch` or `-qtune` options, then the new value to use is `qp` for the Blue Gene/Q, as in `-qarch=qp -qtune=qp`.

If you have not used the MPI wrappers for building your applications, but used the BlueGene compilers directly, such as bgxlc then some of the include and library paths have changed. Replace the following compiler options as appropriate:

* `-I/bgsys/drivers/ppcfloor/comm/xl/include` or
* `-I/bgsys/drivers/ppcfloor/comm/gcc/include`

* `-L/bgsys/drivers/ppcfloor/comm/xl/lib` or
* `-L/bgsys/drivers/ppcfloor/comm/gcc/lib`

* `-L/bgsys/drivers/ppcfloor/spi/lib`

### Front End Node Compilers

* cc - GNU or XL C Compiler
* gcc - GNU C Compiler
* g++ - GNU C++ Compiler
* gfortran - GNU Fortran Compiler
* cc_r - XL Re-entrant (Thread Safe) C Compiler
* c89 - XL C (1989 Standard) Compiler
* c89_r - XL Re-entrant (Thread Safe) C (1989 Standard) Compiler
* c99 - XL C (1999 Standard) Compiler
* c99_r - XL Re-entrant (Thread Safe) C (1999 Standard) Compiler
* gxlc - XL C Compiler that understands GNU command line arguments
* gxlc++ - XL C++ Compiler that understands GNU command line arguments
* gxlC - XL C Compiler that understands GNU command line arguments
* xlc - XL C Compiler
* xlc_r - XL Re-entrant (Thread Safe) C Compiler
* xlc++ - XL C++ Compiler
* xlc++_r - XL Re-entrant (Thread Safe) C Compiler
* xlC - XL C++ Compiler
* xlC_r - XL Re-entrant (Thread Safe) C++ Compiler
* f77 - XL Fortran77 Compiler
* fort77 - XL Fortran77 Compiler
* f90 - XL Fortran90 Compiler
* f95 - XL Fortran95 Compiler
* xlf - XL Fortran77 Compiler
* xlf_r - XL Re-entrant (Thread Safe) Fortran77 Compiler
* xlf90 - XL Fortran90 Compiler
* xlf90_r - XL Re-entrant (Thread Safe) Fortran90 Compiler
* xlf95 - XL Fortran95 Compiler
* xlf95_r - XL Re-entrant (Thread Safe) Fortran95 Compiler
* xlf2003 - XL Fortran 2003 Compiler
* xlf2003_r - XL Re-entrant (Thread Safe) Fortran 2003 Compiler

Modules are used to control which compiler is used. By default the GNU compilers (version 4.4.6) are available at login with no module loaded. To use the XL compilers the xlc and/or xlf module must be loaded.

### XL Compilers

All the documentation below can be interactively browsed at the [XL Compiler web page](http://pic.dhe.ibm.com/infocenter/compbg/v121v141/index.jsp).

IBM's compilers for the POWER processor architecture are XL C/C++ and XL Fortran. The BlueGene versions of these compilers are derived from the Linux on POWER versions since they execute on the BlueGene front end node which is a Linux on POWER system. Most documentation is therefore the same as that for the Linux on POWER versions.
