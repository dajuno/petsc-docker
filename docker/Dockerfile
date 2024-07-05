FROM  ubuntu:24.04

ARG PETSC_VERSION=3.21.3
ARG OPENMPI_SERIES=5.0
ARG OPENMPI_PATCH=3

ARG PETSC_OPTFLAGS="-O3"

# Number of build threads to use with make
ARG BUILD_NP=4

RUN apt-get -qq update &&  \
    apt-get -yq upgrade && \
    apt-get -y install \
        cmake \
        g++ \
        gfortran \
        liblapack-dev \
        libopenblas-dev \
        python3-dev \
        python3-pip \
        python3-venv \
        git \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN wget https://download.open-mpi.org/release/open-mpi/v${OPENMPI_SERIES}/openmpi-${OPENMPI_SERIES}.${OPENMPI_PATCH}.tar.gz && \
    tar xfz openmpi-${OPENMPI_SERIES}.${OPENMPI_PATCH}.tar.gz  && \
    cd openmpi-${OPENMPI_SERIES}.${OPENMPI_PATCH} && \
    ./configure  && \
    make -j${BUILD_NP} install && \
    ldconfig && \
    rm -rf /tmp/*

ENV VIRTUAL_ENV /petsc-env
ENV PATH /petsc-env/bin:$PATH
RUN python3 -m venv ${VIRTUAL_ENV}

# Install Python packages (via pip) in venv
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir cffi numpy setuptools wheel pytest && \
    pip install --no-cache-dir --no-build-isolation mpi4py


# Install PETSc and petsc4py with real types (64bit real, 32bit int)
ENV PETSC_DIR=/usr/local/petsc SLEPC_DIR=/usr/local/slepc
RUN apt-get -qq update && \
    apt-get -y install bison flex && \
    git clone --depth=1 -b v${PETSC_VERSION} https://gitlab.com/petsc/petsc.git ${PETSC_DIR} && \
    cd ${PETSC_DIR} && \
    ./configure \
    --COPTFLAGS="${PETSC_OPTFLAGS}" \
    --CXXOPTFLAGS="${PETSC_OPTFLAGS}" \
    --FOPTFLAGS="${PETSC_OPTFLAGS}" \
    --with-64-bit-indices=no \
    --with-fortran-bindings=no \
    --with-shared-libraries \
    --download-hypre \
    --download-metis \
    --download-mumps \
    --download-ptscotch \
    --download-scalapack \
    --download-spai \
    --download-suitesparse \
    --download-superlu \
    --download-superlu_dist \
    --with-scalar-type=real \
    --with-precision=double && \
    make ${MAKEFLAGS} all && \
    # Install petsc4py
    cd src/binding/petsc4py && \
    pip install --no-cache-dir cython wheel && \
    pip -v install --no-cache-dir --no-build-isolation . && \
    # pip3 uninstall --yes cython wheel && \
    # Cleanup
    apt-get -y purge bison flex && \
    apt-get -y autoremove && \
    apt-get clean && \
    rm -rf \
    ${PETSC_DIR}/**/tests/ \
    ${PETSC_DIR}/**/obj/ \
    ${PETSC_DIR}/**/externalpackages/  \
    ${PETSC_DIR}/CTAGS \
    ${PETSC_DIR}/RDict.log \
    ${PETSC_DIR}/TAGS \
    ${PETSC_DIR}/docs/ \
    ${PETSC_DIR}/share/ \
    ${PETSC_DIR}/src/ \
    ${PETSC_DIR}/systems/ \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN useradd --create-home --shell /bin/bash petsc
RUN chown -R petsc:petsc /petsc-env

USER petsc
WORKDIR /home/petsc
ENV SHELL /bin/bash
ENTRYPOINT ["/bin/bash"]
