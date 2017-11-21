# Installing ORCA

## Installation requirements

In order to use ORCA you need:

* `gcc` and `g++`
* MATLAB/Octave, including `mex`.

## Download and compile ORCA dependencies

To download ORCA you can simply clone this GitHub repository by using the following commands:
```bash
$ git clone https://github.com/ayrna/orca
```
All the contents of the repository can also be downloaded from the GitHub site by using the "Download ZIP" button.

ORCA is programmed in MATLAB, but many of the classification algorithms are implemented in C/C++. Because of this, these methods have to be compiled and/or packaged into the corresponding `mex` files.

## Quick install from MATLAB/Octave console

In Windows and GNU/Linux you can build ORCA directly from MATLAB/Octave console. Just enter in `scr` directory and type `make`. Alternatively you can clean the objects files with `make clean`

```MATLAB
>> cd src/Algorithms
>> make
```

### Instalation testing

We provide basic tests to test all the algorithms both using ORCA's API and experiments scripts to run experiments.

To run basic tests (running time is ~12 seconds):

```MATLAB
>> cd src/
>> runtestssingle
...
.........................
Performing test for SVORLin
Accuracy Train 0.262222, Accuracy Test 0.266667
Test accuracy matchs reference accuracy
Processing svrTest.m...
.........................
Performing test for SVR
Accuracy Train 0.995556, Accuracy Test 0.973333
Test accuracy matchs reference accuracy
All tests ended successfully
```

To run script tests (running time is ~123 seconds):

```MATLAB
>> cd src/
>> runtestscv
...
Running experiment exp-svr-real1-toy-1
Processing Experiments/exp-2017-11-16-13-59-1/exp-svr-real1-toy-1
Calculating results...
Experiments/exp-2017-11-16-13-59-1/Results/toy-svr-real1/dataset
Experiments/exp-2017-11-16-13-59-1/Results/toy-svr-real1/dataset
Test passed for svr
All tests ended successfully
```

## Detailed build and troubleshooting

### Build from GNU/Linux terminal

Under GNU/Linux, the simplest way to compile all the algorithms is to use the [Makefile](../src/Makefile) included in ORCA. This will compile all the algorithms and clean intermediate object files. You need to properly setup MATLABDIR variable inside [Makefile](../src/Makefile) to point out to MATLAB installation directory (for instance `MATLABDIR = /usr/local/MATLAB/R2017b`):

```bash
$ cd src/
$ make
```
To build ORCA for Octave the setup OCTAVEDIR to point out to Octave heather files (for instance `OCTAVEDIR = /usr/include/octave-4.0.0/octave/`) and type:
```bash
$ cd src/
$ make octave
```

### Windows build

**Octave** installation in Windows:

Default Octave installation provides `mex` command pre-configured with `MinGW`.

1. Inside Octave's console, run `make` in folder `src\Algorithms`
1. From `src` run `runtestssingle` to check the instalation.


**MATLAB** installation in Windows:

1. Install a [supported compiler](https://es.mathworks.com/support/compilers.html). The easier way is to use the "Add-ons" assistant to download
and install [MinGW](http://es.mathworks.com/help/matlab/matlab_external/install-mingw-support-package.html).
1. Test [basic C example](https://es.mathworks.com/matlabcentral/fileexchange/52848-matlab-support-for-mingw-w64-c-c++-compiler) to ensure `mex` is propertly working.
1. Inside MATLAB's console, run `make` in folder `src\Algorithms`
1. From `src` run `runtestssingle` to check the instalation.

We provide binaries and *dlls* for 'ORBoost' to avoid the *complex* build task in Windows. Make will unpak all the binary files. If you need to compile your own binaries, these are the steeps.

1. Install [w64-mingw32](https://mingw-w64.org).
1. Open a terminal by presing Windows key and type `cmd.exe`.
1. Set Windows path to your `w64-mingw32` installation binaries dir, for instance:
```
set PATH=C:\Program Files\mingw-w64\x86_64-7.2.0-posix-seh-rt_v5-rev0\mingw64\bin;"%PATH%"
```
1. Move to directory `orca\src\Algorithms\orensemble\orensemble`.
1. Run `mingw32-make.exe Makefile.win all`.

### Fixing compilation errors

If the command fails, please edit the files `src/Algorithms/libsvm-rank-2.81/matlab/Makefile`, `src/Algorithms/libsvm-weights-3.12/matlab/Makefile`, `src/Algorithms/SVOREX/Makefile` and `src/Algorithms/SVORIM/Makefile`. Make sure that the variable `MATLABDIR` is correctly pointing to the folder of your Matlab installation (by default, `/usr/local/matlab`). You can also make a symbolic link to your current Matlab installation folder:
```bash
$ sudo ln -s /path/to/matlab /usr/local/matlab
```
The following subsections provides individual instructions for compiling each of the dependencies in case the [Makefile](../src/Algorithms/Makefile) still fails or for those which are working in other operating systems.

#### libsvm-weights-3.12

These instructions are adapted from the corresponding README of `libsvm`. First, you should open MATLAB console and then `cd` to the directory `src/Algorithms/libsvm-weights-3.12/matlab`. After that, try to compile the `MEX` files using `make.m` (from the Matlab console):
```MATLAB
>> cd src/Algorithms/libsvm-weights-3.12/matlab
>> make
```

These commands could fail (especially for Windows) if the compiler is not correctly installed and configured. In those cases, please try `mex -setup` to choose a suitable compiler for `mex`. Make sure your compiler is accessible and workable. Then type `make` to start the installation.

On GNU/Linux systems, if neither `make.m` nor `mex -setup` works, please use `Makefile`, typing `make` in a command window. Please change MATLABDIR in Makefile to point the directory of Matlab (usually `/usr/local/matlab`).

#### libsvm-rank-2.81

To compile this dependency, the instructions are similar to those of `libsvm-weights-3.12` (from the Matlab console):
```MATLAB
>> cd src/Algorithms/libsvm-rank-2.81/matlab
>> make
```

#### SVOREX and SVORIM

For both algorithms, please use the `make.m` file included in them (from the Matlab console):
```MATLAB
>> cd src/Algorithms/SVOREX
>> make
>> cd ..
>> cd SVORIM
>> make
```

#### orensemble

We have not prepared a proper MEX interface for ORBoost, so the binary files of this algorithm should be compiled and are then invoked directly from Matlab. For compiling the ORBoost algorithm, you should uncompress the file `orsemble.tar.gz` and compile the corresponding source code. In GNU/Linux, this can be done by (from the `bash` console):
```bash
$ cd src/Algorithms/orensemble
$ tar zxf orensemble.tar.gz
$ cd orensemble/
$ make
g++ -Ilemga-20060516/lemga -Wall -Wshadow -Wcast-qual -Wpointer-arith -Wconversion -Wredundant-decls -Wwrite-strings -Woverloaded-virtual -D NDEBUG -O3 -funroll-loops -c -o robject.o lemga-20060516/lemga/object.cpp
...
```
Then, you should move the binary files to `..` folder and clean the folder (from the `bash` console):
```bash
$ mv boostrank-predict ../
$ mv boostrank-train ../
$ cd ..
$ rm -Rf orensemble
```