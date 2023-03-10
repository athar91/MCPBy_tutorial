# MCPBy_tutorial
#1. make a cleaned and renumbered pdb with metal (using pdb4amber)

**2. export metal coordinate in ca.pdb file**

**3. generate mol2 file with charge (-c *)**
metalpdb2mol2.py -i ca.pdb -o mol2 ca.mol2 -c 1

**4. Run MCPBYMCPB.py **

MCPBYMCPB.py -i input.in -s 1

********input.in**********

original_pdb protein_clean.pdb

group_name 4ZF6

cut_off 2.8

ion_ids **

software_version g16

ion_mol2files ca.mol2

***********
you will get multiple gaussian input files.

**5. Run gaussian for small_opt**

g09 < MOL_small_opt.com > MOL_small_opt.log

g09 < MOL_small_fc.com > MOL_small_fc.log

formchk MOL_small_opt.chk MOL_small_opt.fchk

Perform the Merz-Kollman RESP charge calculation for the large model:


g09 < MOL_large_mk.com > MOL_large_mk.log

MCPB.py -i input.in -s 2
MCPB.py -i input.in -s 3
MCPB.py -i input.in -s 4

tleap -s -f MOL_tleap.in > MOL_tleap.out #you might solvate or change the box inside the tleap file 

run parmed to convert it into gromacs

************
import parmed as pmd

parm = pmd.load_file('MOL_solv.prmtop','MOL_solv.inpcrd')

parm.save('protein_qm_prep.itp',format='gromacs')

parm.save('protein_qm_prep.gro')

parm.save('protein_qm_prep.top') #,format='gromacs')

*************
There is a bug in Gaussian09 rev B.01 when doing the Merz-Kollman population analysis. Please following the "Gaussian 09 fix" section in the Bug Fix Webpage to solve the problem.
#You might consider changing the basis set based on metal, you can use pseudopotential. lanl2dz can be a perfect choice.

https://ambermd.org/tutorials/advanced/tutorial20/mcpbpy_heme.htm
