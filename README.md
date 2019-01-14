# Cascade Trajectory

Fortran 90 code for calculating the auto-correlations of a three-level atom. 

* *cross_correlation.f90* is the actual code to be worked on
* *cross_correlation_matrix.f90* is a matrix version of the code to be worked on
* *plot_correlation_traj.py* is a python code using `numpy` and `matplotlib.pyplot` to plot the results

## Benchmark test case

The benchmark test cases use the same source code files mentioned above. The difference is that a
preprocessor directive is passed at compile time to enable the tests (`-DTESTCASE`). This
directive does the following:

* disable the usual (useful) output files
* write a new output file that can be compared to a reference version, to check results did not change
* uses a fixed random seed, so that the results are reproducible

Test reference files, run scripts and so on are located in the *test* directory.

Different compilers will require different reference files for comparison, since the random number
generators are likely to be different between compilers. Also, if parameters are changes, new reference
files will need to be created.

Check the [README.md](test/README.md) in the *test* directory too.

## Building the code

CMake and a Fortran compiler (e.g. `gfortran` or `ifort`) are required to build the code.

### Mahuika

#### Intel Compiler

From the directory this *README.md* file is located in, run:

```
module load CMake intel
mkdir build-intel && cd build-intel  # the directory can be called anything you like

# build with default options
FC=ifort cmake ..

# compile using Intel compiler
make VERBOSE=1
```

#### Cray compiler

From the directory this *README.md* file is located in, run:

```
module load CMake PrgEnv-cray craype-broadwell
mkdir build-cray && cd build-cray  # the directory can be called anything you like

# build with default options
FC=ftn cmake ..

# compile using Intel compiler
make VERBOSE=1
```

#### GNU compiler

From the directory this *README.md* file is located in, run:

```
module load CMake intel
mkdir build-gnu && cd build-gnu  # the directory can be called anything you like

# build with default options
FC=gfortran cmake ..

# compile using Intel compiler
make VERBOSE=1
```

#### Running tests

To run the shorter test cases:

```
ctest -R short -V
```

There are two shorter test cases, one for the original version and one for the matrix
version.

To list all available test cases run: `ctest -N`. Then `ctest -R <regular-expression> -V`
can be used to run tests that match the given regular expression.

There is a longer test case that is used for benchmarking, e.g.:

```
ctest -R long -V
```
