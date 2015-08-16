#GROMACS Tutorials

Some [GROMACS](http://www.gromacs.org) tutorials for beginners. It's not
necessary to do the tutorials in order, but the first two
tutorials are essential before going on to the others, as the structure file
(`methane.pdb`) and topology file (`topol.top`) for methane from tutorial 2 are
used in all subsequent tutorials.

I assume you have some working knowledge of the command line. Specifically, you
should know how to make directories, change into them, and edit text files.
Obviously I also assume you have GROMACS installed on a machine available to you.

Throughout the tutorials we'll be using OPLS methane and TIP4PEW water.

##Contents

1. [Water](1_tip4pew_water/README.md) - Basics of setting up a simulation. Find out the
   density of TIP4PEW water.
2. [One methane in water](2_methane_in_water/README.md) - How to create a topology file
   for a molecule and solvate it. Get the radial distribution function.
3. [Several methanes in water](3_methanes_in_water/README.md) - How to put multiple
   solutes into a system. Get the methane-methane potential of mean force.
4. [Free energy of solvation of methane](4_methane_fe/README.md) - How to do a free energy
   simulation when coupling a molecule. Use MBAR to get the result.
5. [Umbrella sampling](5_umbrella/README.md) - Get methane-methane PMF from umbrella sampling using pull
   code.

## Links

Some of the other software that I use in these tutorials that you may find
useful are:

* [alchemical-analysis](https://github.com/MobleyLab/alchemical-analysis)
* [Avogadro](http://avogadro.cc/wiki/Main_Page)
* [gnuplot](http://www.gnuplot.info/)
* [vmd](http://www.ks.uiuc.edu/Research/vmd/)

