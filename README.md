# petsc-docker

Ubuntu docker image with PETSc installed.

- PETSc version: 3.21.3
- 64-bit real, 32-bit int
- with python bindings (`petsc4py`)
- with external packages MUMPS, SUPERLU_DIST, SCALAPACK, HYPRE, SUITESPARSE, SCOTCH, METIS, SPAI

*Setup adapted from https://github.com/FEniCS/dolfinx/blob/main/docker/Dockerfile.test-env*

## Usage

Create a container from the latest published image, execute
```shell
docker run -ti ghrc.io/dajuno/petsc-docker:main
```
For a list of available versions, see https://github.com/dajuno/petsc-docker/pkgs/container/petsc-docker/versions

## Building

To build the image locally, execute 

```shell
cd docker
docker build -t petsc-real .
```

Create a container from the local image with
```shell
docker run -ti petsc-real
```
