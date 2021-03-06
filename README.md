# Defect_calc_tool

Python scripts to conveniently manage structure files for defect calculations in the scheme of density functional theory calculations with VASP. These scripts use atom labeling consistent with [VESTA](https://jp-minerals.org/vesta/en/), where each atom in POSCAR file is labeled with its element and number such as Si1, O1, and H1.

## Requirements
While some scripts are coded only with standard python packages (like *numpy, pandas, and sys*), the other scripts rely on *Structure* module of [pymatgen](https://pymatgen.org/).

## List of scripts
The description of each script assumes POSCAR file of SiO<sub>2</sub> (alpha-quartz). In total 9 atoms (3 silicon atoms and 6 oxygen atoms) are included, which are labeld as Si1, Si2, Si3, O1, O2, O3, O4, O5, and O6.


### coordinate.py \[POSCAR\] \[Atom1\] \[optional: Atom2 etc.\] 
* get the line number in POSCAR and coordinates of atoms.

```
$ python coordinate.py POSCAR Si1 O1
   Atom label | Line # |     x       y       z
   ───────────┼────────┼─────────────────────────
          Si1 |     9  |  0.4782  0.0000  0.6667
           O1 |    12  |  0.4178  0.2526  0.7987
```


### neighbors.py \[POSCAR\] \[Atom1 or coords\] \[optional: Atom2 or coords etc.\]
* get the neighboring atoms of each atom given as arguments
* (optional): *r = XX* will show you atoms with radius of *XX*. Default *XX* is 2.5 angstrom.
* Instead of atom label, coordinates can be given like followings:
	* 0.1,0.1,0.1
	* (0.1,0.1,0.1)
	* [0.1,0.1,0.1]

```
$ python neighbors.py Si1 0.1,0.1,0.1
   Atom label |     x       y       z    | Distance
  ────────────┼──────────────────────────┼──────────
     Si1      |  0.4782  0.0000  0.6667  |  -----
      └ O5    |  0.1652  0.7474  0.5346  |  1.596
      └ O1    |  0.4178  0.2526  0.7987  |  1.596
      └ O6    |  0.5822  0.8348  0.8679  |  1.599
      └ O2    |  0.7474  0.1652  0.4654  |  1.599
     coords   |  0.1000  0.1000  0.1000  |  -----
      └ O6    |  0.5822  0.8348  0.8679  |  2.104
      └ O3    |  0.8348  0.5822  0.1321  |  1.995
      └ Si3   |  0.5218  0.5218  0.0000  |  2.452
      └ O1    |  0.4178  0.2526  0.7987  |  2.050
      └ O4    |  0.2526  0.4178  0.2013  |  1.967
```


### perturb.py \[POSCAR\] \[Atom or coords\] \[optional: r=XX\]
* randomly perturb the atom positions around specific atom or position.
* (optional): *r = XX* will show you atoms with radius of *XX*. Default *XX* is 3.5 angstrom, which typically covers up to second nearest neighbors
* Instead of atom label, coordinates can be given like followings:
	* 0.1,0.1,0.1
	* (0.1,0.1,0.1)
	* [0.1,0.1,0.1]

```
$ python perturb.py POSCAR Si1 r=3
   Atom label |     x       y       z    | Distance
   ───────────┼──────────────────────────┼──────────
    Before    | Space group: P3_221      |
     Si1      |  0.4782  0.0000  0.6667  |  ------
      └ O5    |  0.1652  0.7474  0.5346  |  1.5961
      └ O1    |  0.4178  0.2526  0.7987  |  1.5961
      └ O6    |  0.5822  0.8348  0.8679  |  1.5987
      └ O2    |  0.7474  0.1652  0.4654  |  1.5987
   ───────────┼──────────────────────────┼──────────
    After     | Space group: P1          |
     Si1      |  0.4782  0.0000  0.6667  |  ------
      └ O5    |  0.1545  0.7532  0.5316  |  1.6271
      └ O1    |  0.4133  0.2435  0.8056  |  1.5874
      └ O6    |  0.5820  0.8333  0.8793  |  1.6462
      └ O2    |  0.7553  0.1583  0.4590  |  1.6429

  Perturbed structure was written as PERTURBED_POSCAR
```


### compare_POSCAR.py \[POSCAR1\] \[POSCAR2\] \[optional: t=0.01\]
* Compare two POSCAR files with equivalent lattice but different atomic sites. This script will be effective when you compare two structures relaxed with different charge states.
* The atoms are printed in order of larger to smaller displacements.
* (optional): *t = XX* will show you atoms displaced more than *XX* angstrom. Default *XX* value is 0.01.

```
$ python compare_POSCAR.py POSCAR PERTURBED_POSCAR t=0.01
              | Thres.= 0.01 | POSCAR                   | PERTURBED_POSCAR         |
   Atom label | Displ. (Ang) |     x       y       z    |     x       y       z    |
   ───────────┼──────────────┼──────────────────────────┼──────────────────────────┤
           O5 |    0.0738    |  0.1652  0.7474  0.5346  |  0.1545  0.7532  0.5316  |
           O2 |    0.0725    |  0.7474  0.1652  0.4654  |  0.7553  0.1583  0.4590  |
           O6 |    0.0625    |  0.5822  0.8348  0.8679  |  0.5820  0.8333  0.8793  |
           O1 |    0.0540    |  0.4178  0.2526  0.7987  |  0.4133  0.2435  0.8056  |
```


### selective_dynamics.py \[input_POSCAR\] \[optional: out_POSCAR_name\]
* Change tags for [selective dynamics](https://www.vasp.at/wiki/index.php/POSCAR), like 'T T T' and 'F F F' of each atoms in POSCAR
* This script will ask you for the atoms and tag you want to change
* Ex) If you want to fix all of atoms except for several atoms, run twice as 1) change all tags to FFF, and 2) change some atoms to TTT
* With the second argument you can type the output POSCAR name you want. The default output filename is *SelDy_TAG_ATOMS.vasp*

```
$ python selective_dynamics.py PERTURBED_POSCAR
   For which atoms do you want to apply this tag?
   Separate with spaces: ex) 'Si1 Si2 O1 O3'
   For all atoms, write 'All'
All
   Which tag do you want to apply for selective dynamics?
   ex) TTT, FFF, FFT
FFF

   All atoms selective dynamics tag is set to FFF

   POSCAR is written with this filename: SelDy_FFF_All.vasp

$  python selective_dynamics.py SelDy_FFF_All.vasp
   For which atoms do you want to apply this tag?
   Separate with spaces: ex) 'Si1 Si2 O1 O3'
   For all atoms, write 'All'
Si1
   Which tag do you want to apply for selective dynamics?
   ex) TTT, FFF, FFT
TTT

   Modified atom list
   Atom label |     x       y       z   | tag
   ───────────┼─────────────────────────┼─────
          Si1 |  0.4782  0.0000  0.6667 | TTT

   POSCAR is written with this filename: SelDy_TTT_Si1.vasp
```
