# pyscf2fchk
A python module to generate a fchk file from a PySCF calculation object. This module works for the following type of PySCF calculations: HF/KS, 
ROHF/ROKS, UHF/UKS, DFTD3, FCI, CCSD, CASSCF

# Features

This module contains two different functions:
- write_fchk(): Writes standard FCHK file using information stored in a PySCF object calculation
- write_dm12(): Generates `dm1` and `dm2` files containing  spin resolved rdm1 and rmd2. This function is only available for 
a CASSCF PySCF calculation.

The fchk wavefunction file generated will have the following features:

- **General information**: Number of atoms, Info1-9, Charge, Multiplicity, Number of electrons, Number of alpha electrons, 
Number of beta electrons, Number of basis functions, Number of independent functions, Atomic numbers, Nuclear charges,
Current cartesian coordinates.
- **Basis set information**: Number of contracted shells, Number of primitive shells, Pure/Cartesian d shells,Pure/Cartesian f shells
Highest angular momentum, Largest degree of contraction, Shell types, Number of primitives per shell, Shell to atom map, Primitive exponents.
Contraction coefficients, Coordinates of each shell, Coordinates of each shell.
- **Calculation/Wavefunction information**: Virial Ratio, SCF Energy, Total Energy, S^2, RMS Density, Job Status, External E-field,
Number of CAS Electrons, Number of CAS Orbitals, Alpha MO coefficients, Beta MO coefficients, Total SCF Density, Spin SCF Density.
- **Extra Energy terms**: Kinetic Energy, Electron-Nuclei Energy, Electron-Electron Energy, Coulomb Energy, Exact-exchange Energy,
DFT-exchange Energy, Total Exchange Energy.

# Dependencies

- [PySCF](https://github.com/pyscf/pyscf)


# Installation

- PySCF installation:

It is recommended to use a conda enviroment to use PySCF, regardless of using the conda installed PySCF or source code. To create anew conda enviroment:

```
conda create --name pyscf_env python=3.9
```

To install it using pip (Recommended option in PySCF Installation guide)

Simplest version
```
conda activate pyscf_env
pip install pyscf
```
All extensions version

```
conda activate pyscf_env
pip install pyscf[all]
```

For alternative installation options [see](https://pyscf.org/install.html#installation-with-conda)

- pyscf2fchk module installation

```
mkdir ~/pyscf2fchk
cd ~/pyscf2fchk
git clone git@github.com:psalse/pyscf2fchk.git (or download/uncrompess zip file)
```

Add to your .bashrc:

`export PYTHONPATH=~/pyscf2fchk/pyscf2fchk:$PYTHONPATH` (or change the path to where the `pyscf2fchk` module is)

# Example

```
from pyscf import gto, scf, mcscf
import apost3d as apost


molname='molecule'
mol = gto.M(atom = [
            [8,( 0, 0, 0)],
            [8, (0, 0, 1.2)] ], basis = 'ccpvdz')
mol.ms = 1 # spin quantum number: triplet with ms=1
mol.charge = 0
mol.cart= True
mol.max_memory = 4000

# start with reference RHF calculation.
myhf = scf.RHF(mol)
myhf.kernel()

#then full-valence CAS TRIPLET STATE with 6 orbitals, 8 electrons
mycas = mcscf.CASSCF(myhf, 6, 8)
mycas.kernel()

#usage of pySCF to apost utilities (generating fchk and dm1/dm2 files)
s = myhf.get_ovlp()
apost.write_fchk(mol, mycas, molname, s)
apost.write_dm12(mol, mycas, molname)

```
