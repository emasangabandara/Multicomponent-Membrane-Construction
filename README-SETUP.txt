Prepare the initial configuration with packmol

packmol < Packmol.inp

Generate the simulation box

editconf_mpi -f initial.pdb -box a b c -c -d 0.0 -o step5_charmm2gmx.pdb
Keep a b c  close to x,y,z dimension of the initial pdb

Prepare the .top file in the exact order order of the components in the initial pdb file

Include both old and new CHARMM36.itp files (new itp contains some AMBER slipid parametrs relevent for glycolipids
Remove 'deafult' from one of the CHARMM36.itp files

Create a reastraint file for GM (model it based on CHL1).
Edit the GLIP.itp file to include the path to the restraint file

Prepare and index file for the Membrane [ MEMB ] and solvent and ions [ SOL_ION ]
grompp_mpi -f step6.0_minimization.mdp -o index.tpr -c step5_charmm2gmx.pdb -p topol.top -maxwarn -1

make_ndx_mpi -f index.tpr -o index.ndx


Energy minimization and equlibrations based on CHARMM-GUI protocols
Extended minimization (500000 steps)
#step 6.0
grompp_mpi -f step6.0_minimization.mdp -o step6.0_minimization.tpr -c step5_charmm2gmx.pdb -n index.ndx -p topol.top -maxwarn -1
mpirun -np 16 mdrun_mpi -deffnm step6.0_minimization

#step 6.1
grompp_mpi -f step6.1_equilibration.mdp -o step6.1_equilibration.tpr -c step6.0_minimization.gro -r step5_charmm2gmx.pdb -n index.ndx -p topol.top -maxwarn -1
mpirun -np 16 mdrun_mpi -deffnm step6.1_equilibration

grompp_mpi -f step6.2_equilibration.mdp -o step6.2_equilibration.tpr -c step6.1_equilibration.gro -r step5_charmm2gmx.pdb -n index.ndx -p topol.top -maxwarn -1
mpirun -np 16 mdrun_mpi -deffnm step6.2_equilibration