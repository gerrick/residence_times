# residence_times

may 19 2019

Added same-type-amber-neighbors.npt which calculates te residence time between pairs of one atom type. The useage is something like this: 

./same-type-amber-neighbors.npt ../cho_ger.parm7 ../mdcrd.crd N1 12.0

May 16 2019

Added amber-neighbors.npt to handle the neighbor search for npt simulations

may 8 2019

Importing all of these on github. 

july 26 2013

These are scripts for calculating the residence time from amber prmtop and mdcrd files. They are similar to the lammps scripts, but just modified for amber file structures. Here is the general useage:

./amber-neighbors all.prmtop mdcrd N1 IM 9.99 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./no-hopo-time-get $p >> res-time.dat ; done

And

./amber-neighbors all.prmtop mdcrd N1 IM 9.99 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./no-hopo-time-get $p >> res-time.dat ; done

june 19 2014

They are now implemented for lammps. Here is the general usage:

./lammps-neighbors /scratch/voth1/gerrick/OH-interface/9/9.run/lmp.lammpstrj 3 1 3 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./lammp-zeros $p > temp ; mv temp $p ; ./no-hopo-time-get $p >> res-time.dat ; done
