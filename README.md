# CUDA Setup on Linux

This guide will walk you through installing **CUDA drivers** on a Linux machine, step by step.  Before starting setup make sure you know your `GPU series` and `Ubuntu version`.
We’ll begin by removing any previously installed NVIDIA drivers or CUDA remnants to ensure a clean setup.

> Check your [CUDA GPU Compute Capability](https://developer.nvidia.com/cuda-gpus)

---

## Step 1: Remove Preinstalled NVIDIA Drivers

Run the following commands to completely remove any existing NVIDIA drivers and CUDA installations:

```bash
# Purge old NVIDIA drivers
sudo apt-get purge nvidia*

# Remove NVIDIA packages
sudo apt remove nvidia-*

# Remove any CUDA repository lists
sudo rm /etc/apt/sources.list.d/cuda*

# Clean up unused dependencies and packages
sudo apt-get autoremove && sudo apt-get autoclean

# Remove old CUDA directories
sudo rm -rf /usr/local/cuda*
```

---

## Step 2: Verify CUDA-Capable GPU

Before installing CUDA, make sure your system actually has a **CUDA-capable NVIDIA GPU**.  
To check run the following command:

```bash
lspci | grep -i nvidia
```

If your GPU shows up, you’re good to go.
For detailed compatibility, check the official [CUDA Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/backend/latest/reference/support-matrix.html)

## Step 3: Update Your System
```bash
sudo apt-get update
sudo apt-get upgrade -y
```

## Step 4: Install Required Dependencies
Now, install the essential packages and development libraries needed for CUDA:
```bash
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
```
Then add the official **NVIDIA graphics drivers PPA**:
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
```
---

## Step 5: Install CUDA Driver

First, check your GPU’s **compatible CUDA driver version** in the  
[CUDA Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/backend/latest/reference/support-matrix.html).

Then, visit the [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) to download the correct version for your system.  

In this guide, we’ll install **CUDA Toolkit 11.5**.  

Run the following commands:

```bash
# Download the CUDA repository pin
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600

# Download the CUDA 11.5 local installer
wget https://developer.download.nvidia.com/compute/cuda/12.8.0/local_installers/cuda-repo-ubuntu2204-12-8-local_12.8.0-570.86.10-1_amd64.deb

# Install the local CUDA repository package
sudo dpkg -i cuda-repo-ubuntu2204-12-8-local_12.8.0-570.86.10-1_amd64.deb

# Add the CUDA GPG key
sudo cp /var/cuda-repo-ubuntu2204-12-8-local/cuda-*-keyring.gpg /usr/share/keyrings/

# Update package lists
sudo apt-get update

# Install CUDA
sudo apt-get -y install cuda-toolkit-12-8
```
> **⚠️ Note:** When you select a specific version of `CUDA` from [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive), run the corresponding commands from the archive.

---

## Step 6: Configure Environment Variables

After installing CUDA, you need to set up your **environment variables** so the system can locate CUDA binaries and libraries.

Run the following commands:

```bash
# Add CUDA to your PATH
echo 'export PATH=/usr/local/cuda-12.8/bin:$PATH' >> ~/.bashrc

# Add CUDA libraries to your LD_LIBRARY_PATH
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-12.8/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

# Reload your shell configuration
source ~/.bashrc

# Update the dynamic linker runtime bindings
sudo ldconfig
```
> **⚠️ Note:** Change `cuda-12.8`  from the path `/usr/local/cuda-12.8` according to your cuda version.

---

## Step 7: Install cuDNN Library

CUDA alone isn’t enough for deep learning frameworks — you’ll also need **cuDNN** (NVIDIA’s GPU-accelerated library for deep neural networks).

1. Check your **CUDA-compatible cuDNN version** in the [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive).  
2. Download the appropriate **tar file** for your CUDA version.  

Example for `cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz`:

```bash
# Extract the cuDNN tar file
tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz

# Copy header files
sudo cp -P cuda/include/cudnn.h /usr/local/cuda-11.5/include
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/include/cudnn.h /usr/local/cuda-12.4/include

# Copy library files
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/lib64/libcudnn* /usr/local/cuda-12.4/lib64/
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/lib/libcudnn* /usr/local/cuda-12.4/lib64/

# Set permissions
sudo chmod a+r /usr/local/cuda-12.4/lib64/libcudnn*
```

> **⚠️ Note:** Change `cuda-12.4` and `cudnn-linux-x86_64-8.9.7.29_cuda12-archive` according to your `CUDA` and `cuDNN` version.

Finally, reboot your system:
```bash
sudo reboot
```

---

## Step 8: Verify Installation

After completing the installation of **CUDA** and **cuDNN**, verify that everything is working correctly.

### 1. Check NVIDIA Driver
Run the following to ensure your GPU driver is loaded:

```bash
nvidia-smi
```
### 2. Check CUDA Version

Confirm that nvcc (the CUDA compiler) is installed and accessible:

```bash
nvcc --version
```
You should see the installed CUDA version (e.g., release 12.4).

<!-- ### 3. Check cuDNN Installation

Check whether cuDNN is correctly installed by running:

```bash
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```
This should display the installed cuDNN version.

--- -->

## Conclusion

You’ve successfully installed and configured **NVIDIA Drivers, CUDA Toolkit, and cuDNN** on your Linux system.  
With this setup, your machine is now ready to accelerate **deep learning, machine learning, and GPU-powered workloads**.

If you run into issues, double-check compatibility in the official documentation:  
- [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)  
- [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)  
- [CUDA Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/backend/latest/reference/support-matrix.html)
- [CUDA GPU Compute Capability](https://developer.nvidia.com/cuda-gpus)
