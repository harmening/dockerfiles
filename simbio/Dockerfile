# docker build -t simbio .
# docker run -it simbio:latest bash
FROM ubuntu:16.04

# Set an encoding to make things work smoothly.
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
        apt-transport-https \
        apt-utils \
        sudo \
        g++ \
#        gcc=5.4.0 \
        gfortran \
				libboost-all-dev \
				libopenmpi-dev \
				openmpi-bin \
				python-dev \
				python-pip \
        csh \
        mpich \
        f2c \ 
    		make \
        cmake \
        subversion \
        vim \
        wget \
        tar \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && ln -s /usr/include/f2c.h /usr/include/g2c.h \
    && ln -s /usr/bin/gfortran /usr/bin/g77 


## SimBio
# get simbio code
RUN cd / \
    && svn checkout svn://svn.code.sf.net/p/neurofem-simbio/code/ SimBio \
    && cd SimBio/trunk/ \
    && make clean || true \
#    && make realclean || true \
    && make Venant


### Testing on example data -> see tutorial
# Download test data
RUN SF_URL="https://ayera.dl.sourceforge.net/project/neurofem-simbio/SimBio-Testdata2.tar.gz" \
    && wget -P ./ $SF_URL \
    && tar -xzf ./SimBio-Testdata2.tar.gz -C / \
    && rm ./SimBio-Testdata2.tar.gz
# Run test data
# (i)
RUN cd /SimBio-Testdata2/deMunck/software/ \
    && make clean \
    && sudo make -f Makefile_alldips \
    && cd /SimBio-Testdata2/deMunck/ \
    && rm ../models/tetgen_31860-all_-1_0.2_-1_1/results_deMunck/potdeMunck_tang_1stepyAx_128-204_senstetgen_31860-all_-1_0.2_-1_1_aniso.elc.pot || true \
    && rm ../models/tetgen_31860-all_-1_0.2_-1_1/results_deMunck/potdeMunck_rad_1stepyAx_128-204_senstetgen_31860-all_-1_0.2_-1_1_aniso.elc.pot || true \
    && ./scripts_alldips/run_tetgen_31860-all_-1_0.2_-1_1_alltangdipoles_aniso >> ../models/tetgen_31860-all_-1_0.2_-1_1/results_deMunck/potdeMunck_tang_1stepyAx_128-204_senstetgen_31860-all_-1_0.2_-1_1_aniso.elc.pot \
    && ./scripts_alldips/run_tetgen_31860-all_-1_0.2_-1_1_allraddipoles_aniso >> ../models/tetgen_31860-all_-1_0.2_-1_1/results_deMunck/potdeMunck_rad_1stepyAx_128-204_senstetgen_31860-all_-1_0.2_-1_1_aniso.elc.pot
# (ii)
RUN cd /SimBio-Testdata2/models/ \
    && rm ipm_linux_opt || true \
    && alias ipm_linux_opt='/SimBio/trunk/bin/ipm_linux_dbx_Venant' \
    && ln -s /SimBio/trunk/bin/ipm_linux_dbx_Venant ipm_linux_opt 
#    && rm ipm_linux_opt_Oldsubtraction || true \
#    && ln -s /SimBio/trunk/bin/ipm_linux_opt_Oldsubtraction ipm_linux_opt_Oldsubtraction

# (iii) 
RUN cd /SimBio-Testdata2/models/ \
#    && cp ./ForwardComputation_IPNeuroFEM_scripts/run_tetgen_31860-all_-1_0.2_-1_1_sources_1.0step_yAxis_alldips_aniso ./  \
#    && ./run_tetgen_31860-all_-1_0.2_-1_1_sources_1.0step_yAxis_alldips_aniso > run_tetgen_31860-all_-1_0.2_-1_1_sources_1.0step_yAxis_alldips_aniso.out 
# (iv)
#RUN cd /SimBio-Testdata2/models/Comp_RDM_and_MAG \
#    sudo make  \
#    cd /SimBio-Testdata2/models/ \
#    #cp ForwardComputation_IPNeuroFEM_scripts/run_tetgen_31860-all_-1_0.2_-1_1_aniso_RE_and_RDM_and_MAG_sources_1.0step_yAxis_alldips 
#    run_tetgen_31860-all_-1_0.2_-1_1_aniso_RE_and_RDM_and_MAG_sources_1.0step_yAxis_alldips > run_tetgen_31860-all_-1_0.2_-1_1_aniso_RE_and_RDM_and_MAG_sources_1.0step_yAxis_alldips.out
## (v)


#CMD ["cd /SimBio-Testdata2/models/ && ./run_tetgen_31860-all_-1_0.2_-1_1_sources_1.0step_yAxis_alldips_aniso > run_tetgen_31860-all_-1_0.2_-1_1_sources_1.0step_yAxis_alldips_aniso.out" ]

#WORKDIR /
