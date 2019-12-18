FROM ubuntu:18.04

RUN apt-get update && apt-get install -y --no-install-recommends apt-utils \
        build-essential \
        sudo \
        gcc \
        g++ \
        libopenblas-dev \
        liblapacke-dev \
        libhdf5-serial-dev \
        libmatio-dev \
    && apt-get install -y make \
        -y cmake \
        -y python2.7-dev \
        python-pip \
        swig \
        wget \
        unzip \
    && pip install \
        numpy \
    && apt-get clean \
    && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# add local openmeeg version
#ADD . /openmeeg

# load openmeeg from master branch
#RUN GIT_URL="https://github.com/openmeeg/openmeeg/archive/master.zip" # master not stable
# 9741e76254ea2b501746d88973b11bca3be4950c
RUN GIT_URL="https://github.com/openmeeg/openmeeg/archive/30b8ee5322863b56f12ce790c8a04852a4594be0.zip" \
    && wget -P ./ $GIT_URL \
    && unzip ./30b8ee5322863b56f12ce790c8a04852a4594be0.zip -d / \
    && mv /openmeeg-30b8ee5322863b56f12ce790c8a04852a4594be0 /openmeeg \
    && rm ./30b8ee5322863b56f12ce790c8a04852a4594be0.zip 

RUN cd /openmeeg \
    && mkdir build \
    && cd build \
    && cmake -DCMAKE_BUILD_TYPE=Release -DUSE_PROGRESSBAR=ON -DBLA_VENDOR=OpenBLAS -DENABLE_PYTHON=ON .. \
    && make \
    && make test \
    && make install \
    && cd ../.. && rm -rf cppcheck*

ENV LD_LIBRARY_PATH "${LD_LIBRARY_PATH}:/usr/local/lib/"

CMD [ "python -c 'import openmeeg'"]

