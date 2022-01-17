FROM ubuntu:focal

RUN apt-get update && \
    apt-get install -y software-properties-common apt-transport-https curl && \
    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      build-essential \
      vim \
      cmake \
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
      cppcheck \
      \
      clang \
      clang-tidy \
      libclang-dev \
      libeigen3-dev \
      python3-clang \
      \
      libnfft3-dev \
      \
      sudo \
      openssh-client \
      python3-pip \
      python3-setuptools \
      python3-tk \
      && \
    apt-get autoremove --purge -y && \
    apt-get autoclean -y && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/*

RUN apt-get update && \
    apt-get install -y software-properties-common apt-transport-https curl && \
    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
     libboost-all-dev && \
    apt-get autoremove --purge -y && \
    apt-get autoclean -y && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/*

WORKDIR /root

RUN git clone https://github.com/krivenko/libcommute.git libcommute.src && \
    mkdir libcommute.build && cd libcommute.build && \
    CC=mpicc CXX=mpic++ cmake -DCMAKE_BUILD_TYPE=Release        \
          -DCMAKE_INSTALL_PREFIX=/opt/libcommute    \
          -DSTATIC_ANALYSIS=ON                                \
          -DTESTS=OFF                                          \
          -DEXAMPLES=OFF                                       \
          ../libcommute.src && \
          make -j 3 install

RUN git clone https://github.com/krivenko/pomerol.git pomerol.src && \
    mkdir pomerol.build && cd pomerol.build && \
    CC=mpicc CXX=mpic++ cmake -DCMAKE_BUILD_TYPE=Release  \
      -DCMAKE_INSTALL_PREFIX=/opt/pomerol -Dlibcommute_DIR=/opt/libcommute/lib/cmake/ ../pomerol.src && \
    make -j 3 install

#RUN git clone https://github.com/j-otsuki/pomerol2dcore.git pomerol2dcore.src && \
    #mkdir pomerol2dcore.build && cd pomerol2dcore.build && \
    #CC=mpicc CXX=mpic++ cmake ../pomerol2dcore.src -DCMAKE_INSTALL_PREFIX=/opt/pomerol2dcore -Dpomerol_DIR=/opt/.ins && \
    #make 
