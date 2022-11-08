FROM ubuntu:16.04 as compile-image

ENV LD_LIBRARY_PATH /usr/local/lib
WORKDIR /build

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && apt-get install -y "$@" \
        build-essential software-properties-common \
        byobu curl git htop man unzip vim wget cmake g++ zsh doxygen graphviz \
        libopenmpi1.10 libtiff5-dev libgomp1 libfftw3-dev libboost-all-dev libeigen3-dev \
        libblas-dev liblapack-dev gfortran libboost-dev \
        mpi-default-bin mpi-default-dev libgmp-dev libopenmpi-dev gfortran \
        libmetis-dev libparmetis-dev autotools-dev automake libug-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Build PETSC
RUN git clone -b main https://gitlab.com/petsc/petsc.git && \
    cd petsc && git checkout 8695de0 && \
    ./configure --prefix=/usr \
                --with-x=0 --with-debugging=0 \
                CFLAGS="-O3 -DNDEBUG -ffast-math" \
                --with-parmetis=1 \
                --with-metis=1 \
                --with-hypre=1 --download-hypre=yes \
                --with-superlu_dist=1 --download-superlu_dist=yes \
                --with-mumps=1 --download-mumps=yes \
                --with-ml=1 --download-ml=yes \
                --download-scalapack=yes --download-blacs=yes && \
    make all && make install

# Build Zoltan
RUN git clone https://github.com/sandialabs/Zoltan.git && \
    mkdir Zoltan/zoltanBUILD && cd Zoltan/zoltanBUILD && \
    ../configure --prefix=/usr \
                 --with-parmetis -with-parmetis-incdir=/usr/include/ --with-parmetis-libdir=/usr/lib/ && \
    make everything && \
    make install && \
    rm -rf {petsc,?oltan}

# Build PEITS
RUN cd /build && \
    git clone https://github.com/EIT-team/PEITS && \
    cd PEITS && \
    cp config.opts_example config.opts && \
    sed -i -e '/^PETSCPATH/d' -e '/ZOLTANPATH/d' \
        -e '/with-metis/d' -e '/with-parmetis/d' \
        config.opts && \
    sed -i -e '1 a PETSCPATH="/usr/lib"' \
        config.opts && \
    bash ./INSTALL



#FROM ubuntu:16.04 as build-image
WORKDIR /build/PEITS/src

#COPY --from=compile-image /build/ /build/
#COPY --from=compile-image /usr/lib/ /usr/lib/


# Create User and set environment
RUN useradd -ms /bin/bash peitsier
RUN chown -R peitsier:peitsier /build/PEITS && \
    echo "mpirun -np $(nproc) dune_peits; exit" >> /build/PEITS/src/start.sh

USER peitsier

## Test PEITS
#RUN cd /build/PEITS/tests && \
#    bash ./initial_test.sh

CMD ["bash", "start.sh"]

# cd /build/PEITS/src
# mpirun -np 2 ./dune_peits
# loads data from /build/PEITS/data
