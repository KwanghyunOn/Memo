# Ubuntu 18.04 setup for deep learning

A clean installation of Ubuntu 18.04.4 LTS was used.

This document includes installation of CUDA and cuDNN.

<br>

## (STEP 0)

### 0.1 - update apt

```$ sudo apt-get update```

### 0.2 - install gcc

```$ sudo apt-get install build-essential```

<br>

## (STEP 1) Install NVIDIA driver

### 1.1 - check current driver

```$ sudo lshw -C display```

It will show ```configuration: driver=nouveau``` somewhere in the middle, which means the current driver is nouveau driver.

### 1.2 - check recommended driver

```$ ubuntu-drivers devices```

It will show recommended driver for the GPU device.

### 1.3 - install recommended driver

```$ sudo ubuntu-drivers autoinstall```

### 1.4 - verify the installation

```$ nvidia-smi```

or

```$ sudo lshw -C display```

and check the driver has changed to nvidia; ```configuration: driver=nvidia```.

<br>

## (STEP 2) Install CUDA 10.1

### 2.1 - install CUDA 10.1

Follow [Pre-installation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pre-installation-actions), [Ubuntu-installation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#ubuntu-installation), [Post-installation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions) in [official CUDA toolkit documentation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

#### 2.1.1 - install linux-generic

When installing linux-generic in pre-installation, you can just type

```$ sudo apt-get install linux-headers-generic```

without checking the kernel version.

### 2.2 - update PATH variable in ~/.bashrc

Add the following lines to the end of ```~/.bashrc```

```
export PATH="/usr/local/cuda-10.1/bin:/usr/local/cuda-10.1/NsightCompute-2019.1:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-10.1/lib64:$LD_LIBRARY_PATH"
```

(```.../cuda-10.1/lib:...``` for 32bit users.)

Reboot and check PATH variable.

```
$ echo $PATH
$ echo $LD_LIBRARY_PATH
```

### 2.3 - verify the installation

```$ nvidia-smi```

This will show nothing if the installation has failed or PATH variable hasn't been updated properly.

<br>

## (STEP 3) Install cuDNN

### 3.1 - install cuDNN

Follow [official cuDNN installation guide](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html) to install cuDNN.

### 3.2 - verify the installation

```$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2```

should show something like

```
#define CUDNN_MAJOR 7
#define CUDNN_MINOR 6
#define CUDNN_PATCHLEVEL 5
--
#define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)

#include "driver_types.h"
```

<br>

## (STEP 4) Install TensorFlow 2

[Official tensorflow installation guide](https://www.tensorflow.org/install)

### 3.1 - install pip, setuptools

```
$ sudo apt update
$ sudo apt install python3-pip
$ pip3 --version
$ sudo pip3 install pip --upgrade
$ pip --version

$ sudo apt install python3-dev python3-setuptools
$ pip show setuptools		// version should be >= 40.3.0
```

### 3.2 - install tensorflow

```
$ pip install tensorflow
$ pip show tensorflow
```

### 3.3 - verify the installation

There is a [tensorflow test code](https://github.com/KwanghyunOn/Memo/blob/master/test_tf.py) in my github.

```
$ python3 test_tf.py
```

<br>

## (STEP 5) Install Docker

### 3.1 - install docker

Follow [official docker installation guide](https://docs.docker.com/install/linux/docker-ce/ubuntu/).

### 3.2 - run docker without sudo

[Docker docs link for this step](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)

```
$ sudo usermod -aG docker $USER
$ reboot
$ docker run hello-world
```

### 3.3 - install nvidia-docker

[Official nvidia-docker installation guide](https://github.com/NVIDIA/nvidia-docker)

```
# Add the package repositories
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
$ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
$ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# Install nvidia-docker
$ sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
$ sudo systemctl restart docker

# Verify the installation
$ docker run --gpus all --rm nvidia/cuda nvidia-smi
```

### 3.4 - (optional) install tensorflow docker image

[TensorFlow docker guide](https://www.tensorflow.org/install/docker?hl=ko)

```
$ docker pull tensorflow/tensorflow:2.1.0-gpu-py3
```

<br>

