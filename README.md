# dockerfiles

Collection of various Dockerfiles for images I regularly use.
They live on dockerhub under [harmening](https://hub.docker.com/u/harmening/).


### conversion to singularity
```bash
docker save d507db60d71e -o openmeeg.tar 
singularity build openmeeg.sif docker-archive://openmeeg.tar 
singularity shell openmeeg.sif
qsub -l singularity -b y singularity run openmeeg.sif data/start_calc.sh  
```

