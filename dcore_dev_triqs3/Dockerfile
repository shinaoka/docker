FROM --platform=linux/x86_64 shinaoka/triqs3_all

# ALPS
WORKDIR /root
RUN git clone -b master --single-branch https://github.com/ALPSCore/ALPSCore.git && \
    mkdir ALPSCore.build && \
    cd ALPSCore.build && \
    cmake -DALPS_CXX_STD=c++14 -DCMAKE_INSTALL_PREFIX=/opt/ALPSCore -DDocumentation=OFF -DALPS_INSTALL_EIGEN=true -DTesting=OFF ../ALPSCore && \
    make install -j 4

# pomerol
WORKDIR /root
RUN git clone https://github.com/aeantipov/pomerol.git && cd pomerol && git checkout 7a45b6a8b8254dcbf8 && cd .. && \
    mkdir pomerol.build && cd pomerol.build && \
    cmake -DCMAKE_INSTALL_PREFIX=/opt/pomerol -DPOMEROL_COMPLEX_MATRIX_ELEMENTS=ON ../pomerol && \
    make install && cd .. && rm -rf pomerol.build pomerol

# pomerol2dcore
WORKDIR /root
RUN git clone https://github.com/j-otsuki/pomerol2dcore.git && \
    mkdir pomerol2dcore.build && cd pomerol2dcore.build && \
    cmake -Dpomerol_DIR=/opt/pomerol -DCMAKE_INSTALL_PREFIX=/opt/pomerol2dcore ../pomerol2dcore && \
    make install
