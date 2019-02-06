# R  H  Y  Z  E 
**A Relativistic Hyperfine-Zeeman GRASP2k module**
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
**Description:** RHYZE is a GRASP2K [[1]](http://dx.doi.org/10.1016/j.cpc.2013.02.016) addon module for evaluation of hyperfine and/or magnetic-field perturbed states and transition rates and lifetimes in atoms or ions. Unperturbed eigenstates and transition properties as-well as Hyperfine and Zeemain interaction matrix elements are calculated beforehand using GRASP2k [[1]](http://dx.doi.org/10.1016/j.cpc.2013.02.016) and HFSZEEMAN [[2]](http://dx.doi.org/10.1016/j.cpc.2007.07.014)

An updated version of the HFSZEEMAN code by Andersson and Jönsson CPC (2008) [[2]](http://dx.doi.org/10.1016/j.cpc.2007.07.014) is also included in the present package - the aim is re-write the code in modern Fortran and parallize it with e.g. OpenMP, with the latter being of higher priority.

**Author:** Jon Grumer, Lund University, Sweden, (c) 2015-2016

**Personal Homepage:** http://matfys.lth.se/staff/Jon.Grumer

**E-mail:** jon.grumer@teorfys.lu.se / jongrumer@gmail.com

**External software dependencies:** GRASP2K [[1]](http://dx.doi.org/10.1016/j.cpc.2013.02.016), HFSZEEMAN [[2]](http://dx.doi.org/10.1016/j.cpc.2007.07.014)

**External library dependencies:** LAPACK [[3]](http://www.netlib.org/lapack/)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
### RHYZE in short
Determines perturbed atomic eigenstates and radiative properties under some magnetic-field and nuclear conditions.
Exotic transition channels which are opened up due to the additional mixings introduced by these perturbations are usually of special interest.

##### Method
The M<sub>J</sub>, F or M<sub>F</sub> dependent eigenstates and transitions properties are evaluated using three different methods of increasing sophistication: 

  **1) Diagonal treatment** of the states in which there is no inclusion of mixing due to the magnetic-field or hyperfine perturbations - equivalent to A and B constants in the hyperfine case - and the transition rates are determined to first order as matrix elements between the eigenstates.

  **2) Full diagonalization** of the magnetic-field and/or hyperfine interaction matrix to determine the perturbed eigenstates (mixing between unperturbed states is included to all orders) and the transition rates are determined to first order as matrix elements between the eigenstates. 

  **3) [under development] Radiation damping** through a complex non-local optical potential [[4]](http://journals.aps.org/pra/abstract/10.1103/PhysRevA.52.1319). In this method the perturbations and the interaction with the radiation field are treated on an equal footing, which is important for close degeneracies when the natural width of the states are of similar magnitude as the fine-structure separations. 

##### Input files
* **isodata** - [identical to the GRASP2k isodata file] Contains necessary information about the nucleus (I, mu, Q) 

* **\<name\>.hzm** [one or more] Output files from the HFSZEEMAN (the version included in this package) containing reduced Hyperfine and Zeeman interaction matrix elements as-well as information about the unperturbed basis states (the ASFs). If more than one file is included, then make sure that no state is duplicated.

* **\<case-id\>.grasptransdata.rhyze.inp** - A file with concattenated GRASP2k transition files containing information about the unperturbed transitions - i.e. say file1 contains E1 and M2 and file2 M1 and E2 transitions, then `cat file1.ct file2.ct > <case-id>.grasptransdata.rhyze.inp`. The states involved in the transitions should match the states in the .hzm files. These have to be calculated using a modified version of the GRASP2k transition code (RTRANSITION) wich, in addition to gf, A and S, also prints the reduced transition matrix elements with the necessary phase information.

* **\<case-id\>.caseinfo.rhyze.inp** [optional] - input parameters which will be automatically read during the execution (see output files for more info) 

##### Output files
* **\<case-id\>.asfdata.rhyze.out** - collected information about the unperturbed states (ASFs), and hyperfine, zeeman and radiative transition interaction matrices in this basis

* **\<case-id\>.caseinfo.rhyze.out** - input parameters given by the user, rename to `<case-id>.caseinfo.rhyze.inp` for the code to read this information automatically during the next execution 

* **\<case-id\>.eigenstates.rhyze.out** - information about the perturbed basis states and resulting eigen states, including complete eigenvectors

* **\<case-id\>.transitions.rhyze.out** [optional] - radiative transition data (transition energies, rates, lifetimes, widths)

* **\<case-id\>.fieldscan.rhyze.out** [optional] - scan of eigenenergy structure from the field-free limit up to the given magnetic field (to produce level-crossing diagrams and identify level crossings)

### Installation
The compilation of the code is done by simply running  
```
make
```
in the command prompt while standing in the main directory. If successful, exectuables will be put in the created bin directory.

* **Note 1)**   External library dependence. The code is dependent on LAPACK [3] for diagonalization, so make sure you have this installed. LAPACK is usually bundled with most modern Linux distributions, such as Ubuntu or Suse, so this should not pose a problem for most users. It might be that the LAPACK libraries can't be located during the compilation even though you have them installed, in that case it might help to install the LAPACK developement package. I.e. for Debian/Ubuntu/Mint/... you just type  `sudo apt-get install liblapack-dev` in the command prompt. Or you can set up the linking correctly manually.

* **Note 2)**   The compilation is performed with gcc/gfortran per default. If you would like to use another compiler, please modify the Makefiles in the src sub-directories. Further instructions are found in this file.

* **Note 3)**   If you don't have a working installation of gfortran on your machine, then install it accordingly for Debian/Ubuntu/Mint/... ` sudo apt-get install gfortran`

* **Note 4)**   HFSZEEMAN is dependent on some GRASP2k library routines, so make sure you have compiled these beforehand

The installation may be cleaned up (i.e. remove redundant files) in a standard way
```
make clean 
```  
To completely clean up the installation, i.e. remove the binaries and the bin folder, run
```
make cleanall
```  

### Present Assumptions

 - The code can read any number of input files from 
   HFSZEEMAN. The perturbed interaction matrix is 
   constructed and diagonalized. However, if the energy 
   separations are big then the matrix might become 
   ill-conditioned and numerical precission lost. In this 
   case one should diagonalize the independent blocks, 
   corresponding to different hfszeeman input files, 
   separately. This is requires a minor modification of
   the main program due to the generic object oriented
   design of the code.

 - The GRASP2k transition matrix element is currently given
   as the square root of the line strength multiplied by
   the phase. 

    a) The phase/ket-bra issue: It is assumed, for now, that
      the matrix element is given on the form 
      <_lower_||Tk||_upper_>. This is most likely true, but
      should be verified.
   
    b) The fact that the reduced radiative matrix element in 
      GRASP2k is constructed from a "relativistic" line strength implies
      that they actually have a week energy dependence. This is
      an approximation in the current method, which however should be 
      negligible.

### To-Do's

 -  Make sure the correct decoupling formula is used for decoupling I in a 
   JIF representation (Cowan 11.38 vs 11.39)

 -  Verify assumption a) above - the transition matrix element phase problem

### Programming Guidlines

 - Global datastructures are strictly restricted to parameters.
   All datastructures that are variable have to be sent as 
   arguments to subroutines or functions.
   
 - The code should be written in an object-oriented fashion.
   I.e. define classes (derived types) with datastructures and 
   methods in a logical way. As an example one can define a 
   class representing a basis set, which also contains related
   operations one would like to perform on the seti, such as
   printing information about it, or routines for adding 
   or deleting a subsection of the set.

 - Error messages should be handled by 'error stop' and follow 
   the format:
```
   error stop "main module name|procedure name: informative error message"
```
 - Use error handling extensively, in the sense that you make 
   sure that expected result is found, i.e. if you determine a 
   phase exponent that should be an exponent, then check this
   using the utillity function `f_isint( )`, if ok then
   proceed with the execution, otherwize do an error stop
   with a message according to the required format above.

### References
**[1]** [Jönsson et al. Comp. Phys. Comm. **184** 9 2197 (2013)](http://dx.doi.org/10.1016/j.cpc.2013.02.016)

**[2]** [Andersson et al. Comp. Phys. Comm. **178** 2 156 (2008)](http://dx.doi.org/10.1016/j.cpc.2007.07.014)

**[3]** [LAPACK - Linear Algebra PACKage](http://www.netlib.org/lapack/)

**[4]** [Robicheaux et al. Phys. Rev. A 52 1319 (1995)](http://journals.aps.org/pra/abstract/10.1103/PhysRevA.52.1319)
