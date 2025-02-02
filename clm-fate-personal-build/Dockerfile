## This Dockerfile is heavily based on the work of Shawn P. Serbin:
## https://github.com/serbinsh/elm_containers
## ...and also on the work of NGEETROPICS - Gregory Lemieux - Jessy Needham.
## https://github.com/NGEET/fates-tutorial
# Use an updated LTS Ubuntu base image
FROM ubuntu:22.04

# Set the environment variable to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Categories and Explanation for packages in the apt-get install list:
# - Basic Package Management and Utilities:
#     apt-transport-https: Required for accessing repositories over HTTPS. Keep if you're using secure package sources.
#     apt-utils: Provides utilities for package management. Safe to remove unless you need advanced package management.
#     ca-certificates: Essential for verifying SSL certificates for HTTPS connections (e.g., downloading files from secure sources). Usually necessary.
#     curl / wget: Used to download files from the web. Keep if your Dockerfile relies on downloading resources during the build.
# - Development Tools:
#     build-essential: A meta-package that installs GCC, make, and other compilation tools. Keep if your build requires compiling software.
#     cmake: Used for building software that relies on CMake build scripts. Keep if you're compiling software that requires CMake.
#     libblas-dev, liblapack-dev: Linear algebra libraries. Keep if you are doing scientific or mathematical computation.
#     libcurl4-openssl-dev: Development files for libcurl (used in many HTTP clients). Keep if your application links to libcurl.
#     libffi-dev, libffi7: Foreign Function Interface libraries, required for interfacing with C in Python or other languages. Keep if you're using FFI.
#     libgmp3-dev: Used for arbitrary precision arithmetic in some scientific libraries. Remove if not needed.
#     libncurses5-dev: Development files for terminal user interfaces. Only keep if you're building software that relies on ncurses.
#     libxml2, libxml2-dev, libxml-libxml-perl, libxml2-utils: Libraries for XML processing. Keep if your application processes XML data.
# - Shells, Text Editors, and Monitoring Tools:
#     csh: The C shell. Only needed if your scripts or tools use csh. Can likely be removed unless explicitly required.
#     emacs: A text editor. Remove unless you need it for editing or debugging within the container.
#     htop: An interactive system monitor. Remove if you don't need to monitor the container’s resource usage interactively.
#     m4: A macro processing language. Keep if needed for build scripts.
# - File Compression and Transfer:
#     bzip2, tar, xz-utils: Utilities for file compression and decompression. Usually needed.
#     ftp, openssh-client: For file transfer and SSH. Remove if you're not transferring files via FTP/SSH within the container.
# - Version Control:
#     git: Keep if you're cloning repositories or managing version control.
#     subversion: Keep if you're using SVN repositories. Otherwise, remove.
# - Python and Related Development Tools:
#     python3, python3-dev, python3-pip, python3-venv: Necessary for Python development. Keep if your application is Python-based or uses Python tools.
# - Other Libraries and Tools:
#     libudunits2-0, libudunits2-dev, udunits-bin: Libraries for unit conversion. Keep if used in scientific computation.
#     libxml2 and libxml2-utils: XML processing libraries. Keep if your project handles XML files.
#     zlib1g-dev: Compression library development files. Keep if your application or dependencies need compression functionality.
# - Optional or Likely Removable:
#     sudo: Commented out in your Dockerfile. Typically unnecessary in a container unless you need privilege escalation during runtime.
#     locales: Useful if you're dealing with localization. Remove if you don’t need language support.
#     nco: A tool for working with netCDF files. Keep only if you're dealing with climate/atmospheric data or other netCDF data.
# - What to Keep:
#     Critical utilities for package management, SSL verification, and downloading files: (apt-transport-https, ca-certificates, curl, wget).
#     Development tools if compiling software in the container: (build-essential, cmake, python3-dev, etc.).
#     Version control: (git, possibly subversion).
#     Python: (python3, python3-pip, python3-venv) if your application uses Python.
# - What to Remove (if not needed):
#     Editors, monitors, FTP clients, and shells: (emacs, htop, ftp, csh).
#     Libraries for specific functionality: (libgmp3-dev, libudunits2, libncurses5-dev, etc.), unless required by your application.

# Update the system and install dependencies (organized alphabetically)
RUN apt-get update -y && \
    apt-get install -y \
    apt-transport-https \
    apt-utils \
    build-essential \
    bzip2 \
    ca-certificates \
    cmake \
    csh \
    curl \
    emacs \
    ftp \
    git \
    gpg \
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
    # sudo \
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

# Install specific pip packages
RUN pip install numpy==1.26.4 scipy==1.11

# Set up environment variables for paths and libraries
ENV PATH=/usr/local/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/lib:/usr/lib/x86_64-linux-gnu:$LD_LIBRARY_PATH
# /usr/lib/x86_64-linux-gnu is /usr/lib64. This changed when Ubuntu 12.04 came out.

# Set the default locale
# Set the locale to en_US.UTF-8
RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8

# Set environment variables for the locale
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

# --- GCC Setup ---
# Make sure to check the application binary interface (ABI) compatibility with the GCC version
# When you run this image again, probably when updating the packages or whatever, you may get some errors.
# One of the errors is related to cmake not being able to find a particular library.
# Youll get a path, when you check that path youll see the library there.
# The problem is generally because the library was compiled with a different version of GCC.
# The solution is to recompile the library with the same version of GCC that you are using.
# This is because the ABI is not compatible between different versions of GCC.
# https://gcc.gnu.org/onlinedocs/libstdc++/manual/abi.html

ENV GCC_VERSION=12.1.0

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

# --- Expat Setup ---
ENV EXPAT_VERSION=2.4.4

RUN echo "*** Installing Expat XML parser version ${EXPAT_VERSION}" && \
    cd / && \
    wget https://github.com/libexpat/libexpat/releases/download/R_2_4_4/expat-${EXPAT_VERSION}.tar.xz && \
    tar -xvf expat-${EXPAT_VERSION}.tar.xz && \
    cd expat-${EXPAT_VERSION} && \
    ./configure && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf expat-${EXPAT_VERSION} expat-${EXPAT_VERSION}.tar.xz

# --- HDF5 Setup ---
ENV HDF5_VERSION=1.13.0

RUN echo "*** Installing HDF5 version ${HDF5_VERSION}" && \
    cd / && \
    wget https://support.hdfgroup.org/ftp/HDF5/prev-releases/hdf5-1.13/hdf5-${HDF5_VERSION}/src/hdf5-${HDF5_VERSION}.tar.gz && \
    tar -xvf hdf5-${HDF5_VERSION}.tar.gz && \
    cd hdf5-${HDF5_VERSION} && \
    CC=mpicc ./configure --enable-fortran --enable-parallel --prefix=/usr/local/hdf5 && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf hdf5-${HDF5_VERSION} hdf5-${HDF5_VERSION}.tar.gz

# Update PATH and LD_LIBRARY_PATH for HDF5
ENV PATH=/usr/local/hdf5/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/hdf5/lib:$LD_LIBRARY_PATH

# --- netCDF Setup ---
ENV NETCDF_VERSION=4.9.0

RUN echo "*** Installing netCDF version ${NETCDF_VERSION}" && \
    cd / && \
    wget https://github.com/Unidata/netcdf-c/archive/refs/tags/v${NETCDF_VERSION}.tar.gz && \
    tar -xvf v${NETCDF_VERSION}.tar.gz && \
    cd netcdf-c-${NETCDF_VERSION} && \
    export H5DIR=/usr/local/hdf5 && \
    export NCDIR=/usr/local/netcdf && \
    CC=mpicc CPPFLAGS=-I${H5DIR}/include LDFLAGS=-L${H5DIR}/lib ./configure --enable-parallel-tests --prefix=${NCDIR} && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf netcdf-c-${NETCDF_VERSION} v${NETCDF_VERSION}.tar.gz

# Update PATH and LD_LIBRARY_PATH for netCDF
ENV PATH=/usr/local/netcdf/bin:$PATH
ENV LD_LIBRARY_PATH=/usr/local/netcdf/lib:$LD_LIBRARY_PATH

# --- netCDF-Fortran Setup ---
ENV NETCDF_FORTRAN_VERSION=4.5.4

RUN echo "*** Installing netCDF-Fortran version ${NETCDF_FORTRAN_VERSION}" && \
    cd / && \
    wget https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v${NETCDF_FORTRAN_VERSION}.tar.gz && \
    tar -xvf v${NETCDF_FORTRAN_VERSION}.tar.gz && \
    cd netcdf-fortran-${NETCDF_FORTRAN_VERSION} && \
    export NCDIR=/usr/local/netcdf && \
    export NFDIR=/usr/local/netcdf && \
    CPPFLAGS=-I${NCDIR}/include LDFLAGS=-L${NCDIR}/lib ./configure --prefix=${NFDIR} --enable-parallel-tests && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf netcdf-fortran-${NETCDF_FORTRAN_VERSION} v${NETCDF_FORTRAN_VERSION}.tar.gz

# --- Elm Setup ---
# Create necessary directories and set proper ownership
RUN mkdir -p /tools/cprnc /baselines /.cime /home/elm-user/.cime
#  && \
#     chown elm-user:dockerusers home/elm-user

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

# --- User and Group Definition ---
# Create a user and a group for the container
RUN groupadd -r dockerusers && \
    useradd -ms /bin/bash -u 1000 -g dockerusers elm-user

# # --- User definition ---

# # Create a user and a group
# RUN groupadd -r dockerusers && \
#     useradd -ms /bin/bash elm-user -u 1000 -g dockerusers
# groupadd flags: https://manpages.ubuntu.com/manpages/xenial/en/man8/groupadd.8.html#:~:text=DESCRIPTION,the%20system%20files%20as%20needed.
# useradd flags: https://manpages.ubuntu.com/manpages/xenial/en/man8/useradd.8.html

# To add sudo password to the user:
# usermod -aG sudo elm-user && \
# echo 'elm-user:fates' | chpasswd

ENV GROUP=dockerusers
ENV USER=elm-user

# --- Directory Setup ---
# Create necessary directories and set permissions
RUN mkdir -p /tools/cprnc /baselines /.cime /home/${USER}/.cime /projects_mirror && \
    chown -R ${USER}:${GROUP} /tools /baselines /.cime /home/${USER} /projects_mirror && \
    chmod -R 777 /projects_mirror

# --- Download Configuration Files ---
# Download necessary configuration files for the environment
RUN wget -P /.cime ${REPOSITORY_RAW_URL}/config ${REPOSITORY_RAW_URL}/config_compilers.xml \
    ${REPOSITORY_RAW_URL}/config_machines.xml ${REPOSITORY_RAW_URL}/gnu_docker.cmake && \
    wget -P /home/${USER}/.cime ${REPOSITORY_RAW_URL}/config ${REPOSITORY_RAW_URL}/config_compilers.xml \
    ${REPOSITORY_RAW_URL}/config_machines.xml ${REPOSITORY_RAW_URL}/gnu_docker.cmake

# # Download configuration files
# RUN cd /.cime && \
#     wget $REPOSITORY_RAW_URL/config && \
#     wget $REPOSITORY_RAW_URL/config_compilers.xml && \
#     wget $REPOSITORY_RAW_URL/config_machines.xml && \
#     wget $REPOSITORY_RAW_URL/gnu_docker.cmake && \
#     cd /home/elm-user/.cime && \
#     wget $REPOSITORY_RAW_URL/config && \
#     wget $REPOSITORY_RAW_URL/config_compilers.xml && \
#     wget $REPOSITORY_RAW_URL/config_machines.xml && \
#     wget $REPOSITORY_RAW_URL/gnu_docker.cmake

# RUN chown ${USER}:${GROUP} home/${USER} && \
#     chown ${USER} tools && \
#     chown ${USER} tools/cprnc && \
#     chown ${USER} baselines && \
#     chown ${USER} .cime && \
#     chown ${USER} /home/${USER}/.cime && \
#     chown ${USER}:${GROUP} /E3SM && \
#     chmod -R 777 /E3SM
# # chown flags: https://manpages.ubuntu.com/manpages/xenial/man1/chown.1.html
# # chmod flags: https://help.ubuntu.com/community/FilePermissions

# --- Set Ownership for Additional Directories ---
# Set ownership for E3SM directory and permissions
RUN chown -R ${USER}:${GROUP} /E3SM && \
    chmod -R 777 /E3SM

# --- Cleanup ---
# Remove any extra build files
RUN rm -rf /usr/local/src/* /tmp/*

# --- Configure Bashrc for User ---
# Configure .bashrc for elm-user with simple aliases and prompt color
RUN echo 'alias ls="ls --color=auto"' >> /home/${USER}/.bashrc && \
    echo 'alias grep="grep --color=auto"' >> /home/${USER}/.bashrc && \
    echo "alias la='ls -la'" >> /home/${USER}/.bashrc && \
    echo 'PS1="\[\e[32m\]\u@\[\e[34m\]\W\[\e[0m\]\$ "' >> /home/${USER}/.bashrc

# # --- Configure bashrc for elm-user ---
# RUN echo 'alias ls="ls --color=auto"' >> /home/elm-user/.bashrc && \
#     echo 'alias grep="grep --color=auto"' >> /home/elm-user/.bashrc  && \
#     echo "alias la='ls -la'" >> /home/elm-user/.bashrc && \
#     echo " LS_COLORS='rs=0:di=1;35:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.axa=00;36:*.oga=00;36:*.spx=00;36:*.xspf=00;36:'"  >> /home/elm-user/.bashrc && \
#     echo "export LS_COLORS" >> /home/elm-user/.bashrc && \
#     echo "PS1='\\e[37;1m\\u@\\e[35m\\W\\e[0m\\$ '" >> /home/elm-user/.bashrc

# # Create projects_mirror directory, set ownership, and allow group write permissions
# RUN mkdir -p /projects_mirror && \
#     chown ${USER}:${GROUP} /projects_mirror && \
#     chmod -R 777 /projects_mirror

# --- Switch to the new user ---
USER ${USER}

# Any additional setup commands (if necessary)
RUN echo "Setting up the environment for user: elm-user"

# Set the default command to run when the container starts
CMD ["/bin/bash"]

# Build and run commands
# podman build -t elm-fates-build-image .

