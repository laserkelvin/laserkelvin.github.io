@def title = "Gaussian Recipes for Rotational Spectroscopy"
@def tags = ["quantum chemistry", "spectroscopy", "bayesian statistics"]
@def isblog = true
@def showall = true

# {{fill title}}

Recently I've had to do a few calculations using Gaussian where I had to keep going back and
waste time to find out how to do something again. I basically got fed up with this, and I'm
organizing this post to more or less create a cheatsheet that tells you how, and which keywords
to use for calculations using Gaussian '09/'16 to do with spectroscopy, and where to look
for the outputs.

## Which method to use

This is entirely a shameless plug, but this question depends on what you want. If you need
to do a lot of calculations quickly, I've benchmarked a bunch of low-cost electronic
structure methods, and used Bayesian statistics to tease out scaling factors. While these
aren't more accurate than state-of-the-art coupled cluster, they are fast and on many occasions
the scaling factors give better estimates of rotational constants than VPT2 (which can take
a long time). Here is a table of scaling factors:

| Method | Basis Set | Mean | Lower bound | Upper bound
|---|---|---|---|---|
| PW6B95-D3 | cc-pVQZ | 0.9816 | 0.9511 | 1.0159 |
| PW6B95-D3 | 6-31+G(d) | 0.9929 | 0.9603 | 1.0296 |
| ωB97X-D | cc-pVQZ | 0.9866 | 0.9560 | 1.0208 |
| M06-2X | cc-pVTZ | 0.9878 | 0.9570 | 1.0226 |
| M05-2X | cc-pVQZ | 0.9848 | 0.9517 | 1.0218 |
| | cc-pVQZ | 0.9867 | 0.9558 | 1.0219 |
| | 6-31+G(d) | 0.9965 | 0.9650 | 1.0323 |
| ωB97X-D | cc-pVTZ | 0.9872 | 0.9546 | 1.0238 |
| ωB97X-D | 6-31+G(d) | 0.9971 | 0.9642 | 1.0340 |

Simply take one of the method/basis above, and multiply your equilibrium rotational constants
by these factors to obtain an effective scaled value to base predictions on: $$A(BC)'_0 = A(BC)_e \times \mathrm{scaling}$$.
The mean scaling factor basically says statistically, on average your equilibrium value is this much off the
experimental constant. The lower and upper bounds set a 95% credible interval, meaning that
your result is contained within this boundary 95% of the time. For more details about the methodology,
[check out my paper](10.1021/acs.jpca.9b09982). _There are similar scaling parameters for dipole moments_.

My recommendation is to use ωB97X-D/6-31+G(d), as it gives the best compromise between speed
and uncertainty. If you have time to kill, ωB97X-D/cc-pVQZ is better, although of course 
if you have enough computing power _and_ time you should run ae-CCSD(T)/cc-pWCVQZ. The bump
in computational method here acts mainly to _decrease_ the uncertainty, and in the case of
the coupled-cluster approach, _improve the accuracy_.


## Default keywords

These are not nominally default, but for all spectroscopic calculations you _should_ include
these:

`Int=Ultrafine` specifies an ultrafine integration grid: this changes the result of every
calculation, and so when you're making comparisons you _need_ to make sure you're operating
with the same integration grid settings.

`SCF=VeryTight` ensures that the SCF convergence is sufficiently good.

`Opt=VeryTight` ensures your geometry converges enough: since all spectroscopic calculations
depend on geometry, you _need_ this.

## Reading in a geometry

Say you are chaining a bunch of serial geometry optimizations, and you're simply modifying the
basis set used. You don't have to constantly copy-paste the coordinates each time by specifying
`Geom=AllCheck` or `Geom=Check`; the former reads the charge and multiplicity as well, while
the latter only takes the geometry and still expects a charge/multiplicity line.

Keep in mind that this can be dangerous if the checkpoint file is corrupted, as you'll lose
the geometry! I would suggest making a copy of the `.chk` file between runs.

## From Gaussian to SPFIT/SPCAT

There's actually an extremely useful keyword that you should have in every calculation;
`Output=Pickett`. This creates a summary of spectroscopic parameters to higher precision
at the end of the output file, and in the format of a `.var` file in the SPFIT format. Additionally,
codings for significant hyperfine nuclei and quartic centrifugal distortion terms are added
if you have nuclei with significant quadrupole couplings in the former, and if you're doing
a harmonic frequency calculation for the latter. To get the whole shebang:

```
%chk=calc.chk
%nprocshared=4
%mem=4GB
# wB97XD/6-31+G* SCF=VeryTight Int=Ultrafine Output=Pickett Freq=VibRot
...
```

This will perform vibration-rotation analysis (to get __harmonic__ $\alpha$ values)

## Changing the representation for parameters

While centrifugal distortion terms are given in both _A_ and _S_ reductions, the representation
is actaully inferred by the calculation and often _not_ the one you want. This will make a
significant difference in the sign—and to a lesser extent magnitude—of your distortion terms
(e.g. $d_1$ and $d_2$ are flipped in sign).

The procedure to change this is carried out after you've done an anharmonic calculation, i.e.
`Freq=Anharm`. You can subsequently provide a `ITop` keyword that then lets you specify
$\mathrm{I_{r/l}-III_{r/l}}$ representations, and have the calculation read in all of the
force constants. _This should not require any significant re-calculation_.

```
%chk=calc.chk
%nprocshared=4
%mem=4GB
# wB97XD/6-31+G* Guess=Read Freq=(ReadFC,ReadAnharm,Anharm) 
SCF=VeryTight Int=Ultrafine Geom=Check
Changing representations of CD terms
0 1
Print=ITop=Ir
```

Where you have a `calc.chk` file containing the previous anharmonic calculation and structure.
The calculation will read in the geometry from the checkpoint file, and the `Print=Itop=Ir`
changes the representation explicitly to $\mathrm{I_r}$. _Note the blank lines!_

## Rare isotopic calculations

Some properties are automatically calculated for isotopologues 

## Extracting hyperfine parameters

Although the `Output=Pickett` keyword formats the result nicely, there are cases where the
output electric properties are not in the right orientation. Whatever you do, _check the
dipole moment values_ to make sure they are what you expect. Here's an example of a goofy result
on the PhC<sub>3</sub>N molecule, which is a near-prolate top with only one permanent dipole moment
along the $a$ axis. Normally, you would put the dipole axis ordering to be $x=a, y=b, z=c$,
however as seen in the following calculation the axes are swapped:

```
Rotational constants (MHZ):
   5733.6904049    574.7286787    522.3680080
 Nuclear quadrupole coupling constants [Chi] (MHz):
 15  N(14)
   aa=     2.5734   ba=     0.0094   ca=    -0.0000
   ab=     0.0094   bb=     2.3852   cb=    -0.0000
   ac=    -0.0000   bc=    -0.0000   cc=    -4.9586
 Dipole moment (Debye):
     -0.0000000     -0.0000000      5.8910287  Tot=      5.8910287
 Atoms with significant hyperfine tensors:  N15-14
```

The consequence is that the quadrupole coupling is also messed up. $\chi_{aa}$ should be
the projection of the nitrogen quadrupole moment along the $a$-axis, and for cyanide groups 
this value is typically around -4.5 MHz, which actually turns out to be labelled $\chi_{cc}$.
So this is a case of Gaussian messing up the labels, and calling the $c$-axis properties $a$.
Chemical unintuition!

---

If you feel like I've left something out, or you have suggestions, feel free to let me know
through an email or tweet!
