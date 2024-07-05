# petsc-docker

Ubuntu docker image with PETSc installed.

- PETSc version: 3.21.3
- 64-bit real, 32-bit int
- with python bindings (`petsc4py`)
- with external packages MUMPS, SUPERLU_DIST, SCALAPACK, HYPRE, SUITESPARSE, SCOTCH, METIS, SPAI

*Inspired by https://github.com/FEniCS/dolfinx/blob/main/docker/Dockerfile.test-env*

# Building

Execute
```shell 
docker build -t petsc-real .
```

# Usage

Execute
```shell
docker run -ti petsc-real
```
