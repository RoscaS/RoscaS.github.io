---
layout: post
title: "Py: openCV install(linux)"
subtitle: "Computer Vision"
date: 2017-08-17
author: Sol
category: Py
tags: divers, fr, en
finished: false
mathjax: true
---

## links

* [learn openCV](https://www.learnopencv.com/install-opencv3-on-ubuntu/)

# Step by step Install OpenCV + Python3 virtualenv virtualenvwrapper

## 1 Update packages
```
sudo apt-get update
sudo apt-get upgrade
```

## 2 Install OS libraries

### 2.1 Remove any previous installation of x264

```
sudo apt-get remove x264 libx264-dev
```

### 2.2 Install dependencies

>Ne pas hésiter à s'aider de `Synaptic Package Manager` si il ne trouve pas certaines libraires à la ligne de commande.

```
sudo apt-get install build-essential checkinstall cmake pkg-config yasm gfortran git
sudo apt-get install libjpeg8-dev libjasper-dev libpng12-dev
# If you are using Ubuntu 14.04
sudo apt-get install libtiff4-dev
# If you are using Ubuntu 16.04
sudo apt-get install libtiff5-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libdc1394-22-dev
sudo apt-get install libxine2-dev libv4l-dev
sudo apt-get install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt-get install libqt4-dev libgtk2.0-dev libtbb-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libfaac-dev libmp3lame-dev libtheora-dev
sudo apt-get install libvorbis-dev libxvidcore-dev
sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt-get install x264 v4l-utils
```

### 2.3 Optional dependencies

```
sudo apt-get install libprotobuf-dev protobuf-compiler
sudo apt-get install libgoogle-glog-dev libgflags-dev
sudo apt-get install libgphoto2-dev libeigen3-dev libhdf5-dev doxygen
```

## 3 Install Python libraries

> Très probablement déja là

```
sudo apt-get install python-dev python-pip python3-dev python3-pip
sudo -H pip3 install -U pip numpy
```

### 3.1 Python virtualenv (bash)

```py
# Install virtual environment
sudo pip3 install virtualenv virtualenvwrapper

# si bash: (normalement la plus part du monde)
echo "# Virtual Environment Wrapper"  >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
source ~/.bashrc

# create virtual environment
mkvirtualenv test-py3
workon test-py3
  
# install python libraries within this virtual environment
pip install numpy scipy matplotlib scikit-image scikit-learn ipython
  
# quit virtual environment
deactivate
```

#### 3.1.1 virtualenv avec ZSH

Si `ZSH` est utilisé à la place de `bash`, ouvrir le fichier `~/.zshrc` et y ajouter:

```py
# virtualenv and virtualenvwrapper
export WORKON_HOME=~/.venvs
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
export PIP_VIRTUALENV_BASE=~/.venvs
```
Pour ajouter le nom du venv courant au prompt, on ajoute `virtualenv` dans un des prompts dans le même fichier. Exemple:

```
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(virtualenv dir_joined dir_writable_joined status context_joined)
```

Pour changer les couleurs:

```
POWERLEVEL9K_VIRTUALENV_BACKGROUND='clear'
POWERLEVEL9K_VIRTUALENV_FOREGROUND='green'
```

#### Python virtualenv (with zsh) CheatSheet
* [install(linux)](http://bicofino.io/2015/02/10/install-virtualenv-and-virtualenvwrapper-on-ubuntu-14-dot-04-2/)
* [virtualenv doc](https://python-guide.readthedocs.io/en/latest/dev/virtualenvs/)

* `mkvirtualenv` _name_ $ \Rightarrow $ new virtualenv
* `workon` _name_ $ \Rightarrow $ switch to "name" virtualenv
* `workon` $ \Rightarrow $ list virtualenvs
* `lsvirtualenv` $ \Rightarrow $ same
* `deactivate` $ \Rightarrow $ leave current virtualenv
* `virtualenv --version` $ \Rightarrow $ check version

* `mkproject` _name_ $ \Rightarrow $ creates a project **directory** 
* `deactivate` $ \Rightarrow $ leave current project

* `rmvirtualenv` _name_ $ \Rightarrow $ delete project
* `cdvirtualenv` $ \Rightarrow $ navigate into the directory of the currently activated venv.
* `lssitepackages` $ \Rightarrow $ show content of `site-packages` directory

## 4 Download OpenCV & OpenCV_contrib

```
git clone https://github.com/opencv/opencv.git
cd opencv 
git checkout 2b44c0b6493726c465152e1db82cd8e65944d0db 
cd ..
```

```
git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout abf44fcccfe2f281b7442dac243e37b7f436d961
cd ..
```

## 5 Compile and install OpenCV with contrib modules


### 5.1 Create build directory inside OpenCV dir
```
cd opencv
mkdir build
cd build
```

### 5.2 Run CMake
```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D INSTALL_C_EXAMPLES=ON \
      -D INSTALL_PYTHON_EXAMPLES=ON \
      -D WITH_TBB=ON \
      -D WITH_V4L=ON \
      -D WITH_QT=ON \
      -D WITH_OPENGL=ON \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D BUILD_EXAMPLES=ON ..
```

### 5.3 Compile and install
```
sudo make install
sudo sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
```

### 5.4 Create symlink in virtual environment

Les binaires de OpenCV (cv2.so) penvent ếtre installés soit dans le fichier `site-packages` soit `dist-packages`. La commande suivante nous permet de connaitre le bon endroit:

```
find /usr/local/lib/ -type f -name "cv2*.so"
```

Cela devrait retourner des chemins d'acces similaires à un des suivants:
```py
# dist-packages:
/usr/local/lib/python3.5/dist-packages/cv2.cpython-35m-x86_64-linux-gnu.so
/usr/local/lib/python3.6/dist-packages/cv2.cpython-36m-x86_64-linux-gnu.so
# site-packages:
/usr/local/lib/python3.5/site-packages/cv2.cpython-35m-x86_64-linux-gnu.so
/usr/local/lib/python3.6/site-packages/cv2.cpython-36m-x86_64-linux-gnu.so
```

<span style="color:red"> Bien vérifier le chemin de **notre machine** avant de lancer la commande suivante: </span> 

```
cd ~/.virtualenvs/facecourse-py3/lib/python3.6/site-packages
ln -s /usr/local/lib/python3.6/dist-packages/cv2.cpython-36m-x86_64-linux-gnu.so cv2.so
```

## 6 Test OpenCV3

Télécharger [RedEyeRemover.zip](http://www.learnopencv.com/wp-content/uploads/2017/06/RedEyeRemover.zip) et l'extraire.


### 6.1 Test C++ code
Dans le fichier extrait, compiler et lancer:

```py
# compile
# There are backticks ( ` ) around pkg-config command not single quotes
g++ -std=c++11 removeRedEyes.cpp `pkg-config --libs --cflags opencv` -o removeRedEyes
# run
./removeRedEyes
```

### 6.2 Test Python code

#### Activate Python venv
```
workon facecourse-py3
```

#### Quick check
```
# open ipython (run this command on terminal)
ipython
# import cv2 and print version (run following commands in ipython)
import cv2
print cv2.__version__
# If OpenCV3 is installed correctly, the above command should give output 3.2.0
# Press CTRL+D to exit ipython
```

#### Demo RedEyeRemover

```
python removeRedEyes.py
deactivate
```

<span style="color:red"> A chaque fois qu'on veut travailler avec des scripts Python qui utilisent OpenCV, on devrait le faire à partir du venv créé ici. </span>


# Introduction

## Lire une image:

`cv2.imread('image', flag)` 

* `image` doit soit être dans le working directory, soit on utilise un full path
* `flag` spécifie la façon dont `image` doit être lue:
  * `1` $ \Rightarrow $ `cv2.IMREAD_COLOR` $ \Rightarrow $ img couleur, toute transarence est négligée (flag par défaut)
  * `0` $ \Rightarrow $ cv2.IMREAD_GRAYSCALE $ \Rightarrow $ grayscale mode
  * `-1` $ \Rightarrow $ cv2.IMREAD_UNCHANGED $ \Rightarrow $ image originale


```python
import numpy as np
import cv2

# Load an color image in grayscale
img = cv2.imread('messi.jpg', 0)
```

<span style="color:red"> Si le path est erroné, aucune erreur n'apparait mais print(img) retourne `None` </span> 

## Afficher une image:


`cv2.imshow('window_name', image)` $ \Rightarrow $ affiche l'image dans une nouvelle fenêtre (fit to the image size).

* `'window_name'` $ \Rightarrow $ une string avec le nom de la nouvelle fenêtre
* `image` $ \Rightarrow $ image à afficher

> Nous pouvons afficher autant d'images qu'on veut mais les fenêtres doivent porter des noms différents

```python
import numpy as np
import cv2

# Load an color image in grayscale
img = cv2.imread('messi.jpg', 0)

cv2.imshow('image', img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![alt](http://opencv-python-tutroals.readthedocs.io/en/latest/_images/opencv_screenshot.jpg)

* `cv2.waitKey()` $ \Rightarrow $ permet de binder une touche (plus de détails suivent)
* `cv2.destroyAllWindows()` $ \Rightarrow $ détruit toutes les fenếtres créés
* `cv2.destroyWindow()` $ \Rightarrow $ détruit une fenêtre spécifique passée en argument

* `cv2.namedWindow('image', flag)` $ \Rightarrow $ permet de set la taille de la fenêtre via l'argument `flag`:
  * `cv2.WINDOW_AUTOSIZE` $ \Rightarrow $ valeur par défaut
  * `cv2.WINDOW_NORMAL` $ \Rightarrow $ fenêtre redimensionnable

> `q` pour quitter la lecture d'image

```python
import numpy as np
import cv2

# Load an color image in grayscale
img = cv2.imread('hills.jpg', 0)

cv2.namedWindow('image', cv2.WINDOW_NORMAL)
cv2.imshow('image', img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```




