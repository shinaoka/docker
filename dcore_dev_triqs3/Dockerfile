FROM --platform=linux/x86_64 shinaoka/triqs3_all

# ALPS
WORKDIR /root
RUN git clone -b master --single-branch https://github.com/ALPSCore/ALPSCore.git && \
    mkdir ALPSCore.build && \
    cd ALPSCore.build && \
    cmake -DALPS_CXX_STD=c++14 -DCMAKE_INSTALL_PREFIX=/opt/ALPSCore -DDocumentation=OFF -DALPS_INSTALL_EIGEN=true -DTesting=OFF ../ALPSCore && \
    make install -j 24

# Additional packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential \
    curl \
    ca-certificates \
    git \
    zip \
    vim \
    gosu \
    hdf5-tools \
    libopenblas-base \
    libopenblas-dev \
    libhdf5-103 \
    libhdf5-dev \
    libeigen3-dev \
    cmake \
    pkg-config \
    gfortran \
    openmpi-bin \
    libopenmpi-dev \
    sudo \
    libboost-dev \
    libboost-mpi-dev \
    && \
    apt-get clean && rm -rf /var/cache/apt/archives/* /var/lib/apt/lists/* # clean up

# pomerol
WORKDIR /root
RUN git clone https://github.com/aeantipov/pomerol.git && cd pomerol && git checkout 7a45b6a8b8254dcbf8 && cd .. && \
    mkdir pomerol.build && cd pomerol.build && \
    cmake -DCMAKE_INSTALL_PREFIX=/opt/pomerol -DPOMEROL_COMPLEX_MATRIX_ELEMENTS=ON -DEIGEN3_INCLUDE_DIR=/opt/ALPSCore/deps/eigen ../pomerol && \
    make install && cd .. && rm -rf pomerol.build pomerol

# pomerol2dcore
WORKDIR /root
RUN git clone https://github.com/j-otsuki/pomerol2dcore.git && \
    mkdir pomerol2dcore.build && cd pomerol2dcore.build && \
    cmake -Dpomerol_DIR=/opt/pomerol -DCMAKE_INSTALL_PREFIX=/opt/pomerol2dcore ../pomerol2dcore && \
    make install

# ALPS/CT-HYB-SEGMENT
WORKDIR /root
RUN git clone https://github.com/ALPSCore/CT-HYB-SEGMENT.git && \
    mkdir CT-HYB-SEGMENT.build && cd CT-HYB-SEGMENT.build && \
    cmake -DCMAKE_INSTALL_PREFIX=/opt/CT-HYB-SEGMENT ../CT-HYB-SEGMENT && \
    make install -j 12

# Some common libraries
RUN apt remove python3-terminado -y
RUN pip3 install --upgrade pip setuptools
RUN pip3 install \
        terminado numpy scipy matplotlib ipykernel jupyterlab \
        numpy scipy h5py!=2.10.0 toml dcorelib mpi4py matplotlib mypy pytest \
        sphinx wild_sphinx_theme versioneer

