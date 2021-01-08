FROM python:3.7-slim

RUN apt-get update && apt-get install -qq -y \
			  build-essential libpq-dev --no-install-recommends apt-utils \
        libpython-dev \
        libsuitesparse-dev \
        gcc \
        g++ \
        gfortran \
        libopenmpi-dev \
        openmpi-bin \
        openmpi-common \
        openssh-client \ 
        openssh-server \
        libeigen3-dev \
        libopenblas-dev \
        liblapacke-dev \
        libhdf5-serial-dev \
        libmatio-dev \
        wget \ 
        tar \
    && apt-get install -y make \
        -y cmake \
        -y python3-dev \
        git \
        sudo \
    && pip install \
        numpy \
    && apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN SUPERLU_URL="https://portal.nersc.gov/project/sparse/superlu/superlu_5.2.1.tar.gz" \
    && wget -P ./ $SUPERLU_URL \
    && tar -xzf ./superlu_5.2.1.tar.gz -C / \
    && rm ./superlu_5.2.1.tar.gz \
    && cd /SuperLU_5.2.1 \
    #&& cp MAKE_INC/make.linux make.inc \
    && mkdir build; cd build \
    && cmake .. \
    && make \
    && make test \
    && make install


RUN mkdir /duneuro \
    && cd duneuro \
    && for i in common geometry localfunctions grid istl; do \
	       git clone --branch releases/2.6 https://gitlab.dune-project.org/core/dune-$i.git; \
       done; \
       for i in functions typetree uggrid; do \
	       git clone --branch releases/2.6 https://gitlab.dune-project.org/staging/dune-$i.git; \
       done; \
       for i in pdelab; do \
	       git clone --branch releases/2.6 https://gitlab.dune-project.org/pdelab/dune-$i.git; \
       done; \
       for i in duneuro; do \
	       #git clone --branch releases/2.6 https://gitlab.dune-project.org/duneuro/$i.git; \
	       #git clone https://gitlab.dune-project.org/duneuro/duneuro/-/tree/quick-hack-bst-issue; \
         git clone --branch quick-hack-bst-issue https://gitlab.dune-project.org/duneuro/$i.git; \
       done; \
       for i in duneuro-py; do \
	       git clone --branch releases/2.6 --recursive https://gitlab.dune-project.org/duneuro/$i.git; \
       done; 

RUN cp /duneuro/dune-uggrid/low/*.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/low/*.h /duneuro/dune-uggrid/ \
    && cp /duneuro/dune-uggrid/low/*.cc /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/dom/domain.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/pargm.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/cw.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/np/udm/udm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/np/algebra/sm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/evm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/dlmgr.* /duneuro/dune-uggrid/ug/ 

# 1111 for normal model, 3333 for big model
RUN sed -i 's/^.*#define NDELEM_BLKS_MAX\s*100.*$/#define NDELEM_BLKS_MAX                 3333/' /duneuro/dune-uggrid/gm/gm.h \
    && sed -i 's/^.*#define NDELEM_BLKS_MAX\s*100.*$/#define NDELEM_BLKS_MAX                 3333/' /duneuro/dune-uggrid/ug/gm.h

# 500k for normal model, 500b for big model
RUN sed -i 's/^.*for (; i<500000; i++) {/      for (; i<500000000; i++) {/' /duneuro/duneuro/duneuro/common/kdtree.hh

RUN MAKE_FLAGS="-j2" \
    && for i in common geometry localfunctions grid istl typetree uggrid functions pdelab ; do \
      cd /duneuro/dune-$i; \
      mkdir build; \
      cd build; \
      cmake -G "Unix Makefiles" \
            -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Debug \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
      make test; \
      make install; \
    done;


RUN for i in duneuro duneuro-py; do \
      cd /duneuro/$i; \
      mkdir build; \
      cd build; \
      cmake -G "Unix Makefiles" \
            -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Release \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
      make test; \
      make install; \
    done;


RUN echo 'CMAKE_FLAGS="\
          -G \"Unix Makefiles\" \
          -DCMAKE_CXX_FLAGS=\"-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor\" \
					-DCMAKE_BUILD_TYPE=Debug \
					-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
					-DCMAKE_CXX_COMPILER=g++\
					-DCMAKE_C_COMPILER=gcc \ 
          " MAKE_FLAGS="-j2"' > /duneuro/dune-common/config_release.opts \
    && /duneuro/dune-common/bin/dunecontrol --opts=/duneuro/dune-common/config_release.opts --builddir=/duneuro/build-release all



ENV PYTHONPATH "${PYTHONPATH}:/duneuro/build-release/duneuro-py/src/"
ENV LD_LIBRARY_PATH "${LD_LIBRARY_PATH}:/usr/local/lib/"


#CMD [ "python -c 'import duneuropy'"]
