# Anztec-iMX7-Terminal-Project

## Build Environment Setup A5 colibri imx7

### Operating System and Build Dependencies

Ubuntu and Debian
```
$ sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev zstd liblz4-tool file locales libacl1
```
```
$ sudo locale-gen en_US.UTF-8
```
### Repo and Git

Install the repo bootstrap binary:
```
$ mkdir bin
$ export PATH=bin:$PATH
$ curl https://commondatastorage.googleapis.com/git-repo-downloads/repo > bin/repo
$ chmod a+x bin/repo
```
Repo uses Git. Make sure you have it installed and your user name and e-mail address configured.
```
$ sudo apt install git
$ git config --global user.name "user-name"
$ git config --global user.email <example@example.com>
```
### Installation

#### First-time Configuration

Create a directory for your OE-Core setup to live in and clone the meta-information: 
```
$ mkdir ${HOME}/oe-core
$ cd ${HOME}/oe-core
$ repo init -u git://git.toradex.com/toradex-manifest.git -b kirkstone-6.x.y -m tdxref/default.xml
$ repo sync
```
source the file export to setup the environment
```
$ . export
```
#### Adjust the settings in the local.conf file

Adapt `build/conf/local.conf` to your needs

The `MACHINE`variable specifies the target device for the image. Set this variable to the module type
you are planning to build for.

| MACHINE NAME       | Corresponding Toradex Module            |
|--------------------|-----------------------------------------|
| colibri-imx7       | Colibri iMX7S 256MB and iMX7D 512MB     |
| colibri-imx7-emmc  | Colibri iMX7D 1GB                       |

open the `local.conf` and add the `MACHINE`
```
MACHINE ?= "colibri-imx7-emmc"
```
#### Add the Anztec-iMX7-Terminal-Project into the Build Environment

Clone the repsitory into the layers
```
$ cd oe-core/layers
$ git clone https://github.com/Miktronic/Anztec-iMX7-Terminal-Project.git
```
If the repository is private use the below method
```
$ git clone https://your_username:your_personal_access_token@github.com/Miktronic/Anztec-iMX7-Terminal-Project.git
```
Add the layer into bblayers.conf using the command
```
$ cd ../
$ . exports
$ bitbake-layers add-layers ../path/to/custom/layer
```
After adding the layer we can confirm the addition of layer in build environment by
```
$ bitbake-layers show-layers
```
Another way to confirm the layer added by using
```
$ vim build/conf/bblayers.conf
```
### Add the u-boot changes into the image 

First find the u-boot version using u-boot while board boot

Power up the board and stop in the u-boot then enter any key to stop then enter the below command 
```
$ version 
```
Now we can see the version of the u-boot 

Another way of finding u-boot version go to build directory 
```
$ cd oe-core
$ . export
$ bitbake -e virtual/bootloader | grep '^PV='
```
this will result like `PV="2022.07"`

Now go to the layers and find the file
```
$ cd oe-core/layers
$ find ./ -name "*2022.07*"
```
This will result the location of the file
  
Now go to the file directory and open the `.bb` file and add the patch files in the `"TDX_PATCHES = " veriable`
  
Also copy the u-boot changes patchs and paste in the .bb file location by below steps
```
$ cp u-boot-chages.patch ../../../layers/meta-toradex-bsp-common/recipes-bsp/u-boot/u-boot-toradex
```
Change the patch file name accordingly 

Now build the u-boot source by using the below command
```
$ cd oe-core
$ . export
$ bitbake u-boot-toradex
```
We can build the image and check the changes 

### Build an Image

For building the image go to the build environment and execute the below command
```
$ bitbake anztec-imx7-terminal-image
```
### Flash the image

After the Build completes we can get the `.wic` file for the flash
```
$ cd build/depoly/images/colibri-imx7-emmc
$ ls -l
```
Now able to see the files generated by the build in these we are using the `Colibri-iMX7-eMMC_Anztec-iMX7-Terminal-image_11-upstream-Tezi.tar` file.

Follow this article to flash the image: [USB flash using Toradex Easy Installer](https://drive.google.com/file/d/1gI7tw1BgLOC4lGzFPabaunS-FAEqD6_q/view?usp=drive_link).
