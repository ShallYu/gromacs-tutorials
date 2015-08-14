One Methane in Water
====================

In this tutorial I'll show you how to create a system containing one OPLS
methane in a box of Tip4pEW water.

Setup
-----

As before, we need a structure file, a topology file, and parameter files. We're
going to use the GROMACS tool *gmx pdb2gmx* to generate the topology from a pdb
file.

### Setup residues for pdb2gmx

For this molecule we'll be using the OPLS force field. The force field is
located in the top-level force field directory (probably `/usr/lib/gromacs/top`
or something similar. 

If you're unsure where your GROMACS installation is do:

	echo $GMXPREFIX

If you are properly sourcing the GROMACS configuration file, this will give you
the installation location. Look for the directory `top` in that directory and go
into it.

Let's take a look at the force field directory's its contents:

	cd oplsaa.ff
	ls

You'll see several files, but we're only interested in a few of them for now.
Notice `forcefield.itp`. This is the main file used in simulations. Inside
you'll see a `[ defaults ]` section as well as the inclusion of two other files -
one for bonded interactions and one for non-bonded interactions. We're also
interested in `atomtypes.atp` which gives the descriptions for the cryptic
`opls_####` terms as well as the `aminoacids.rtp` which give a list of
recognized residues used for the *gmx pdb2gmx* command.

Open `atomtypes.atp` (You might need to have root privileges for this):

	vim atomtypes.atp

Go to the line with `opls_138`. Notice the comment says `alkane CH4`, so we are
on the right track here for our methane. However, notice the mass in the second
column - this is only the carbon for a CH4 group, so we also need the
hydrogens. This is an "all atom" model - every atom is represented. The
corresponding hydrogen is `opls_140`. You'll probably also want to look at the
[supporting info of the OPLS force field
paper](http://pubs.acs.org/doi/suppl/10.1021/ja9621760/suppl_file/ja11225.pdf).
The parameters in the paper should match up with the parameters that we'll look
at in a minute. Now make a note of these two atom types and close the file.

Let's take a look at `ffnonbonded.itp` for these two atom types:

	grep opls_138 ffnonbonded.itp
	grep opls_140 ffnonbonded.itp

Here we see the name of the atom type, the bond type, the mass, the charge,
ptype, sigma, and epsilon. Make a note of the charge for each one - we'll need
it for our new residue. As a side note, `ffbonded.itp` will use the bond type
for the bond types, angle types, and dihedral types.

Before continuing, you may want to copy your top-level force field directory
directory somewhere, like your home directory, since we'll be modifying it and
adding some files. To copy it to your home directory do: 

	cp -r /usr/lib/gromacs/top $HOME/GMXLIB

You might have to be root to do it. Now change the `$GMXLIB` environmental variable to:

	export GMXLIB=$HOME/GMXLIB

Add the above to your `.bash_profile` to make it permanent. Now do:

	cd $GMXLIB

You are now in the copy of the director you made, and all simulations will use
that directory instead of the one provided in the GROMACS default directory.

Now open `aminoacids.rtp`. You'll notice several residues already in the file.
We're going to add a new file called `methane.rtp` for our methane with a
residue that we'll call `CH4`. Close `aminoacids.rtp`. We'll need to tell
pdb2gmx the atoms and bonds in the atom in our residue file. We could also tell
it the angles, but we'll leave them out, since *gmx pdb2gmx* will figure it out for
us. You should create [a file that looks like
this](methane.rtp).  

A few notes on the above file: the `[ bondedtypes ]` comes from
`aminoacids.rtp` and is required.  Under `[ atoms ]` you can name them anything
you want, as long as they match the pdb file we are going to create later.
Notice in the first column we gave the
atom names, then we gave the atom types, the charges, and then the charge
group. Under `[ bonds ]` we just tell it how each atom is connected to the
others. In this case, `C` has a connection to each hydrogen. We could
optionally add `[ angles ]`, but as stated earlier, Gromacs will sort this out
for us. Now close the file. See section 5.6 for more information about this.

### Create pdb file and run pdb2gmx

Now we are ready to create the pdb file. There are several programs out there
to create molecule structure files.
[Avogadro](http://avogadro.cc/wiki/Main_Page) is one of those. An alternative
to this is to use "xleap" from the [AmberTools](http://ambermd.org/#AmberTools)
package. In Avogadro simply click any spot in the window and you'll get a
methane. Save this file as `methane.pdb`. Your file should look something like
this. Save this somewhere in your home directory but not anywhere in `$GMXLIB`.

Change `LIG` to `CH4` everywhere in `methane.pdb`. Also change the first `H` to
`H1`, the second to `H2` and so on. PDB files are fixed format, so keep the
beginning of each column in the same place. Also go ahead and change `UNNAMED`
to `Methane`. Your modified file should look [something like
this](methane.pdb). Save the file as `methane-1.pdb`.

Now we can use *gmx pdb2gmx* to create GROMACS .conf and .top files:

	gmx pdb2gmx -f methane-1.pdb

You'll be prompted to choose a force field. Choose OPLS. For the water model
just choose TIP4PEW for now. Three files will be created: `conf.gro`, `posre.itp`,
and `topol.top`. `conf.gro` is our coordinate file, topol.top is the system
topology file, and posre.itp is the optional position restraint file. We won't
be using that one. In the topol.top file notice that there is an `[ angles ]`
section as promised. You'll also want to rename the compound in `topol.top`.
Take a look and explore each file. Chapter 5 of the Gromacs manual will help you
understand the topology file more.

### Solvate system

Our structure file and topology file only have our methane thus far. We need to
add waters by using *gmx solvate*:

	gmx solvate -cp conf.gro -o conf.gro -cs tip4p -p topol.top -box 3.5 3.5 3.5

### Parameter files

We'll be using the same files as in the previous tutorial:

Here are the files we'll be using:

* [Minimization](../1_tip4pew_water/mdp/min.mdp)
* [Minimization 2](../1_tip4pew_water/mdp/min2.mdp)
* [Equilibration](../1_tip4pew_water/mdp/eql.mdp)
* [Equilibration 2](../1_tip4pew_water/mdp/eql2.mdp)
* [Production](../1_tip4pew_water/mdp/prd.mdp)

Simulation
----------

We'll be using the same sequence as last time. This assumes your mdp files are
in a directory named `mdp`:

	gmx grompp -f mdp/min.mdp -o min -pp min -po min
	gmx mdrun -deffnm min

	gmx grompp -f mdp/min2.mdp -o min2 -pp min2 -po min2 -c min -t min -maxwarn 1
	gmx mdrun -deffnm min2

	gmx grompp -f mdp/eql.mdp -o eql -pp eql -po eql -c min2 -t min2
	gmx mdrun -deffnm eql

	gmx grompp -f mdp/eql2.mdp -o eql2 -pp eql2 -po eql2 -c eql -t eql
	gmx mdrun -deffnm eql2

	gmx grompp -f mdp/prd.mdp -o prd -pp prd -po prd -c eql2 -t eql2
	gmx mdrun -deffnm prd

Tip: You may want to put the above commands in a bash script called `run`. Add
the following lines to the top of the script:

	#!/bin/bash

	set -e

Then do:

	chmod +x run

To run the script do:

	./run

Your script should look [like this](run). `set -e` tells bash to stop the script
if there is an error.

Analysis
--------

Let's calculate something called the [radial distribution
function](https://en.wikipedia.org/wiki/Radial_distribution_function). First, we
need to create an index file:

	gmx make_ndx -f conf.gro

At the prompt do:

	a C
	a OW
	q

Now run *gmx rdf*:

	gmx rdf -f prd.xtc -n index.ndx -xvg none

At the prompt select `C` for the reference group. Then select `OW`. A plot of
the result should look something like this:

[C-OW RDF](rdf.png)
