#FROM ubuntu:18.04
FROM python:3.6-slim

RUN apt-get update && apt-get install -qq -y \
			  build-essential libpq-dev --no-install-recommends apt-utils \
        sudo \
        gcc \
        g++ \
        gfortran \
        libopenmpi-dev \
        openmpi-bin \
        openmpi-common \
        openssh-client \ 
        openssh-server \
        libeigen3-dev \
    && apt-get install -y make \
        -y cmake \
        -y python2.7-dev \
        python-pip \
        git \
    && pip install \
        numpy \
    && apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN mkdir /duneuro \
    && cd duneuro \
    && for i in common geometry localfunctions grid istl; do \
	       git clone --branch releases/2.5 https://gitlab.dune-project.org/core/dune-$i.git; \
       done; \
       for i in functions typetree uggrid; do \
	       git clone --branch releases/2.5 https://gitlab.dune-project.org/staging/dune-$i.git; \
       done; \
       for i in pdelab; do \
	       git clone --branch releases/2.5 https://gitlab.dune-project.org/pdelab/dune-$i.git; \
       done; \
       for i in duneuro; do \
	       git clone --branch releases/2.5 https://gitlab.dune-project.org/duneuro/$i.git; \
       done; \
       for i in duneuro-matlab duneuro-py; do \
	       git clone --branch releases/2.5 --recursive https://gitlab.dune-project.org/duneuro/$i.git; \
       done; 

			#-G \"Unix Makefiles\"\
RUN MAKE_FLAGS="-j2" \
    && for i in common geometry localfunctions grid istl typetree functions pdelab uggrid; do \
      cd /duneuro/dune-$i; \
      mkdir build; \
      cd build; \
      cmake -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Debug \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
    # make test; \
      make install; \
    done;

RUN for i in duneuro duneuro-py; do \
      cd /duneuro/$i; \
      mkdir build; \
      cd build; \
      cmake -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Debug \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
    # make test; \
      make install; \
    done;


#CMD [ "python -c 'import duneuro'"]
