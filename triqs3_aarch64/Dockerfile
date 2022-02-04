FROM ubuntu:jammy

RUN apt-get update && \
    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      build-essential \
      vim \
      gosu \
      g++ \
      gfortran \
      git \
      hdf5-tools \
      libblas-dev \
      libboost-dev \
      libfftw3-dev \
      libgmp-dev \
      libhdf5-dev \
      liblapack-dev \
      libopenmpi-dev \
      python3-dev \
      \
      clang \
      libclang-dev \
      python3-clang \
      \
      libnfft3-dev \
      \
      sudo \
      openssh-client \
      python3-pip \
      python3-setuptools \
      python3-tk \
      jupyter-notebook \
      python3-mako \
      python3-numpy \
      python3-scipy \
      python3-h5py \
      python3-mpi4py \
      python3-matplotlib \
      cmake \
      && \
    apt-get autoremove --purge -y && \
    apt-get autoclean -y && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/*

# Install TRIQS
WORKDIR /root
RUN git clone -b unstable https://github.com/TRIQS/triqs.git --depth=1 triqs.src && \
    mkdir triqs.build && cd triqs.build && \
    cmake \
    -DCMAKE_C_COMPILER=mpicc \
    -DCMAKE_CXX_COMPILER=mpicxx \
    -DCMAKE_INSTALL_PREFIX=/opt/triqs \
    -DCMAKE_VERBOSE_MAKEFILE=ON \
    -DBuild_Documentation=OFF \
    -DBuild_Tests=OFF \
    ../triqs.src && \
    make -j 6 install && \
    cd .. && rm -rf *.build *.src


# Install TRIQS/DFTTools
RUN . /opt/triqs/share/triqs/triqsvars.sh && \
    git clone -b unstable https://github.com/TRIQS/dft_tools.git --depth=1 dft_tools.src && \
    mkdir dft_tools.build && \
    cd dft_tools.build  && \
    cmake -DCMAKE_CXX_COMPILER=mpicxx ../dft_tools.src && \
    make install && \
    cd .. && rm -rf *.build *.src


# Install TRIQS/HubbardI
RUN . /opt/triqs/share/triqs/triqsvars.sh && \
    git clone -b unstable https://github.com/TRIQS/hubbardI.git --depth=1 hubbardI.src && \
    mkdir hubbardI.build && \
    cd hubbardI.build  && \
    cmake -DCMAKE_CXX_COMPILER=mpicxx ../hubbardI.src && \
    make install && \
    cd .. && rm -rf *.build *.src


# Install TRIQS/cthyb
RUN . /opt/triqs/share/triqs/triqsvars.sh && \
    git clone -b unstable https://github.com/TRIQS/cthyb.git --depth=1 cthyb.src && \
    mkdir cthyb.build && \
    cd cthyb.build  && \
    cmake -DCMAKE_CXX_COMPILER=mpicxx \
          -DLocal_hamiltonian_is_complex=ON \
          -DHybridisation_is_complex=ON \
          ../cthyb.src && \
    make install && \
    cd .. && rm -rf *.build *.src


RUN echo "source /opt/triqs/share/triqs/triqsvars.sh" >> /root/.bashrc
