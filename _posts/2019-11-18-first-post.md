---
title: A Case for Abstraction in Scientific Programming
date: 2019-11-18 21:17
category: programming
tags: [python, programming, data analysis]
teaser: The solution to many routine data analysis tasks is to create classes that abstract both the data and the analysis we perform, making data analysis much more easily reproducible and maintainable.
---

Data analysis usually (read: should) follow some consideration about what the objective of the analysis is, and what kind of transformations to a dataset is required to get to the objective. As part of planning, one needs to think as far ahead as possible as to what kind of information needs to be propagated throughout your analysis pipeline, which will inevitably evolve with your objectives and your methodology. What this article aims to convey is to convince you that it's always worth taking some time to construct scientific analysis code properly, in the spirit of maintability for you and whomever may have the (mis)fortune of working with it! While these ideas are fairly language agnostic, the examples will be written in Python, which I think is the most natural language for implementing these concepts thanks to its object-oriented nature and readability.

In most scientific programming, the common practice is to write a series of scripts that automate a set of tasks which can inevitably lead to poorly documented, reptitive, and complex code. My recommendation here is to frame the problem into a class that embodies the kind of data we're working with: by defining a class that suits our data and task, we convert our abstract goals into executable code, which is common between all of our datasets (i.e rows of a table).

To demonstrate this, I'll use some of my own data analysis as a concrete example. Some of the data I deal with involves parsing a combination of structured and unstructured data outputs from quantum chemical calculations: the goal of these calculations is to determine some properties of molecules completely completely from first principles using only fundamental constants. The results of these calculations inform chemists, physicists, biologists - and any other disciplines that work with molecules - important aspects about how molecules react and behave. To automate our analysis, the output of these programs need to be parsed somehow; the calcuation output typically involves some semi-structured text file that looks something like this:

>  Iteration Nr.  17 started:
> Generation of Intermediates required (CPU/WALL):     15.17/      76.91 seconds.
> Transformation of t(ab,ij) amplitudes required    0.4 seconds.
> Contraction of t(mu nu,ij) with AO integrals required  392.1 seconds.
> Backtransformation of t(mu nu,ij) increments required    0.8 seconds.
> Construction of Hbar required (CPU/WALL):           399.24/     411.12 seconds.
> Amplitude changes are:
> -------------------------------------------------------------------
>              Spin      RMS          Max.      Max. change for
>  Amplitude   Case     Change       Change       i    j    a    b
> -------------------------------------------------------------------
>     T1        AA    0.0000000000 0.0000000188    7       113
>     T1        BB    0.0000000000-0.0000000300    6       113
>     T2        AA    0.0000000000 0.0000000016    7    6  263  174
>     T2        BB    0.0000000000-0.0000000055    6    5  113   10
>     T2        AB    0.0000000000 0.0000000300    6    6  113   10
> -------------------------------------------------------------------
> The AA contribution to the correlation energy is:   -0.0573370 a.u.
> The BB contribution to the correlation energy is:   -0.0463618 a.u.
> The AB contribution to the correlation energy is:   -0.4880085 a.u.
> The total correlation energy is -0.591707310596 a.u.
> Convergence information after    17 iterations:
> Largest element of residual vector :  0.30018424E-07.
> Largest element of DIIS residual   :  0.22877296E-07.

An output file can include thousands to tens of thousands of lines, depending on how complex your calculation is. The goal of the parsing is to extract some common data between calculations so that we can make some comparisons and post-analysis. The common elements of these calculations include:

1. Termination (success or failure?)
2. Electronic energy
3. Molecular cartesian coordinates
4. Molecular properties

For your own application, these parameters will obviously change. The important thing to note here is that writing out this list forces you to think about what kind of comparisons you might make later - the fewer times you have to re-run your parsing and pipeline routines, the better! In our case, we're interested in whether or not the calculation completed without a hitch, the coordinates that represent where the atoms are in 3D space, and some other numbers that will be used for comparison.

To implement this, I'll be using the `dataclass` decorator that was introduced in the Python standard library as of [Python 3.7 with PEP 557](https://docs.python.org/3/library/dataclasses.html). Essentially, it converts a Python class into something better suited for handling data (as in its namesake) much like `namedtuples`, only better because of the way we define them. Because we're working with classes, these dataclasses can inherit from other classes, as well as define our own methods.

~~~ python
@dataclass
class CalculationOutput:
    energy: float = 0.                      # Electronic energy
    coordinates: np.ndarray = np.empty(1)   # Array that holds Cartesian coordinates of the molecule
    dipoles: np.ndarray = np.zeros(3)       # Array holding dipole moments along each axis
    frequencies: np.ndarray = np.empty(1)   # Array of vibrational frequencies
    formula: str = ""                       # Chemical formula
    smi: str = ""                           # Unique SMILES code
    data_id: int = 0                        # Unique integer used to identify the calculation
    filename: str = ""                      # Filepath to the output file for checking
    ...
~~~

The specifics on each of the data fields don't actually matter - instead, focus on how each field is annotated and given default values. The annotations are important because it tells you, the reader, what kind of data is expected for each field, and allows for anyone reading the code to quickly deduce what the code and analysis should expect for a particular field. The default values are also important, because if anything is failed to be parsed the code will fallback onto some sensible values that can be double-checked later on.

The other advantages of working with classes is our ability to implement methods - this ensures that the same analysis routines are used across all the data, saving us from repeating our code as well as ensuring consistency.

~~~ python

from scipy.constants import physical_constants

@dataclass
class CalculationOutput:
    data_id: int = 0
    ...
    def _rotate_coordinates(self, rotation_matrix):
        self.coordinates = self.coordinates.dot(rotation_matrix)

    def _convert_energy(self, factor=None):
        # Convert from hartrees to another unit; if no value is
        # provided, default to joules
        if not factor:
            factor = physical_constants["atomic unit of energy]
        self.energy *= factor

    def pipeline(self, rotation_matrix, factor=None):
        self._rotate_coordinates(rotation_matrix)
        self._convert_energy(factor)

~~~

Now, each transformation and analysis step is detailed in the very definition of our class - every output file we analyze will go through the same treatment. The definition of a `pipeline` method also lays out each of the steps in our analysis transparently, making it modular and debugging much easier. Even if you're not an expert, you can see that our operations include rotating our Cartesian coordinates into some other projection, and also converting the energy we've parsed out into some other unit system. All in all, everyone benefits from having cleaner code; if Marie Kondo wrote Python, it would spark joy for her.

As a bonus, we can also implement some double under (dunder) methods for our classes that provide some utility to our class:

~~~ python
    ...
    def __eq__(self, other):
        return self.smi == other.smi

    def __subtract__(self, other):
        return self.energy - other.energy

    def __add__(self, other):
        return self.energy + other.energy

    def __repr__(self):
        return f"Calculation #{self.data_id}"
---