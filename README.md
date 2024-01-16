# NOMAD 3.9.1 with CMake

This is a highly customized version of [NOMAD 3.9.1](https://www.gerad.ca/nomad/) for the purpose of working categorical variables built on the crossplatform build tool CMake.


# Description

NOMAD is a C++ implementation of the Mesh Adaptive Direct Search (MADS)
algorithm, designed for constrained optimization of black-box functions.


# Batch or Library mode :

NOMAD is designed to be used in two different modes : batch and library.
The batch mode is intended for a basic and simple usage of the MADS method,
while the library mode allows more flexibility.
For example, in batch mode, users must define their separate black-box program,
that will be called with system calls by NOMAD.
In library mode, users can define their black-box function as C++ code
that will be directly called by NOMAD, without system calls and temporary files.


# Executables

On Windows and Mac OS X platforms, NOMAD batch mode executable is located in
directory `$NOMAD_HOME/build/bin` or `%NOMAD_HOME%\build\bin`.
In order to avoid compiling the code, you can simply use this executable.

# Compilation

On Linux, Unix, and Mac OS X, NOMAD can be compiled using the CMake file as follows:

make a directory called `build`

```bash
cd build
cmake -S .. -B Release
cmake --build Release --config Release
cmake --install Release
```


# Execute NOMAD

For informations about the execution of NOMAD, please read the user guide:

[https://www.gerad.ca/NOMAD/Downloads/user_guide.pdf](https://www.gerad.ca/NOMAD/Downloads/user_guide.pdf)
