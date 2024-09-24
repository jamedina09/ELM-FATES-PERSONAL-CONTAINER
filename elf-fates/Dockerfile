# Use an updated LTS Ubuntu base image
FROM ubuntu:20.04

# Set the environment variable to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Update the system and install dependencies (organized alphabetically)
RUN apt-get update -y && \
    apt-get install -y \
    apt-transport-https \
    apt-utils \
    build-essential \
    bzip2 \
    cmake \
    csh \
    emacs \
    ftp \
    git \
    htop \
    libblas-dev \
    libcurl4-openssl-dev \
    libffi-dev \
    libffi7 \
    libgmp3-dev \
    liblapack-dev \
    libncurses5-dev \
    libudunits2-0 \
    libudunits2-dev \
    libxml-libxml-perl \
    libxml2 \
    libxml2-dev \
    libxml2-utils \
    locales \
    m4 \
    nco \
    openssh-client \
    python3 \
    python3-dev \
    python3-pip \
    python3-venv \
    subversion \
    tar \
    udunits-bin \
    wget \
    xz-utils \
    zlib1g-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set Python 3 as the default
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 1 && \
    update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

# Set up environment variables for paths and libraries
ENV PATH=/usr/local/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/lib:/usr/local/lib64:$LD_LIBRARY_PATH

# Set the default locale
RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen && \
    locale-gen && \
    dpkg-reconfigure locales && \
    /usr/sbin/update-locale LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

# --- GCC Setup ---
ENV GCC_VERSION=11.2.0

RUN echo "*** Installing GCC version ${GCC_VERSION}" && \
    mkdir -p /usr/local/gcc && \
    cd / && \
    wget https://mirrors.kernel.org/gnu/gcc/gcc-$GCC_VERSION/gcc-$GCC_VERSION.tar.xz && \
    tar -xvf gcc-$GCC_VERSION.tar.xz && \
    cd gcc-$GCC_VERSION && \
    contrib/download_prerequisites && \
    mkdir build && cd build && \
    ../configure --enable-languages=c,c++,fortran --prefix=/usr/local/gcc --disable-multilib && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf gcc-$GCC_VERSION gcc-$GCC_VERSION.tar.xz

# Update PATH for GCC
ENV PATH=/usr/local/gcc/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/gcc/lib64:$LD_LIBRARY_PATH

# --- OpenMPI Setup ---
ENV OPENMPI_VERSION=4.1.6

RUN echo "*** Installing OpenMPI version ${OPENMPI_VERSION}" && \
    cd / && \
    wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-${OPENMPI_VERSION}.tar.gz && \
    tar -xvf openmpi-${OPENMPI_VERSION}.tar.gz && \
    cd openmpi-${OPENMPI_VERSION} && \
    ./configure CC=gcc CXX=g++ FC=gfortran --prefix=/usr/local/openmpi --enable-static && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf openmpi-${OPENMPI_VERSION} openmpi-${OPENMPI_VERSION}.tar.gz

# Update PATH and LD_LIBRARY_PATH for OpenMPI
ENV PATH=/usr/local/openmpi/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/openmpi/lib:$LD_LIBRARY_PATH

# --- Elm Setup ---
# Create a user to access the E3SM directory
RUN useradd -ms /bin/bash elm-user -u 1000

# Create necessary directories
RUN mkdir -p /tools/cprnc /baselines /home/elm-user/.cime && \
    chown -R elm-user:elm-user /home/elm-user

# Define environment variables for repository tags and URLs
ENV FATES_TAG=sci.1.76.4_api.35.1.0
ENV HLM_TAG=elm_v3.0.0-fates_sci.1.76.4_api.35.1.0
ENV CIME_TAG=cime6.0.248
ENV MCT_TAG=MCT_2.11.0
ENV SCORPIO_TAG=scorpio-v1.6.4
ENV MPP_HASH=c0c903e675e31e6b9ab447b0cc51ccd8a36fa7a7
ENV SBETR_HASH=66260f4991d61439d4cba92eb633590b09f97920

# Define repository URLs
ENV HLM_REPOSITORY_URL=https://github.com/NGEET/E3SM/
ENV FATES_REPOSITORY_URL=https://github.com/NGEET/FATES/
ENV MPP_REPOSITORY_URL=https://github.com/MPP-LSM/MPP/
ENV SBETR_REPOSITORY_URL=https://github.com/BeTR-biogeochemistry-modeling/sbetr/
ENV MCT_REPOSITORY_URL=https://github.com/MCSclimate/MCT/
ENV SCORPIO_REPOSITORY_URL=https://github.com/E3SM-Project/scorpio/
ENV CIME_REPOSITORY_URL=https://github.com/ESMCI/cime/
ENV REPOSITORY_RAW_URL=https://raw.githubusercontent.com/jamedina09/ELM-FATES-PERSONAL-CONTAINER/refs/heads/main/cime_config_files/e3sm/

# Define locations for components
ENV ELM_EXT_LOCATION=/E3SM/components/elm/src/external_models/
ENV EXT_LOCATION=/E3SM/externals/
ENV CIME_LOCATION=/E3SM/cime/

# Download and install external repositories
RUN wget $HLM_REPOSITORY_URL/archive/refs/tags/$HLM_TAG.tar.gz && \
    tar -zxvf $HLM_TAG.tar.gz && \
    rm $HLM_TAG.tar.gz && \
    mv E3SM-$HLM_TAG E3SM && \
    rm -rf $CIME_LOCATION $EXT_LOCATION/mct $EXT_LOCATION/scorpio $ELM_EXT_LOCATION/fates $ELM_EXT_LOCATION/mpp $ELM_EXT_LOCATION/sbetr && \
    wget $FATES_REPOSITORY_URL/archive/refs/tags/$FATES_TAG.tar.gz && \
    tar -zxvf $FATES_TAG.tar.gz && \
    rm $FATES_TAG.tar.gz && \
    mv fates-$FATES_TAG $ELM_EXT_LOCATION/fates && \
    wget $MPP_REPOSITORY_URL/archive/$MPP_HASH.tar.gz && \
    tar -zxvf $MPP_HASH.tar.gz && \
    rm $MPP_HASH.tar.gz && \
    mv MPP-$MPP_HASH $ELM_EXT_LOCATION/mpp && \
    wget $SBETR_REPOSITORY_URL/archive/$SBETR_HASH.tar.gz && \
    tar -zxvf $SBETR_HASH.tar.gz && \
    rm $SBETR_HASH.tar.gz && \
    mv sbetr-$SBETR_HASH $ELM_EXT_LOCATION/sbetr && \
    wget $MCT_REPOSITORY_URL/archive/refs/tags/$MCT_TAG.tar.gz && \
    tar -zxvf $MCT_TAG.tar.gz && \
    rm $MCT_TAG.tar.gz && \
    mv MCT-$MCT_TAG $EXT_LOCATION/mct && \
    wget $SCORPIO_REPOSITORY_URL/archive/refs/tags/$SCORPIO_TAG.tar.gz && \
    tar -zxvf $SCORPIO_TAG.tar.gz && \
    rm $SCORPIO_TAG.tar.gz && \
    mv scorpio-$SCORPIO_TAG $EXT_LOCATION/scorpio && \
    wget $CIME_REPOSITORY_URL/archive/refs/tags/$CIME_TAG.tar.gz && \
    tar -zxvf $CIME_TAG.tar.gz && \
    rm $CIME_TAG.tar.gz && \
    mv cime-$CIME_TAG $CIME_LOCATION

# Download configuration files
RUN cd /home/elm-user/.cime && \
    wget $REPOSITORY_RAW_URL/config && \
    wget $REPOSITORY_RAW_URL/config_compilers.xml && \
    wget $REPOSITORY_RAW_URL/config_machines.xml && \
    wget $REPOSITORY_RAW_URL/gnu_docker.cmake

# # Configure bashrc for elm-user
RUN echo "alias la='ls -la'" >> /home/elm-user/.bashrc && \
    echo "# You can add more configurations here" >> /home/elm-user/.bashrc

# Switch to the new user
USER elm-user

# Set the default command
CMD ["/bin/bash"]

# Build and run commands
# podman build -t tst_downloads .
# podman run --name ubun_test -it tst_downloads /bin/bash
