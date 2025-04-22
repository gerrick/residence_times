# residence_times

april 21 2025

Adding some details about usage and workflow. There aren't a lot of details, but this algorithm was first used in this ref: 

J. Phys. Chem. B 2014, 118, 5, 1363–1372; https://pubs.acs.org/doi/10.1021/jp4085662

The inputs will be an amber parmtop file (assuming the parm7 format), a human readable crd trajectory file, and the first solvation shell 
cutoff from the RDF between the two atoms/particles/sites you're looking at. Your simulation traj is probably (should be) in a compressed format like dcd 
or netcdf, which you can convert to crd with cpptraj using commands like this: 

parm *.parm7

trajin mdcrd

trajout mdcrd.crd

go

exit

You will also need to run an radial distribution function calculation. If you're looking at species "N1" and "C2", then calculate the N1 to C2 rdf.
You will need the first minimum in their distribution (to capture the whole first solvation shell or the first hill in the rdf). CAUTION: This 
script only reads atom names, not residues. So if you have multiple atoms in different residues with the same name, you need to carefully design 
your analysis to not get confusing results. You can include a strip command in the cpptraj step to address this issue. 

Now you have everything required to run the residence time calculation. The first step will find all pairs between the atoms of interest. This script is 
called "amber-neighbors" and assumes the residence time is being calculated between different atoms in a NVT trajectory. This amber-neighbors script is 
the most robustly tested, but there are other variants for NPT simulations and same neighbors calculations. Becuase of the file management strategy, it 
is usually a good idea to run a new directory, or at least be sure to delete any working files from a previous calculation. Clean up would look like: 

rm -f myoutput-1.*

For an NVT trajectory, if the two species are N1 and C2 and the first minimum in their RDF occurs at 6.5 Å, then the neighbors command will look like: 

./amber-neighbors file.parm7 mdcrd.crd N1 C1 6.5

Proper execution will produce a bunch of files that elegantly are named "myoutput-1.[number].out". The "number" is the atom index of one of the N1 atoms and
file contains a line for every timestep like this: 

4167 3 168 858 2535

Where 4167 is the timestep, 3 is the number of neighbors of type C1 found within 6.5 Å of that atom, and 168 858 2535 are the indices of those C1 atoms. Next
you will examine each myoutput-1.[number].out file to find the neighbors with the script no-hopo-time-get. In this script you may need to change the variable
$nhn to deal with rattling effects. It is set to 2 for 2 ps rattling effents, where the units are set by the frame spacing in the trajectory. Again you should
clean up the working directory. For the command that will come, you want to be sure the res-time.dat file doesn't exist or is empty. So run something like this:

rm -f res-time.dat

And then you can calculate the diration of each pairing of N1 and C1 from all myoutput-1.[number].out files with the command: 

for p in myoutput-1.* ; do ./no-hopo-time-get $p >> res-time.dat ; done

The res-time.dat file will contain a line for the duration of each observed pairing of N1 and C1. Initial and final pairings, where thier approach and separation
are not observed are ignored. Thus file will have lines for every pair like this: 

8 # 1224 315

This means atoms 1224 and 315 had a pairing that lasted for 8 frames (units of the time spacing of the trajectory). You can average these directly to get an 
average residence time, construct a correlation function to see the lifetime of the pairing, etc. 

may 19 2019 ###########################

Added same-type-amber-neighbors.npt which calculates te residence time between pairs of one atom type. The usage is something like this: 

./same-type-amber-neighbors.npt ../cho_ger.parm7 ../mdcrd.crd N1 12.0

May 16 2019 ###########################

Added amber-neighbors.npt to handle the neighbor search for npt simulations

may 8 2019 ###########################

Importing all of these on github. 

july 26 2013 ###########################

These are scripts for calculating the residence time from amber prmtop and mdcrd files. They are similar to the lammps scripts, but just modified for amber file structures. Here is the general useage:

./amber-neighbors all.prmtop mdcrd N1 IM 9.99 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./no-hopo-time-get $p >> res-time.dat ; done

And

./amber-neighbors all.prmtop mdcrd N1 IM 9.99 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./no-hopo-time-get $p >> res-time.dat ; done

june 19 2014 ###########################

They are now implemented for lammps. Here is the general usage:

./lammps-neighbors /scratch/voth1/gerrick/OH-interface/9/9.run/lmp.lammpstrj 3 1 3 ; rm -f res-time.dat ; for p in myoutput-1.* ; do ./lammp-zeros $p > temp ; mv temp $p ; ./no-hopo-time-get $p >> res-time.dat ; done
