# Custom Android Pixel Rom Tutorial

## Building a Custom Pixel ROM from Scratch

This is a very beginner friendly tutorial/text to follow on how to build a Custom ROM (from scratch) for your device. For this tutorial porpose, I will be explaining the Pixel Experience Rom for POCO F1 (Best Mobile Launched on Budget)

## Requirements

- Your device with USB cable (POCO F1 for example)
- x64-bit decently powerful Linux PC or VMWare (decent specification) for compiling purpose 
- Recommended specification: 4 or more cores with 16GB RAM and 200+ GB hard disk (SSD Preferred for faster transfers and lower build times). I will be considering Ubuntu flavored Zorin OS to build in this tutorial.
- Unlimited and fast internet connection (Since final sync version can expand upto 200 GB and sync time for first timer can exceed 3 days)
- Basic Git and GitHub knowledge. You will need some advanced knowledge too which you can learn along with time.

## Let's begin
Let's begin with the custom ROM journey with the sweet Pixel ROM. After that you can build any ROM for any device easilly

## 1. Choose an operating system
Choosing an operating system is important. Since I am using Ubuntu flavored ZORIN OS (Because it is simple, looks like windows, less waiting time and more customization features) and I know the commands used in it to run on terminal.

## 2. Choose where to install it
Since I think you have choosen an operating system by now, but now where you would like to install it. I have installed in the VMware® Workstation 16 Pro (Gives me feasibility to expand storage and increase ram if needed) or you can go hardcore mode with dual boot operating system(Zorin OS + Windows 10/11).  

Also, for hardcore dual bootable operating system lovers. If the Master Boot Record get corrupted int the process, use the command below

```bash
BOOTREC\FIXMBR 
```
To fix your boot menu  of your Windows, after you decide to delete the Linux Partition (in frustration). You can recover it with Windows Installer Image with the <a href="https://www.ventoy.net/en/index.html"> Ventoy (Just the best Multi ISO Bootable USB Solution)</a> installed pendrive.

## 3. Download and install platform-tools & Unzip it
Download the platform-tools for Linux from here by using the terminal.
```bash
cd ~/
wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip
```
Use this command to unzip it:
```bash
unzip platform-tools-latest-linux.zip -d ~
```

## 4. Add the Platform tools to path
Now you have to add adb and fastboot to your PATH. In the same terminal window enter this:
```bash
cd ~/
nano ~/.profile
```
Just enter the following text at the bottom of the file that opens up, save it and close.
```bash
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```
Key combination to save file and exit in nano editor is: (Ctrl + O) + (Enter) + (Ctrl + X). 
Then, run this to update your environment.
```bash
source ~/.profile
```
## 5. Install GIT in System
Git is required for the syncing of the libraries to your system. 
```bash
sudo apt install git
```

## 6. Install the build packages
Several packages are needed to build a Custom ROM and all those can be installed easily by using Akhil Narang's scripts. Run this:
```bash
cd ~/
git clone https://github.com/akhilnarang/scripts
cd scripts
./setup/android_build_env.sh
```
## 7. Create the directories
You’ll need to set up some directories in your build environment
To create them:
```bash
mkdir -p ~/bin
mkdir -p ~/android/<your_rom_name_here>      
#<your_rom_name_here>  could contain anything, for example it can be any name such as - pe, me, be, thecaptaincook etc. 
```
The ~/bin directory will contain the git-repo tool (commonly named “repo”) and the ~/android directory will contain the source code of the Custom ROM

## 8. Install the repo command
Enter the following to download the repo binary and make it executable (runnable):
```bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

## 9. Configure git
Given that repo requires you to identify yourself to sync Android, run the following commands to configure your git identity:
```bash
git config --global user.email "yourwebsite@example.com"
git config --global user.name "Your_Name_in_here"
```

## 10. Initialize the source repository and download
Get into the android directory that we created and initialize the branch of repo that you wish to build. 

```bash
cd ~/android/<your_rom_name_here> 
```
You can find the link to initialize the repo in the manifest of the particular ROM. As I am using Pixel Experince ROM for example, you can look at their manifest from <a href="https://github.com/PixelExperience/manifest"> [Click Here] </a> or link  (https://github.com/PixelExperience/manifest). Android Pixel ROM's usually called it as "manifest" and other rom can might call it a github repository (since it is externally built by indivisuals with a lot of customisation).

```bash
# Initialize local repository
repo init -u --depth=1 https://github.com/PixelExperience/manifest -b thirteen
```
Note --depth=1 is provided for the most recent files to pick up and scan for it. 

## 11. Download the source repository
Download the source code by this command:
```bash
# Sync
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags -j 4
```
Note that repo sync will take time depending on your internet connection and -j 4 (number of batch tasks to process at a time). Depending on the connection speed it will take maybe 20 min to 3 days. So, fast and unlimited internet is mandatory.

## 12. PITFALL HERE #1 Python Error (Optional)
You may or may not be encountering an error here 
```bash
#ERROR
/usr/bin/env: ‘python’: No such file or directory

#Simple Solution - You have to symlink you python here to the directory
sudo apt install python-is-python3
```

## 13. Get the Dependencies for Beryllium
Next we can start building the ROM by using the following commands. These commands might be different for different ROMs. Check the ROM manifest of different ROM's for exact commands.

After the source downloads, ensure you’re in the root of the source code (cd ~/android/<your_rom_name_here>), then type:
```bash
#changes present working directory
cd ~/android/<your_rom_name_here>

#build the rom
source build/envsetup.sh

#This will download the necessary dependencies for the beryllium.
lunch aosp_beryllium-userdebug
```

## 14. Turn on Caching 
This will turn on the caching for the files on the system and space hogging is premanent here.
```bash
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
```

This will limit the cache to 50 GB Cached but we cah gave more cache for more speed of compilation. 
More cache means more compiling speed and will take less time overall. 
```bash
ccache -M 50G
```
  
## 15. Turn On Compression (optional) 
Compression can turned on to fit more code in the particular spot with slight performace hit.
```bash 
  ccache -o compression=true
```
  
## 16. PITFALL HERE #2 ccache Error (Optional)
You may or may not be encountering an error here 
```bash
#ERROR
ccache: error: Failed to create temporary file for /home/user/.cache/ccache/tmp: Read-only file system
```
```bash
#Simple Solution - Create a new mount point
sudo mkdir /mnt/ccache
  
#Bind cache directory to mount point and replace <your_account_username> with the appropriate value
sudo mount --bind /home/<your_account_username>/.cache/ccache /mnt/ccache

#Now Enable Cache, Execute Cache and Set Cache Directory
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
export CCACHE_DIR=/mnt/ccache
  
#set the cache to 50GB again
ccache -M 50G
  
#Then to make sure this doesnt break after you reboot, we need to add the new mount point to auto mount at login. 
#Edit fstab using sudo nano /etc/fstab and add
/home/<your_account_username>/.cache/ccache /mnt/ccache none defaults,bind,users,noauto 0 0
```

## 17. Start the build 
```bash  
croot
mka bacon -j$(nproc --all)
```

## 18. PITFALL HERE #3 Command not found (Optional)
You may or may not be encountering an error here 
```bash
#ERROR
croot command not found

#Simple Solution - croot is short of "cd (change directory Unix command) to root (of the project)".
#Just Restart you system and you are good to go.
sudo reboot
```
  
## Credits
- <a href="https://get.pixelexperience.org/beryllium">[Pixel Experience]</a>
-  <a href="https://github.com/akhilnarang">[Akhil Narang]</a>

