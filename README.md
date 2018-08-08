# Android Build Guide for Ubuntu 16.04

This guide will walk you through the steps of getting an android 8.1 build enviorment up and running on ubuntu 16.04.
###### some optional additions for building on low end hardware will be present as well: E.G 4gb ram & intel i5-4400

### Installing prerequisites

Install openjdk-8-jdk
```
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

Install required packages for Ubuntu 14.04 and up
```
 sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev libgl1-mesa-dev libxml2-utils xsltproc unzip
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

### Now It's time to choose a ROM!

To do this we must go to the Github of our ROM Source and locate the platform manifest. You should find a command simillar to this:
```
repo init -u https://github.com/Havoc-OS/android_manifest.git -b oreo
```
Before running the repo init command, you'll want to make a directory for the Source that will be downloaded. I will be using Havoc-OS as an example:
```
mkdir -p ~/havoc
cd ~/havoc
```
Now you can go ahead and initialize repo.

### Device Specific Code

There are three key things that you'll need for this proccess:
1. Vendor
2. Kernel
3. Device Tree

We can gather these by using local_manifests. Assuming you're still in the ~/havoc folder you'll want to:
```
cd -e
nano local_manifest.xml
```

Copy this example into the terminal window:
```
<manifest><project path="device/huawei/angler" name="AnierinBliss/android_device_huawei_angler" remote="github" revision="lineage-15.1"/><project path="vendor/huawei" name="TheMuppets/proprietary_vendor_huawei" remote="github" revision="lineage-15.1"/><project path="kernel/huawei/angler" name="LineageOS/android_kernel_huawei_angler" remote="github" revision="lineage-15.1"/></manifest>
```
Then, using your keyboard, hit ctrl-x and then the letter "Y" so that it saves. Run this so that you can edit the manifest with ease:
```
gedit local_manifest.xml
```
Now use Lineage-OS & TheMuppets Github as refrence to edit the files to represent your device codname, kernel name, and vendor name. After you're done doing so, crate a local_manifests folder in Havoc-OS/.repo and move local_manifest.xml into it.
### Syncing the Source!
Note: this will take a while and consume a lot of bandwith
```
repo sync  -f --force-sync --no-clone-bundle
```
If repo-sync exits due to fetch errors at some point then run the mentioned repo sync cmd again as -f will fix broken source

### Modifying the device tree to build with your ROMS source
Open up your file browser and navigate to the device folder at the root of your ROMS source. Continue by entering into your device's manufacture/codename folder. They'll be 2 files that you'll need to edit here. Again, I'll be using havoc as an example:

1. Rename lineage.dependencies to havoc.dependencies and linage.mk to havoc.mk

2. Open havoc.mk and replace all instances of "lineage" with "havoc" as shown in the examples below

###### Example for 1st change in lineage.mk
"PRODUCT_NAME := lineage_angler" 

get's changed to 

"PRODUCT_NAME := havoc_angler"

###### Example for 2nd change in lineage.mk
"$(call inherit-product, vendor/lineage/config/common_full_phone.mk)" 

gets changed to

"$(call inherit-product, vendor/havoc/config/common_full_phone.mk)"




# wip, time for sleep..
Not done yet, I'm going to sleep lol

