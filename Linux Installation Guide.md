# Cookbook #0: Linux Installation Guide

> by [SS47816](https://github.com/SS47816)
>
> This document is a part of the training materials for robotics students
>
> @NUS Advanced Robotics Centre
>
> MIT License



## Linux Install (Dual Boot)

### Prerequisite

* An USB stick (8G and above)
* Internet Connection
* [Ubuntu 18.04.4 ISO image](https://releases.ubuntu.com/18.04.4/)



### Installation

Please follow the respective guide based on your system:

#### Windows + Ubuntu

* Follow [this guide](https://itsfoss.com/install-ubuntu-1404-dual-boot-mode-windows-8-81-uefi/)

#### MacOS + Ubuntu

* [Video Tutorial](https://www.youtube.com/watch?v=kRgKlcm1XPI)



**Notes:** In the installation settings, please allocate at least 20G for /root and 20G for /home



## Developer Tools on Linux

### Git

run the following command in the Terminal:

```bash
sudo apt install git
```

### [Typora](https://typora.io/)

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

### [Sublime Text](https://www.sublimetext.com/)

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

### [Anaconda](https://docs.anaconda.com/anaconda/install/linux/)

* [Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

### [VS Code](https://code.visualstudio.com/)

#### Recommended Extensions:

* C/C++
* Python
* Visual Studio IntelliCode
* GitLens