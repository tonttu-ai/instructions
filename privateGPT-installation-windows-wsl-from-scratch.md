# Running privateGPT on Windows 11 using WSL and CUDA

## Introduction
- Brief explanation about privateGPT
- Overview of WSL (Windows Subsystem for Linux)



## Prerequisites
- Windows 11
- A modern NVIDA GPU
- VS Code
- bash on Ubuntu 
- 
### Installing Linux on Windows with WSL
[Install WSL](https://learn.microsoft.com/en-us/windows/wsl/install)

### Installing the latest NVIDIA drivers for Windows 11
[Install latest NVIDIA drivers](https://www.nvidia.com/Download/index.aspx)

### Configuration of the WSL distribution
```bash
# update the distribution
sudo apt-get update && sudo apt-get upgrade
# install build-essential, including kernel headers and compilers
sudo apt install build-essential libc6-dev g++ gcc make dpkg-dev locate
sudo apt-get install python3 python-is-python3
```

### Installation of python3.11
```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.11
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 2
sudo update-alternatives --config python3
```

check python version (should be >= 3.11)
```bash
python --version
```

reinstall pip [Link](https://cloudbytes.dev/snippets/upgrade-python-to-latest-version-on-ubuntu-linux)
```bash
sudo apt remove --purge python3-apt
sudo apt autoclean
sudo apt install python3-apt

sudo apt-get install pip
sudo apt install python3.11-distutils
sudo apt autoremove
sudo apt install python3.11-venv
```

### Installation of the NVIDIA CUDA tool kit for WSL-Ubuntu
[Download and installation Instructions](https://developer.nvidia.com/cuda-downloads)
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.3.1/local_installers/cuda-repo-wsl-ubuntu-12-3-local_12.3.1-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-3-local_12.3.1-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-3-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-3
```

Add the CUDA binaries to your environment variable PATH in your ~/.bashrc file
```bash
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
```

The utility nvidia-smi seems to be hidden away in another path... I found it under /usr/lib/wsl/lib and therefore created a symbolic link in my /usr/local/bin
```bash
cd /usr/local/bin/ && sudo ln -s /usr/lib/wsl/lib/nvidia-smi
```

Test that all components of the CUDA toolkit were install by running following command:
```bash
nvcc --version
nvidia-smi
```

Now we are going to install the llama.cpp libraries for enabling GPU acceleration
```bash
cd ~/privateGPT
CMAKE_ARGS='-DLLAMA_CUBLAS=on' poetry run pip install --force-reinstall --no-cache-dir llama-cpp-python
```

### Back up of WSL2 environment 
so that it can be easily restored at a laster stage in case the privateGPT installation fails
#### open PowerShell command prompt
```PS
wsl.exe --export <DistributionName> <FileName>
```
