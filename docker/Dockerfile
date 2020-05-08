FROM rocker/tidyverse:3.6.1
MAINTAINER ccdl@alexslemonade.org
WORKDIR /rocker-build/

RUN apt-get update && apt-get install -y --no-install-recommends apt-utils
RUN apt-get install dialog apt-utils -y

RUN apt-get update -qq && apt-get -y --no-install-recommends install \
    build-essential \
    libxml2-dev \
    libsqlite-dev \
    libmariadbd-dev \
    libmariadbclient-dev \
    libmariadb-client-lgpl-dev \
    libpq-dev \
    libssh2-1-dev \
    pandoc \
    libmagick++-dev \
    time

RUN install2.r \
    rjson \ 
    ggpubr \
    colorspace\
    Rtsne \
    umap

ENV BIOCONDUCTOR_VERSION 3.10
RUN R -e "BiocManager::install(c('ensembldb', 'DESeq2', 'qvalue',\
    'org.Hs.eg.db', 'org.Dr.eg.db', 'org.Mm.eg.db', 'org.Cf.eg.db',\
    'ComplexHeatmap', 'ConsensusClusterPlus', 'scran', 'scater',\
    'tximport', 'alevinQC'), \
    version = '${BIOCONDUCTOR_VERSION}', update = FALSE)"

# FastQC
RUN apt update && apt install -y fastqc

# fastp 
RUN git clone https://github.com/OpenGene/fastp.git
RUN cd fastp && \
    make && \
    sudo make install

# MultiQC
RUN apt update && apt install -y python-pip
RUN pip install multiqc

ENV PACKAGES git gcc make g++ libboost-all-dev liblzma-dev libbz2-dev \
    ca-certificates zlib1g-dev curl unzip autoconf

ENV CMAKE_VERSION 3.17.2
ENV SALMON_VERSION 1.2.1

# salmon binary will be installed in /home/salmon/bin/salmon
# don't modify things below here for version updates etc.

WORKDIR /home

RUN apt-get update && \
    apt-get install -y --no-install-recommends ${PACKAGES} && \
    apt-get clean

# Apt doesn't have the latest version of cmake, so install it using their script.
RUN apt remove cmake cmake-data -y

RUN wget --quiet https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-Linux-x86_64.sh && \
    mkdir /opt/cmake && \
    sh cmake-${CMAKE_VERSION}-Linux-x86_64.sh --prefix=/opt/cmake --skip-license && \
    sudo ln -s /opt/cmake/bin/cmake /usr/local/bin/cmake

RUN curl -k -L https://github.com/COMBINE-lab/salmon/archive/v${SALMON_VERSION}.tar.gz -o salmon-v${SALMON_VERSION}.tar.gz && \
    tar xzf salmon-v${SALMON_VERSION}.tar.gz && \
    cd salmon-${SALMON_VERSION} && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local && make && make install