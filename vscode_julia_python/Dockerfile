FROM ubuntu:focal

RUN apt-get update && \
    apt-get install -y software-properties-common apt-transport-https curl && \
    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      build-essential \
      vim \
      unzip \
      hdf5-tools \
      libjpeg-dev \
      libblas-dev \
      libfftw3-dev \
      libgmp-dev \
      libhdf5-dev \
      liblapack-dev \
      python3-dev \
      \
      python3-pip \
      python3-setuptools \
      python3-tk \
      && \
    apt-get autoremove --purge -y && \
    apt-get autoclean -y && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/*


####################################################################################################################
# Julia 
# From https://github.com/docker-library/julia/blob/35e2c25f65f054fc8cbb050693ca58e983570461/1.6/buster/Dockerfile
####################################################################################################################
ENV JULIA_PATH /usr/local/julia
ENV PATH $JULIA_PATH/bin:$PATH

# https://julialang.org/juliareleases.asc
# Julia (Binary signing key) <buildbot@julialang.org>
ENV JULIA_GPG 3673DF529D9049477F76B37566E3C7DC03D6E495

# https://julialang.org/downloads/
ENV JULIA_VERSION 1.6.3

RUN set -eux; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	if ! command -v gpg > /dev/null; then \
		apt-get update; \
		apt-get install -y --no-install-recommends \
			gnupg \
			dirmngr \
		; \
		rm -rf /var/lib/apt/lists/*; \
	fi; \
	\
# https://julialang.org/downloads/#julia-command-line-version
# https://julialang-s3.julialang.org/bin/checksums/julia-1.6.3.sha256
# this "case" statement is generated via "update.sh"
	dpkgArch="$(dpkg --print-architecture)"; \
	case "${dpkgArch##*-}" in \
# amd64
		amd64) tarArch='x86_64'; dirArch='x64'; sha256='c7459c334cd7c3e4a297baf52535937c6bad640e60882f9201a73bab9394314b' ;; \
# arm32v7
		armhf) tarArch='armv7l'; dirArch='armv7l'; sha256='7cf08affbad206bd3c1ef8bc117bf0aa6ac95d1666bf4c06f9d530cff29b2067' ;; \
# arm64v8
		arm64) tarArch='aarch64'; dirArch='aarch64'; sha256='df1a7e96f83b60fda54fd14713a373e37ac2d80fd11dbd708a77c8a22ab86e25' ;; \
# i386
		i386) tarArch='i686'; dirArch='x86'; sha256='06e3d64813e4ba6019d8d79e918c48af4943700bd1eb689c481d82a64f8c280a' ;; \
# ppc64le
		ppc64el) tarArch='ppc64le'; dirArch='ppc64le'; sha256='a3cb6f58ee93bbd3bd72ecd45b9f37bf5e16e9c0ef30ba17c809ec7cbb84ee96' ;; \
		*) echo >&2 "error: current architecture ($dpkgArch) does not have a corresponding Julia binary release"; exit 1 ;; \
	esac; \
	\
	folder="$(echo "$JULIA_VERSION" | cut -d. -f1-2)"; \
	curl -fL -o julia.tar.gz.asc "https://julialang-s3.julialang.org/bin/linux/${dirArch}/${folder}/julia-${JULIA_VERSION}-linux-${tarArch}.tar.gz.asc"; \
	curl -fL -o julia.tar.gz     "https://julialang-s3.julialang.org/bin/linux/${dirArch}/${folder}/julia-${JULIA_VERSION}-linux-${tarArch}.tar.gz"; \
	\
	echo "${sha256} *julia.tar.gz" | sha256sum -c -; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$JULIA_GPG"; \
	gpg --batch --verify julia.tar.gz.asc julia.tar.gz; \
	command -v gpgconf > /dev/null && gpgconf --kill all; \
	rm -rf "$GNUPGHOME" julia.tar.gz.asc; \
	\
	mkdir "$JULIA_PATH"; \
	tar -xzf julia.tar.gz -C "$JULIA_PATH" --strip-components 1; \
	rm julia.tar.gz; \
	\
	apt-mark auto '.*' > /dev/null; \
	[ -z "$savedAptMark" ] || apt-mark manual $savedAptMark; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	\
# smoke test
	julia --version

#################################################################################
############################   User   ###########################################
#################################################################################
ARG NB_USER=vscode
ARG NB_UID=1000
RUN useradd -u $NB_UID -m $NB_USER && \
    echo 'triqs ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER $NB_USER
WORKDIR /home/$NB_USER
ENV CPATH=/usr/include/hdf5/serial:$CPATH \
    PATH=/home/$NB_USER/.local/bin:$PATH

RUN pip3 install -U numpy scipy matplotlib && rm -rf /tmp/pip-*

RUN echo "export JULIA_MPIEXEC=`which mpirun`" >> ~/.bashrc && \
    echo "export JULIA_MPI_BINARY=\"system\"" >> ~/.bashrc && \
    echo "export PYTHON=`which python3`" >> ~/.bashrc && \
    . ~/.bashrc
COPY install_julia_pkg.jl install_julia_pkg.jl
RUN julia install_julia_pkg.jl
