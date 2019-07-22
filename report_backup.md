# Weekend Project Report by Group 7

## Group members
- Chen 👩‍🎓
- Jayadev 👨‍🎓
- An 👨‍🎓 

## 1. Overview of the project

**Project goals**
- [x] Learn how to contruct generalized, scalable and reproducible workflows (Explore multiple workflow management systems)
- [x] Learn how to use Makeflow and workqueue to scale a given workflow beyond a single node

**Project deliverables**
- [x] Using one of the packaging or container systems described (e.g., Conda, Guix, or Docker), prepare a working environment to run the examples. Now try to run the workflows using the tools presented and appreciate the different approaches to execute the same example.
- [x] Compare the different syntaxes used by the tools to define a workflow and explore how each tool describes the processes and the dependencies in a different way.
- [x] Use the Amazon EC2 calculation sheet, and calculate how much it would cost to store 100 GB in S3, and execute a calculation on 100 “large” nodes, each reading 20 GB of data. Do the same for another cloud provider.

**Project report format**
- Deliverables should consist of a report on Github (one report per team). Post the link to your report at the bottom of this HackMD.

## 2. Materials
- Strozzi et al. 2019 paper: https://link.springer.com/protocol/10.1007%2F978-1-4939-9074-0_24
- Strozzi et al. 2019 GitHub repository: https://github.com/EvolutionaryGenomics/scalability-reproducibility-chapter
- Strozzi et al. 2019 DockerHub repository: https://hub.docker.com/u/evolutionarygenomics/

## 3. Task-1: Run snakemake on single node
### 3.1 Approach-1: build docker image using dockerfile
#### 3.1.1 findings
- The Dockerfile is out of date, need to make some minor changes to build docker image
- The out-of-date Dockerfile may reduce reproducibility, a better approach is pull pre-build docker image from DockerHub
- However, Dockerfile is important. Because it shows what is happening under the hood. And users can edit the file for their task.

#### 3.1.2 work log
- [x] create a VM on Jetstream using the image "CyberCarpentry_2019" 
- [x] create a volume and attach to the VM
- [x] clone the GitHub repository to the volume 
```
git clone https://github.com/EvolutionaryGenomics/scalability-reproducibility-chapter
```
- [x] build image using Dockerfile.snakemake
```
docker build -t snakemake-image -f Dockerfile.snakemake .
```
AND IT FAILED, below is the error message:
```
InvalidArchiveError('Error with archive /usr/local/pkgs/libgfortran-ng-7.3.0-hdf63c60_0.conda.  You probably need to delete and re-download or re-create this file.  Message from libarchive was:\n\nCan\'t initialize filter; unable to run program "zstd -d -qq" (errno=22, retcode=-30, archive_p=94069319569600)')
```
Problem should be in the Dockerfile.snakemake file
```
FROM conda/miniconda3
RUN conda config --add channels conda-forge
RUN conda install -y perl=5.22.0
RUN conda install -y -c bioconda paml=4.9 clustalo=1.2.4 wget=1.19.1
ADD pal2nal.pl /usr/local/bin/pal2nal.pl
RUN chmod +x /usr/local/bin/pal2nal.pl 

# For Snakemake
RUN conda install -y -c bioconda snakemake=4.2.0

```
We tried to fix it, see below
```
FROM conda/miniconda3
RUN conda config --add channels conda-forge
RUN conda install -y perl=5.22.0
RUN conda install -y -c bioconda paml=4.9 clustalo=1.2.4 wget=1.19.1
ADD pal2nal.pl /usr/local/bin/pal2nal.pl
RUN chmod +x /usr/local/bin/pal2nal.pl 

# For Snakemake
# RUN conda install -y -c bioconda snakemake=4.2.0 # THIS LINE IS WRONG
RUN pip install snakemake==4.2.0
```


## Questions & Answers
- Where (embarrassing) parallel is happening?

