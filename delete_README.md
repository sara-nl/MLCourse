# HPC & BIG DATA - UvA 2020

Repository to host contents of the High Performance Machine Learning course within HPC & BIG DATA in 2020

# Setup

Start up a JupyterHub instance by clicking the link:    
```
https://jupyter2.lisa.surfsara.nl/course
```
- Login with your username sdemoXXX and password
- Select the **uva_ml_course** from the dropdown menu
- Wait for the server to be spawned...
- Good to go!

> We are going to use this JupyterHub instance on Lisa GPU for the practicals today. 

- Now clone the repository with the following command:

```
## Dependencies
For this installation we will first make sure to have a folder ```/home/examode/``` and in here we make a folder called ```lib_deps``` where we install all the dependencies needed by the code.
```
mkdir -p $HOME/examode/lib_deps
cd $HOME/examode
```
Then add the relevant values to the environment variables:
```
export PATH=/home/$USER/examode/lib_deps/bin:$PATH
export LD_LIBRARY_PATH=/home/$USER/examode/lib_deps/lib64:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/$USER/examode/lib_deps/lib:$LD_LIBRARY_PATH
export CPATH=/home/$USER/examode/lib_deps/include:$CPATH
```

### LibTIFF
1. Download a release from the official repository and untar
```
wget http://download.osgeo.org/libtiff/tiff-4.0.10.tar.gz
tar -xvf tiff-4.0.10.tar.gz
```
2. Build and configure the LibTIFF code from the inflated folder
```
cd $HOME/examode/tiff-4.0.10
CC=gcc CXX=g++ ./configure --prefix=/home/$USER/examode/lib_deps
make -j 8
```
3. Install LibTIFF
```
make install
cd ..
```

### OpenJPEG
The official install instructions are available [here](https://github.com/uclouvain/openjpeg/blob/master/INSTALL.md).
1. Download and untar a release from the official repository
```
wget https://github.com/uclouvain/openjpeg/archive/v2.3.1.tar.gz
tar -xvf v2.3.1.tar.gz
```
2. Build the OpenJPEG repository code
```
cd $HOME/examode/openjpeg-2.3.1
mkdir -p build 
CC=gcc CXX=g++ cmake -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_INSTALL_PREFIX=/home/$USER/examode/lib_deps \
-DBUILD_THIRDPARTY:bool=on
make -j 8

```
3. Install OpenJPEG (we already added the paths to the environment variables)
```
make install
cd ..
```

### OpenSlide
1. Download and untar a release from the official repository
```
wget https://github.com/openslide/openslide/releases/download/v3.4.1/openslide-3.4.1.tar.gz
tar -xvf openslide-3.4.1.tar.gz
```
2. Build and configure the OpenSlide code
```
cd $HOME/examode/openslide-3.4.1
CC=gcc CXX=g++ PKG_CONFIG_PATH=/home/$USER/examode/lib_deps/lib/pkgconfig ./configure --prefix=/home/$USER/examode/lib_deps
make -j 8
```
3. Install OpenSlide (we already added the paths to the environment variables)
```
make install
cd ..
```

### cuDNN
This is needed for efficient GPU utilization
1. Download cuDNN from the [NVIDIA website](https://developer.nvidia.com/rdp/cudnn-download). You need a personal license. Please mind to download the version that corresponds to CUDA 10.0 (not 10.1).
2. Then unarchive in your ```lib_deps``` folder. Please mind that inflating the archive also creates a ```cuda``` folder, we want to move the contents to the top folder, so that in the ```lib_deps``` we will have this structure:
```bin  include	lib  lib64  share```

Since we already added the paths in a previous step we can just use the library now.

### Setting up the Python depencies (specific to LISA GPU)
First we will load the modules needed:
```
module purge
module load 2019
module load Python/3.6.6-foss-2018b
module load CUDA/10.0.130
module load cuDNN/7.6.3-CUDA-10.0.130
module load NCCL/2.4.7-CUDA-10.0.130
module unload GCC
module unload GCCcore
module unload binutils
module unload zlib
module load GCC/8.2.0-2.31.1
module unload compilerwrappers
```
Then export the paths to the local installations:
```
export PATH=/home/$USER/examode/lib_deps/bin:$PATH
export LD_LIBRARY_PATH=/home/$USER/examode/lib_deps/lib64:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/$USER/examode/lib_deps/lib:$LD_LIBRARY_PATH
export CPATH=/home/$USER/examode/lib_deps/include:$CPATH
```
This will give us access to all the dependencies. Now on a GPU node (for example login-gpu1):
```
VIRTENV=openslide
VIRTENV_ROOT=~/.virtualenvs
virtualenv $VIRTENV_ROOT/$VIRTENV --system-site-packages
source $VIRTENV_ROOT/$VIRTENV/bin/activate
```
Now export environment variables for installing Horovod w/ MPI for multiworker training:
```
export HOROVOD_CUDA_HOME=$CUDA_HOME
export HOROVOD_CUDA_INCLUDE=$CUDA_HOME/include
export HOROVOD_CUDA_LIB=$CUDA_HOME/lib64
export HOROVOD_NCCL_HOME=$EBROOTNCCL
export HOROVOD_GPU_ALLREDUCE=NCCL
export MPICC=mpicc
export MPICXX=mpicpc
export HOROVOD_MPICXX_SHOW="mpicxx --showme:link"

```
Install python packages:

```
pip3 install tensorflow-gpu --no-cache-dir
pip3 install horovod --no-cache-dir

```
# Preprocessing on LISA

- To start pre-processing on e.g. CAMELYON17:
```
cd ~/examode/deeplab/CAMELYON17_PREPROCESSING
# See flags in run_prepro.sh file for options
sh run_prepro.sh

```
# Running on LISA
To start a training run on LISA with the CAMELYON16 dataset, image size 1024 and batch size 2:
```
python train.py --dataset 16 --img_size 1024 --batch_size 2
```
OR (for multi-worker)
```
mpirun -map-by ppr:4:node -np 4 -x LD_LIBRARY_PATH -x PATH -mca pml ob1 -mca btl ^openib python train.py --dataset 16 --img_size 1024 --batch_size 2
```
See the * *run_deeplab_exa.sh* * file for details

A training run on positive patches of 1024 x 1024 will converge in 2 hours on 4 TITANRTX nodes (Batch Size 2, ppr:4:node) to mIoU ~ 0.90
