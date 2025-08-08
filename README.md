# ElmerFEM_singularity
-----------------------------

This is a Singularity / Apptainer container image for the multiphysics FEM solver ElmerFEM developed by CSC.

For more info see their website : https://www.elmerfem.org/
and source code: https://github.com/ElmerCSC/elmerfem

## Download premade image:

- See Releases sections on the right ==>>

## Binary Sources

Base OS image: 
- Docker image of latest Ubuntu 22.04 Jammy image, 

Binaries:
- using Elmer FEM packages from official PPA: 
https://launchpad.net/~elmer-csc-ubuntu/+archive/ubuntu/elmer-csc-ppa
- Paraview binaries from Kitware:
https://www.paraview.org/download/

## Build procedure

1. make build dir :
```
mkdir ElmerFEM_singularity
cd ElmerFEM_singularity
```

2. Get Ubuntu Jammy base image and rename
```
singularity pull docker://ubuntu:22.04
mv ubuntu_22.04.sif ElmerFEM.sif
```
3. Build sandbox to modify image

```
singularity build --sandbox ElmerFEM.sif
rm ElmerFEM.sif
```

4. Enter image in write mode for customization
```
sudo singularity shell --writable ElmerFEM
```

5. in singularity shell , install ELMER from PPA

```
apt update
apt upgrade
apt install software-properties-common
add-apt-repository ppa:elmer-csc-ubuntu/elmer-csc-ppa
apt update 
apt install  nano wget
apt install  elmerfem-csc-eg 
apt install elmerfem-csc
```

Now we need to install paraview, but Ubuntu 22.04 version is not working properly (error reading result files...)

So let's get paraview 5.12 binaries from kitware: on your local machine, download:
https://www.paraview.org/paraview-downloads/download.php?submit=Download&version=v5.12&type=binary&os=Linux&downloadFile=ParaView-5.12.1-MPI-Linux-Python3.10-x86_64.tar.gz

copy it to /tmp ( !!! run this in a NORMAL shell, not in singularity shell)
```
cp ~/Downloads/ParaView-5.12.1-MPI-Linux-Python3.10-x86_64.tar.gz /tmp
```

Now back to Singularity Shell: we uncompress the paraview binaries to /opt and make a symlink to start that paraview "seamlessly"
```
cd /opt/

tar zxvf /tmp/ParaView-5.12.1-MPI-Linux-Python3.10-x86_64.tar.gz 

mv ParaView-5.12.1-MPI-Linux-Python3.10-x86_64  paraview

ln -s /opt/paraview/bin/paraview /usr/local/bin/paraview

```

6. set start script as ElmerGUI and leave singularity shell
```
echo '#!/bin/sh
exec ElmerGUI "${@}"'  > /singularity

exit
```
7. test 
```
singularity run ElmerFEM
```
=> it should run ElmerGUI and you can try to build a model etc...
8. build final SIF image
```
sudo singularity build ElmerFEM.sif ElmerFEM
```

## How to use
- install singularity or apptainer for your Linux distro (both should work, jsut substitute apptainer instead of singularity if you choose that).

- basic use: run ElmerGUI by simply running the image with singularity 
```
singularity run ElmerFEM.sif
```

- "expert" use: run interactive shell session and start your commands from there
```
singularity shell ElmerFEM.sif
```


