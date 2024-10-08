# Microscopic calculation of Hubbard parameters for quantum gas microscopes
A package for the calculation of the Hubbard on-site interaction U for cold atoms in optical lattices. This package is written in *Mathematica* and is currently applicable to quasi-1D and (square) quasi-2D optical lattices. 

## How it works

The Hubbard U term is calculated such that the Hubbard scattering amplitude reproduces the exact scattering amplitude for two atoms in the limit of vanishing relative momentum. This packages automates the calculation.

For more details please see the associated research paper freely available on the arXiv [arxiv link].

 ## Using the package
 
 ### Getting started
 To begin, decide on the relevant package:
 - **1DHubbardParameters** for quasi-1D optical lattices
 - **2DHubbardParameters** for quasi-2D (square) optical lattices

Download the relevant package and then the package can then be loaded into a Mathematica notebook using, e.g.,
```
Get[<path-to-1DHubbardParameters.wl>];
```

### Units
In this package the units are set by `m=hbar=d=1` with
* `m` the atom mass,
* `hbar` the reduced Planck's constant
* `d` the lattice spacing.


### Quasi-1D optical lattice
The key function in quasi-1D is `setupHubbardUQuasi1D[vup, vdown, pOnShell, omegaperp, convParam]` which outputs the Hubbard U as a function of `a1dinv` (the inverse 1D scattering length). The inputs of the function are:
* `vup`: depth of the optical lattice for the spin-up atoms [in units of `1/md^2`]
* `vdown`: depth of the optical lattice for the spin-down atoms [in units of `1/md^2`]
* `convParam`: convergence parameter for the integrals and sums, which takes on any non-zero integer (typically a value < 5 will suffice)
* `pOnShell`: relative quasi-momentum for equating the exact and Hubbard scattering amplitudes (must be taken as a limit to zero, but values around 0.05 typically suffice) [in units of `1/d`]
* `omegaperp` the trapping frequency of the 2D harmonic confinement [in units of `1/md^2`].

The following is an example of using the function, with experimentally feasible parameters:
```
uFunc1D = setupHubbardUQuasi1D[vup = 12 Vrec, vdown = 12 Vrec, pOnShell = 0.05, omegaperp = 40, convParam = 4]
```
The function `uFunc1D` can now be plotted
```
Plot[
 uFunc1D[a1dinv],
 {a1dinv, -30, 30},
 Frame -> True,
 FrameLabel -> {"d/a1D", "U/m d^2"}
 ]
```
yielding

<img width="373" alt="image" src="https://github.com/HaydnAdlong/cold-atom-hubbard-parameters/assets/93458010/8e301b81-316b-4035-86f9-1c8edb24ae4a">

One can also introduce the 3D and 1D scattering length relationship
```
lperp = 1/Sqrt[omegaperp];
a1dinvFunc[a3d_] := (-2 (lperp/2 (lperp/a3d + Zeta[1/2]/Sqrt[2])))^-1
```
Then, `uFunc1D` can now be plotted as a function of the 3D scattering length
```
Plot[
  uFunc1D[a1dinvFunc@a3d],
  {a3d, -1, 1},
  Frame -> True,
  FrameLabel -> {"a3d/d", "U/m d^2"}
  ]
```
<img width="373" alt="image" src="https://github.com/HaydnAdlong/cold-atom-hubbard-parameters/assets/93458010/e7932fbc-5cce-4647-aeab-62215308e315">

The limit of zero relative quasi-momentum can be tested by reducing the value to `pOnShell` (e.g. `pOnShell = 0.025`), and the convergence can be tested by increasing the value of `convParam` (e.g. `convParam = 5`).

### Quasi-2D optical lattice
The key function in quasi-2D is `setupHubbardUQuasi2D[vupx, vdownx, vupy, vdowny, pOnShellx, pOnShelly, omegaz, convParam]` which outputs the Hubbard U as a function of `loga2dinv` (the log of the inverse of the 2D scattering length). The inputs of the function are the same as the quasi-1D with an additional directional component (e.g. vupx is the depth of the optical lattice for the spin-up atoms along the x-direction), and omegaperp is replaced by omegaz (the trapping frequency of the 1D harmonic confinement).

The calculation in quasi-2D is significantly more computationally demanding than in quasi-1D, and takes more time to complete. We have therefore added in a progress monitor that displays during the calculation.

The following is an example of using the function, with experimentally feasible parameters:
```
uFunc2D = setupHubbardUQuasi2D[vupx = 12 Vrec, vupy = 12 Vrec, vdownx = 12 Vrec, vdowny = 12 Vrec, pOnShellx = 0.05, pOnShelly = 0.05, omegaz = 40, convParam = 3]
```

The function `uFunc2D` can now be plotted
```
Plot[uFunc2D[loga2dinv],
{loga2dinv, -2, 5},
Frame -> True, 
FrameLabel -> {"Log[1/a2D]", "U/m d^2"}
]
```
yielding

<img width="365" alt="image" src="https://github.com/HaydnAdlong/cold-atom-hubbard-parameters/assets/93458010/f69aa257-9f66-44fd-85e9-f9bbb2f644c0">

One can also introduce the 3D and 2D scattering length relationship
```
lz = 1/Sqrt[omegaz];
loga2dinvFunc[a3d_] := Log[(lz Sqrt[\[Pi]/0.905]*Exp[-((Sqrt[\[Pi]] lz)/(Sqrt[2] a3d))])^-1]
```
Then, `uFunc2D` can now be plotted as a function of the 3D scattering length
```
Plot[uFunc2D[loga2dinvFunc@a3d],
{a3d, -1, 1},
Frame -> True, 
FrameLabel -> {"a3d/d", "U/m d^2"}
]
```
<img width="373" alt="image" src="https://github.com/HaydnAdlong/cold-atom-hubbard-parameters/assets/93458010/8e56afbe-8125-48b5-8cd5-25919116bf18">

The limit of zero relative quasi-momentum can be tested by reducing the value to `pOnShellx` and `pOnShelly`, and the convergence can be tested by increasing the value of `convParam` (e.g. `convParam = 4`).

## Additional functionalities
We have reduced the complexity of determining the Hubbard U parameter by wrapping all of the various convergence parameters (e.g. the size of integration grids) into a single parameter we term `convParam`. This of course comes at the price of better user control of the calculation. Users who wish to understand how to control all of the various parameters are welcome to email hadlong@phys.ethz.ch for further instructions and guides. Users who are seeking an additional functionality (such as using the code with a different lattice geometry) are welcome to email any of the authors of the paper.

 ## Citing this package
This package [fill in]
