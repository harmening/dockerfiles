#FROM python:3.6-slim
#FROM python:3.7-slim
#FROM python:3.8-slim
#FROM python:3.9-slim
FROM python:3.10-slim

WORKDIR /

# install c-libraries and other dependencies
RUN apt-get update && apt-get install -qq -y \
			  build-essential libpq-dev --no-install-recommends apt-utils \
        libpython3-dev \
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
        libsuperlu-dev \
        libhdf5-serial-dev \
        libopenblas-dev \
        liblapacke-dev \
        libhdf5-serial-dev \
        libmatio-dev \
        libgts-dev \
        libgts-0.7-5 \
        libc6 \
        libglib2.0-0 \
        libglib2.0-dev \
        libglu1-mesa-dev \
        libfreetype6-dev\
        libxml2-dev \
        libxslt1-dev \
        python-dev \
        libmpfr-dev \
        libboost-dev \
        libboost-atomic-dev \
        libboost-chrono-dev \
        libboost-date-time-dev \
        libboost-system-dev \
        libboost-thread-dev \
        libeigen3-dev \
        libcgal-dev \
        libgmp-dev \
        libgmpxx4ldbl \
        libtbb-dev \
        libssl-dev \
        wget \ 
        unzip \
        tar \
        csh \
        vim \
    && apt-get install -y make \
        -y cmake \
        -y python3-dev \
        python3-pip \
        swig \
        git \
        sudo \
    && pip3 install \
        numpy \
        scipy \
        sparse \
        h5py \
        pytest \
    && apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    

# clone duneuro
RUN git config --global http.sslverify false \ 
# very very bad but working
    && export GIT_SSL_NO_VERIFY=true \
    && mkdir /duneuro \
    && cd /duneuro \
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
       done \
# small corrections due to missing links
    && cp /duneuro/dune-uggrid/low/*.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/low/*.h /duneuro/dune-uggrid/ \
    && cp /duneuro/dune-uggrid/low/*.cc /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/dom/domain.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/pargm.h /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/cw.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/np/udm/udm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/np/algebra/sm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/evm.* /duneuro/dune-uggrid/ug/ \
    && cp /duneuro/dune-uggrid/gm/dlmgr.* /duneuro/dune-uggrid/ug/ \
# 1111 for normal model, 3333 for big model
    && sed -i 's/^.*#define NDELEM_BLKS_MAX\s*1.*$/#define NDELEM_BLKS_MAX                 3333/' /duneuro/dune-uggrid/gm/gm.h \
    && sed -i 's/^.*#define NDELEM_BLKS_MAX\s*1.*$/#define NDELEM_BLKS_MAX                 3333/' /duneuro/dune-uggrid/ug/gm.h \
# compile duneuro
    && MAKE_FLAGS="-j2 --with-superlu=/SuperLU_5.2.1 --with-superlu-lib=/SuperLU_5.2.1/lib/libsuperlu_5.1.a" \
    && for i in common geometry localfunctions grid istl typetree uggrid functions pdelab ; do \
      cd /duneuro/dune-$i; \
      mkdir build; \
      cd build; \
      cmake -G "Unix Makefiles" \
            -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Debug \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_DISABLE_FIND_PACKAGE_SuperLU=FALSE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
      make test; \
      make install; \
    done \
    && for i in duneuro duneuro-py; do \
      cd /duneuro/$i; \
      mkdir build; \
      cd build; \
      cmake -G "Unix Makefiles" \
            -DCMAKE_CXX_FLAGS="-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor" \
						-DCMAKE_BUILD_TYPE=Release \
						-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
						-DCMAKE_DISABLE_FIND_PACKAGE_SuperLU=FALSE\
						-DCMAKE_CXX_COMPILER=g++\
						-DCMAKE_C_COMPILER=gcc \
					.. ;\
      make $MAKE_FLAGS; \
      make test; \
      make install; \
    done; 

# install duneuro
RUN echo 'CMAKE_FLAGS="\
          -G \"Unix Makefiles\" \
          -DCMAKE_CXX_FLAGS=\"-fPIC -Og -g -Wall -Wextra -Wno-unused-parameter -Wno-unused-local-typedefs -std=c++14 -pthread -march=native -Wdelete-non-virtual-dtor\" \
					-DCMAKE_BUILD_TYPE=Debug \
					-DCMAKE_DISABLE_FIND_PACKAGE_MPI=TRUE\
					-DCMAKE_DISABLE_FIND_PACKAGE_SuperLU=FALSE\
					-DCMAKE_CXX_COMPILER=g++\
					-DCMAKE_C_COMPILER=gcc \ 
          " MAKE_FLAGS="-j2"' > /duneuro/dune-common/config_release.opts \
    && /duneuro/dune-common/bin/dunecontrol --opts=/duneuro/dune-common/config_release.opts --builddir=/duneuro/build-release all


# install openmeeg
RUN git clone https://github.com/openmeeg/openmeeg.git \
    && cd /openmeeg \
    && git fetch --all --tags --prune \ 
    && git checkout tags/2.5.5 -b v2.5.5-branch \
    && mkdir build \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DUSE_PROGRESSBAR=ON -DBLA_VENDOR=OpenBLAS -DENABLE_PYTHON=ON -DCMAKE_CXX_FLAGS="-Wno-narrowing -Wno-unused-local-typedefs -fpermissive -std=c++14" .. \
    && make \
    && make test \
    && make install \
    && cd ../.. && rm -rf cppcheck*


ENV PYTHONPATH "${PYTHONPATH}:/duneuro/build-release/duneuro-py/src/"
ENV LD_LIBRARY_PATH "${LD_LIBRARY_PATH}:/usr/local/lib/"


# Install pyhemo and run tests
RUN git clone https://github.com/harmening/pyhemo.git \
    && cd /pyhemo \
    && python setup.py develop \
    && pytest tests


CMD ["python"]
