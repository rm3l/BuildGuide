#  Android 8.1 Build Guide for Custom ROMS on Ubuntu 16.04 (Lubuntu 16.04 tested)

This guide will walk you through the steps to set up the android 8.1 build environment on ubuntu 16.04.You will also learn how to use local_manifests to sync device specific code, and modify your device tree to be compatible with your ROMs source :)
###### some optional fixes for building on low end hardware will be present as well: E.x 4gb ram & intel i5-4400

### Installing prerequisites

Install Open Java Development Kit 8
```
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

other required packages for Ubuntu 14.04 and up
```
 sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev libgl1-mesa-dev libxml2-utils xsltproc unzip gedit
```

Install Repo and make it executable
```
mkdir ~/bin
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

Configure git with your real name and email address
```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Now It's time to choose a ROM and Grab it's Platform Manifest!

###### Note: I will be using Havoc-OS as an example for this guide so change everything "havoc" to your target rom

Once chosen, go to the ROMs github and locate the platform manifest/android manifest. You should find a command simillar to this:
```
repo init -u https://github.com/Havoc-OS/android_manifest.git -b oreo
```
Before running the repo init command, you'll want to make a directory where the source will be stored.
```
mkdir -p ~/havoc
cd ~/havoc
```
Now you can initialize repo.

### Device Specific Code

There are three things that you'll need:
1. device tree
2. kernel source
3. Vendor source

We can gather these by using local_manifests. Assuming you're still in the ~/havoc folder you'll want to:
```
cd .repo
mkdir local_manifests
cd local_manifests
gedit local_manifest.xml
```
Copy the contents of this  manifest into gedit: https://github.com/AnierinBliss/BuildGuide/blob/master/local_manifest.xml

Now use Lineage-OS & TheMuppets Github as a refrence to edit local_manifest.xml to represent your device codename, kernel name, and vendor name.This is usually pretty easy to do. Just search your device codename on Lineage-OS github and you'll find device and kernel. For vendor, search TheMuppets Github for your devices manufacturer. After you're done doing so, save!
### Syncing the Source!
###### Note: this will take a while and consume a lot of bandwith.
```
cd -e
cd ~/havoc
repo sync  -f --force-sync --no-clone-bundle
```
If repo-sync exits due to fetch errors then run the mentioned repo sync cmd again as -f will fix broken source.

### Modifying your device tree to be compatible with your ROMs source
Open up your file browser and navigate to the "device" folder at the root of your ROMS source. Continue by entering into your device's manufacturer_codename folder. They'll be 2 files that you'll need to edit here. I'll be using havoc as my ROM and angler as my device:

1. Rename lineage.dependencies (if present) to havoc.dependencies and lineage.mk to havoc.mk

2. Open havoc.mk and replace all instances of "lineage" with "havoc" as shown in the examples below:

###### Example for 1st change in lineage.mk
"PRODUCT_NAME := lineage_angler"

get's changed to 

"PRODUCT_NAME := havoc_angler"

###### Example for 2nd change in lineage.mk
"$(call inherit-product, vendor/lineage/config/common_full_phone.mk)" 

gets changed to

"$(call inherit-product, vendor/havoc/config/common_full_phone.mk)"

###### Note: Some ROMs may have vendor/havoc/common.mk)" or vendor/havoc/config/common.mk)" instead of the normal usage so always check to see what extra modifications you need to add to havoc.mk. Also, do not change things like aosp_angler.mk. In Havoc's case, only the original changes were needed.


### Optional fixes for low-end pc's

Before I go any further, I'd like to introduce some common fixes for build/core on low end hardware. Just download the droidoc.mk and deffinitions.mk from this git repo and replace the ones in your local havoc/build/core with them.

Another common error is the jack-server running out of memory, causing constant hangs and even non responsivness. An easy fix for this is setting jack-server to allocate a sufficent ammount of RAM.
```
export ANDROID_JACK_VM_ARGS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx3G"
```
###### Note: Do not increase the #3 if using a 4gb ram pc:

You may also want to set jacks max-services from 4 to 1 or 2

### Time To Build! 

```
cd ~/havoc
```

To use a seperate output directory:
```
export OUT_DIR_COMMON_BASE=/media/username/drivename
```

To Build:
```
. build/envsetup.sh
brunch angler
```
###### Note: Replace "angler" with your device's codename
###### Note #2: These commands can be slightly different for every ROM. Check with your ROMs platform_manifest on Github before executing them.

# Congrats! You're now compiling for Your device!

