SUMO - Supernova MOnte Carlo code.

Papers : Jerkstrand, Fransson & Kozma 2011, see also
Jerkstrand et al. 2012, Jerkstrand et al. 2015 (A&A).

This file created by A. Jerkstrand anders@mpa-garching.mpg.de.

PREPARE THE ENVIRONMENT
-------------------------------------

1. Define the environmental variable "DD" in your login script (eg .bashrc)
   For example in bash: 
``` bash
      export DD=/disk/myscratchfolder     
```
     DD should point to some folder for scratch reading and writing (sometimes large files are created).

2. Go to the DD folder, and type
``` bash
     mkdir mcphotons
     mkdir mcphotons/radfield
     mkdir modelspectra
```
3. The code needs access to the LAPACK and BLAS mathematical libraries. If you do not
have these installed, download them from www.netlib.org and install. Then, open the sumo Makefile and edit the line `LIBS = ...`  To give the path to your libraries. If you have static libraries of BLAS and LAPACK somewhere, edit .bashrc to
```bash
    export SUMOLIBS='..../blas_LINUX.a ..../lapack_LINUX.a' (replace with relevant path and names)
```
and mark in `LIBS=$(SUMOLIBS)` in the Makefile

To use MKL library files for BLAS and LAPACK, use
```bash
LIBS = $(MKLROOT)/lib/em64t/libmkl_lapack95_lp64.a -Wl,--start-group $(MKLROOT)/lib/em64t/libmkl_intel_lp64.a $(MKLROOT)/lib/em64t/libmkl_intel_thread.a $(MKLROOT)/lib/em64t/libmkl_core.a -Wl,--end-group -L$(MKLROOT)/lib/em64t -liomp5 -lpthread
```

COMPILATION
-------------------------------------

The code compiles with a Makefile.

Compilation is by default with `-O3` optimization, edit this in
`FFLAGS=...` if needed.

Go into the folder where you unpacked the code and run
``` bash
     make all
```
Compilation creates a large set of executables (currently 12), most of
which are used sequentially in an iteration loop.
The main code is made of the three executables

* "suse" : Calculates temperatures, excitation and ionization statistical equilibria, and
emissivities (radiative terms and ionization balance fixed)
* "sumo" : Runs Monte Carlo simulation of radiation field (temperatures,
excitation and ionization statistical equilibria fixed)
* "suib" : Calculates ionization statistical equilibria (temperatures, 
radiative terms fixed).

These three are iterated until convergence is achieved. Information on how to
construct wrapped and test for convergence to be added..

### Ex suse (don't run yet, see first preparation of input files below):

```bash
mpirun -np 19 suse example file_modelparameters_example y n 500 10000 1 > outfile_suse
```

Run model with name `example`, and run-file named `file_modelparameters_example`. The `y`
means compute the gamma-ray deposition (only needed once),
the `n` means this is not an iterative run (but the first one),
`500` means start at 500 A, `10000` means end at 10000 A, `1` means
use resolution of 1 A (at 1000 A). The numer of processes (`19` here) has
to be the number of zones in the model plus one; so here the
model must have 18 zones.
In the first iteration one should
use the combination "y n", whereas later on "n y" should be used.

### Ex sumo (always after suse):
```bash
mpirun -np 100 sumo example file_modelparameters_example 500 10000 1 100000000 5 5 50 > outfile_sumo
```
Here example, `file_modelparameters_example`, `500`, `10000`, and `1` mean same as before (keep the same as
when running suse).
`100000000` means use a (fiducial) total number of 100000000 packets
(this is, in fact, not really the total number of packets, let it
be 100000000 for now). `5 5 50` means, use at least 5 packet per optical/infrared energy bin,
at least 5 packets in the UV, and never use more than 50 packets.

For sumo the number of processors can be anything (100 in the example).
Scaling should be relatively linear with number of processors (but depends a lot
on the number of packets).

Then run
```bash
./gather.sh
```
which is an intermediate step necessary between sumo and suib.

### Ex suib (always after sumo):
```bash
mpirun -np 19 suib file_modelparameters_example y > outfile_suib
```
The `y` means its an iterative run.

PREPARING INPUT FILES
---------------------------------------
The code needs 2 input files to run, the explosion model file, and the
model parameters file.

1) The explosion model file.

   An example file is provided (`DATA/expmodels/file_explmodel_example`).

   The format of these files should be as follows:
```
   Line
   1  Name of model and then name of various zones
   2  Mass of zone [Msun]
   3  Inner velocity [km/s]
   4  Outer velocity [km/s]
   5  Filling factor. Is normally 1. But the code contains an option to macroscopically mix the first Ncore zones. The velocity limits for all these are then the inner velocity of zone 1 and the outer velocity of zone Ncore. The filling factor for each of the first Ncore zones can then be smaller than 1.
   6  Density at 100 days [g cm^-3]. Not used by code, can put values to zero if wished.
   7  ---------
   8  56Ni+56Co mass fraction (at t=0, before any decay has occurred).
   9  57Ni mass fraction (at t=0, before any decay has occured).
   10 44Ti mass fraction (at t=0, before any decay has occured).
   11 ---------
   12 H mass fraction 
   13 He -- " --
   14 C -- " --
   15 N -- " --
   16 O -- " --
   17 Ne -- " --
   18 Na -- " --
   19 Mg -- " --
   20 Si -- " --
   21 S -- " --
   22 Ar -- " --
   23 Ca -- " --
   24 Fe -- " --
   25 Co -- " --
   26 Ni -- " --
   27 Ti -- " --
   28 Al -- " --
   29 Cr -- " --
   30 Mn -- " --
   31 Sc -- " --
   32 V -- " --
```
   Note that line 25, 26 and 27 refers to *stable* Co, Ni and Ti.

2) The model parameters file.

   This file should be created in the code execution directory.
   An example file (`file_modelparameters_example`) is provided.

   The philosophy if the code design is to maintain a core source code that should not have to be recompiled depending on which models or parameter space to investigate. If you wish to run 10 different models with 10 different dust parameters, therefore create 10 different "run settings" files, and set up a script to process these in turn.  

   The format of `file_modelparameters` is as follows:
```
Row
1        Time [days since explosion]
2        Distance to SN [MPc]
3        Dust optical depth [dim-less]
4        Explosion model file (see above)
5        Number of zones
6        Ncore : Number of zones to macroscopically mix ("0" for no macroscopic mixing).
7        Ndust : Number of zones to apply dust opacity to
8        Nblobs : Number of clumps in macroscopically mixed model
9        --------------------------
10       VOID
11       VOID
12       VOID
13       VOID
14       VOID
15       VOID
16       VOID
17       VOID
18       -----------------------
19       e+ leakage : 1 => free streaming (no magnetic field), 0 => on-the-spot absorption (complete magnetic confinement). 
20       Npis :   Maximum number of levels to include photoionization from. 0 => no photoionizations from excited states. Typical value : 50
21       PHOTOEXC : Include photoexcitation in NLTE solutions? 2 => Yes. 1 => Only deexcitations. 0: No. Typical value : 2.
22       beta_Lya : Probability of Lya branching into overlapping metal lines. Typical value : 5E-9.
23       beta_Lyb : Probability of Lyb branching into overlapping metal lines. Typical value : 2.1E-5.
24       tau_tresh : Ignore line optical depths below this value. Typical value : 1E-3.
25       sf_tresh : Ignore computation of non-thermal excitation and ionization for elements with mass fractions below this value. Typical value 1E-3.
26       CTON : .T. => Charge transfer reactions on. .F. => Off. Typical value : .T.
27       MOLSW : 0 => No molecules. 1 => Include molecules. CURRENTLY ONLY 0 supported.
28       OLI : Allow only photoionization by local radiation field? .F. => No, .T.=> Yes. Default : 0.
29       REALRECCONT_H : Use real free-bound continua for HI? .T. => Yes. .F. => No (then uses box-shaped approximation as for other elements). Typical value : .T.
30       FORCECAII : Force Ca to be in the Ca II form everywhere? .F. => no, .T. => yes. Typical value : .F.
31       VOID
32       VOID
33       DOPRINT : Print a lot of output? .T. => Yes, .F. => No. Typical value: .F.
34       -----------------------------------
35       HI : Entry 1 : Allow this atom to emit radiation? Entry 2: Allow this atom to absorb radiation? Entry 3 : How many levels to compute NLTE solutions for? (0=all).
         ...
81       H2 ... (same as above)
```

EXAMINING THE OUTPUT
-------------------------------------------

   Spectrum
   -----------

   The main output of the code is the model spectrum, which goes to the file `$DD/modelspectra/spectrum.dat*IDNUMBER*`.
   The columns in this file are as follows:

```
   1  Wavelength [A]
   2  F_lambda [erg s^-1 cm^-1 A^-1]
   3  F_lambda, smoothed with Vsmooth
   4    component1..
   5    component2..
   ..
   ..
```

   Temperatures (from suse)
   -------------  
   Written to `./out/plts/teqs.dat*SHELLNUMBER*`
 
   Excitation (from suse)
   --------------
   Written to `./DATA/EXC_CUBE.dat*SHELLNUMBER*`

   Ionization (from suib)
   --------------
   Written to `./out/ionization/ionfracs.dat*SHELLNUMBER*`

