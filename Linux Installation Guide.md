# Cookbook #0: Linux Installation Guide

Cookbook for new Robotics students

> by [SS47816](https://github.com/SS47816)
>
> @NUS Advanced Robotics Centre
>
> MIT License



## Prerequisite

* An USB stick (8G and above)
* Internet Connection
* [Ubuntu 18.04.4 ISO image](https://releases.ubuntu.com/18.04.4/)



## Installationengine/build/scripts/install_dependencies.sh

Please follow the respective guide based on your system:

#### Windows + Ubuntu

* Follow [this guide](https://itsfoss.com/install-ubuntu-1404-dual-boot-mode-windows-8-81-uefi/)

#### MacOS + Ubuntu

* [Video Tutorial](https://www.youtube.com/watch?v=kRgKlcm1XPI)



**Notes:** For either system, in the installation settings, please allocate at least 20 GB for `/root` and 20 GB for `/home`



### Nvidia GPU Driver + CUDA + cuDNN

Isaac SDK required pkg version:

* tensorflow

[Installation Guide](https://www.tensorflow.org/install/gpu)

[Installation Guide 2](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)

```bash
# Add NVIDIA package repositories
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.243-1_amd64.deb

sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub

sudo dpkg -i cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo apt-get update
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-430
# Reboot. Check that GPUs are visible using the command: nvidia-smi

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-10-1 \
    libcudnn7=7.6.4.38-1+cuda10.1  \
    libcudnn7-dev=7.6.4.38-1+cuda10.1


# Install TensorRT. Requires that libcudnn7 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer6=6.0.1-1+cuda10.1 \
    libnvinfer-dev=6.0.1-1+cuda10.1 \
    libnvinfer-plugin6=6.0.1-1+cuda10.1

```



```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-driver-440
```



### [Bazel](https://docs.bazel.build/versions/master/install-ubuntu.html)





## Developer Tools on Linux

### [Git](https://git-scm.com/): version control tool

run the following command in the Terminal:

```bash
sudo apt update
sudo apt install git
```

**Note: **When you use git for the first time on your machine, you need to set your user-name and your email address: 

```bash
# Set user-name
git config --global user.name "FIRST_NAME LAST_NAME"

# Set email address
git config --global user.email "MY_NAME@example.com"
```

If it is your own machine and you would like to save the trouble of typing your user-name and password every time, you may save them in the machine by running: ([as describled in this post](https://stackoverflow.com/questions/35942754/how-to-save-username-and-password-in-git-gitextension))

```bash
# Store credentials
git config --global credential.helper store

# Do something in git, for example
git pull
```



### [Typora](https://typora.io/): a markdown file editor

```bash
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -

# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update

# install typora
sudo apt-get install typora
```



### [Sublime Text](https://www.sublimetext.com/): a text editor

```bash
# Install the GPG key:
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

# Ensure apt is set up to work with https sources:
sudo apt-get install apt-transport-https

# Select the stable build version:
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list

# Update apt sources and install Sublime Text
sudo apt-get update
sudo apt-get install sublime-text
```



### [Anaconda](https://docs.anaconda.com/anaconda/install/linux/): a python virtual environment manager 

1. Download the Python 3.7 version Anaconda 64-Bit (x86) Installer from [here](https://www.anaconda.com/products/individual#linux), and save it to `~/Downloads/`

2. Run the following commands in the terminal:

```bash
# To use GUI packages with Linux, you will need to install the following extended dependencies for Qt:
apt-get install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6

bash ~/Downloads/Anaconda3-2020.02-Linux-x86_64.sh
```

3. Follow the instructions in the terminal, and press Enter Key and type in `yes` when requested

4. Installation successful!

5. Run 

   ```bash
   # Refresh your Terminal
   source ~/.bashrc
   
   # Disable the auto-activation of conda base environment (recommended)
   conda config --set auto_activate_base False
   ```

**User Guide:** 

* [Manage Environments](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* [Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

#### Create an new environment

To create a environment according to specifications stored in a file, run:

```bash
conda env create -f environment.yml
```



### [VS Code](https://code.visualstudio.com/): an Integrated Development Environment (IDE)

#### Recommended Extensions:

* C/C++
* Python
* Visual Studio IntelliCode
* GitLens
* ROS
* TabNine (Optional, it's magic!)