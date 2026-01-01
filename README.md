# CUDA Setup on Linux

Welcome! This step-by-step guide walks you through installing **NVIDIA drivers**, the **CUDA Toolkit**, **cuDNN**, and related libraries on an Ubuntu system. It was written and tested on an **NVIDIA GeForce RTX 5060** running **Ubuntu 22.04.5 LTS**; if you have a different GPU or Ubuntu release, these instructions generally still apply — just double-check compatibility and version numbers before downloading packages.

Before you begin, confirm your `GPU model` and `Ubuntu version`. We'll start by removing any previously installed NVIDIA drivers and leftover CUDA files to ensure a clean installation.

---

## Step 1: Remove Preinstalled NVIDIA Drivers

Run the following commands to completely remove any existing NVIDIA drivers and CUDA installations:

```bash
sudo apt purge '^nvidia-.*'

# Remove any CUDA repository lists
sudo rm /etc/apt/sources.list.d/cuda*

# Clean up unused dependencies and packages
sudo apt autoremove --purge && sudo apt clean

# Remove any CUDA repository and old CUDA directories
sudo rm -rf /usr/local/cuda*
sudo rm /etc/apt/sources.list.d/cuda*
```

---

## Step 2: Verify CUDA-Capable GPU

Before installing CUDA, confirm your system has a **CUDA-capable NVIDIA GPU**. To check, run:

```bash
lspci | grep -i nvidia
```

If your GPU shows up, you’re good to go.

## Step 3: Check Available Drivers

```bash
ubuntu-drivers devices
```

You should now see something like:
```
nvidia-driver-580-open (recommended)
```

Install the recommended NVIDIA driver:
```bash
sudo apt install nvidia-driver-580-open
sudo reboot
```

To check installation:
```bash
nvidia-smi
```

## Step 4: Install Other Dependencies
Install the essential packages and development libraries required by CUDA:
```bash
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
```
Next, add the official **NVIDIA graphics drivers PPA**:
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
```
---

## Step 5: Install CUDA Driver

First, check your GPU’s compatible **CUDA driver version** by running `nvidia-smi`. You'll see the compatible CUDA version in the top‑right of the output (for example, "CUDA Version: 13.0"). Then visit the [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) to download the matching toolkit and follow the installation instructions provided there.

---

## Step 6: Configure Environment Variables

After installing CUDA, set up your **environment variables** so your shell can find CUDA binaries and libraries.

Run the following commands:

```bash
# Add CUDA to your PATH
echo 'export PATH=/usr/local/cuda-13.0/bin:$PATH' >> ~/.bashrc

# Add CUDA libraries to your LD_LIBRARY_PATH
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-13.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

# Reload your shell configuration
source ~/.bashrc

# Update the dynamic linker runtime bindings
sudo ldconfig
```
> **⚠️ Note:** Change `cuda-13.0` in the path `/usr/local/cuda-13.0` to match your installed CUDA version.

---

## Step 7: Install cuDNN Library

For deep learning frameworks, you'll also want **cuDNN** (NVIDIA’s GPU-accelerated library for deep neural networks).

1. Check which cuDNN versions are compatible with your CUDA installation on the [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive).  
2. Download the appropriate **tar file** for your CUDA version.  

Example for `cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz`:

```bash
# Extract the cuDNN tar file
tar -xvf cudnn-linux-x86_64-8.9.7.29_cuda12-archive.tar.xz

# Copy header files
sudo cp -P cuda/include/cudnn.h /usr/local/cuda-13.0/include
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/include/cudnn.h /usr/local/cuda-13.0/include

# Copy library files
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/lib64/libcudnn* /usr/local/cuda-13.0/lib64/
sudo cp -P cudnn-linux-x86_64-8.9.7.29_cuda12-archive/lib/libcudnn* /usr/local/cuda-13.0/lib64/

# Set permissions
sudo chmod a+r /usr/local/cuda-13.0/lib64/libcudnn*
```

> **⚠️ Note:** Change `cuda-13.0` and `cudnn-linux-x86_64-8.9.7.29_cuda12-archive` according to your `CUDA` and `cuDNN` version.

Finally, reboot your system:
```bash
sudo reboot
```

---

## Step 8: Verify Installation

Once **CUDA** and **cuDNN** are installed, verify that everything is working correctly.

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

## Troubleshooting
If, after installing the recommended NVIDIA driver, running `nvidia-smi` shows an error such as `NVRM: This PCI I/O region assigned to your NVIDIA device is invalid`, it often means a kernel parameter related to PCI resource allocation needs adjusting.

To update the GRUB configuration, edit the file below:
```bash
sudo nano /etc/default/grub
```

Locate the line:
`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"`

Change it to one of the following options:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pci=realloc"
```
If that does not resolve the problem, try:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pci=realloc=off"
```

Save the file, update GRUB, and reboot:
```bash
sudo update-grub
sudo reboot
```

## Conclusion

You have successfully installed and configured **NVIDIA Drivers**, the **CUDA Toolkit**, and **cuDNN** on your Ubuntu system. With this setup, your machine is ready to accelerate **deep learning** and other GPU-powered workloads.

If you run into issues, check compatibility and guidance in the official documentation:  
- [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)  
- [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)  
- [CUDA Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/backend/latest/reference/support-matrix.html)
- [CUDA GPU Compute Capability](https://developer.nvidia.com/cuda-gpus)
- [NVRM: This PCI I/O region assigned to your NVIDIA device is invalid](https://forums.developer.nvidia.com/t/nvrm-this-pci-i-o-region-assigned-to-your-nvidia-device-is-invalid/229899)