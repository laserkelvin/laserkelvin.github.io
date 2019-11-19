---
title: A Case for Abstraction in Scientific Programming
date: 2019-11-18 21:17
category: programming
tags: [python, programming, data analysis]
teaser: The solution to many routine data analysis tasks is to create classes that abstract both the data and the analysis we perform, making data analysis much more easily reproducible and maintainable.
---

Data analysis usually (read: should) follow some consideration about what the objective of the analysis is, and what kind of transformations to a dataset is required to get to the objective. As part of planning, one needs to think as far ahead as possible as to what kind of information needs to be propagated throughout your analysis pipeline, which will inevitably evolve with your objectives and your methodology.

One process I typically go through when attempting to structure my data set is to write a class - by thinking about our problem from an object-oriented perspective, we:

1. Transfer abstract concepts into executable code
2. Minimize repetition for commonly used code
3. Document expected data types and transformations

To demonstrate this, I'll use some of my own data analysis as a concrete example. Some of the data I deal with involves parsing a combination of structured and unstructured data outputs from quantum chemical calculations: the goal of these calculations is to determine some properties of molecules completely completely from first principles using only fundamental constants. The results of these calculations inform chemists, physicists, biologists - and any other disciplines that work molecules - important aspects about their reactivity, 

~~~ python
@dataclass
class CalculationOutput:
    data_id: int = 0                        # Unique integer used to identify the calculation
    filepath: str = ""                      # Filepath to the output file for checking
    coordinates: np.ndarray = np.empty(1)   # Array that holds Cartesian coordinates of the molecule
    energy: float = 0.                      # Electronic energy
    dipoles: np.ndarray = np.zeros(3)       # Array holding dipole moments along each axis
~~~

The specifics on each of the data fields don't actually matter - instead, focus on how each field is annotated and given default values. The annotations are important because it tells you, the reader, what kind of data is expected for each field, and allows for anyone reading the code to quickly deduce what the code and analysis should expect for a particular field. The default values are also important, because it allows for the code to be run and tested, and later during the validation process identify fields that failed to parse.

---