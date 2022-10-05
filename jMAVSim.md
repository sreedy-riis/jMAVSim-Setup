# Table of Contents
1. [jMAVSim MacOS](#jmavsim-macos)
    1. [Prerequisites](#prerequisites)
    2. [Installations](#install-libraries)
    3. [Build and Run](#build-and-run-simulator)
    4. [Gazebo](#setup-gazebo)
2. [jMAVSim Ubuntu](#jmavsim-ubuntu)
3. [jMAVSim Windows](#jmavsim-windows)
4. [Commands](#commands)


# jMAVSim MacOS
## Prerequisites
Follow steps found here https://docs.px4.io/main/en/dev_setup/dev_env_mac.html.
I created a virtual environment for python packages to keep installs organized. Some commands in the setup scripts use `--user` which won't work with `venv`, so I installed those globally. If using a virtual environment, `--user` can be removed from singular commands. While the documentation says to use an `x86` terminal on M1 Macs, I don’t think this is true. Trying to install tools on the `x86` terminal failed a lot of the time but worked fine with an `arm` terminal. The commands below are from the link above.

### Install homebrew: https://brew.sh/ 

### Setup zsh file
```console
nano ~/.zshenv
```
Add the line
```
ulimit -S -n 2048
```

### Enforce Python Version
```console
alias pip3=/usr/bin/pip3
```
## Install Libraries
### Brew Install Libraries
```console
brew tap PX4/px4
brew install px4-dev
```

### Clone PX4 Source
Virutal environment can be created inside `PX4-Autopilot`. Current version of PX4 wouldn't build due to an implicit conversion failure in the code base. Reset project to `7bbdc220f5dcb568f87020cf3837e21043f0a2ab` to avoid problematic file.
```console
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
git resest --hard 7bbdc220f5dcb568f87020cf3837e21043f0a2ab
cd PX4-Autopilot
```

### Install Required Python Packages
```console
python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema
```

If the above command fails with a permissions error, your Python install is in a system path - use this command instead:
```console
sudo -H python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema
```

### Gazebo Simulation
The following snippet is a workaround for a current bug. More information at https://github.com/PX4/PX4-Autopilot/issues/17644
```console
brew unlink tbb
brew install tbb@2020
brew link tbb@2020
```

### Brew Install Gazebo
```console
brew install --cask temurin
brew install --cask xquartz
brew install px4-sim-gazebo
```

### Brew Install jMAVSim
A recent version of Java (e.g. Java 15) needs to be installed to use SITL simulation with jMAVSim.
```console
brew install px4-sim-jmavsim
```

### Setup PX4-Autopilot
```console
cd PX4-Autopilot/Tools/setup
sh macos.sh
```

### Build and Run Simulator
```console
make px4_sitl jmavsim 
```
If building fails, pip install anything that failed. I had to specifcally use `pip` to install empy, not `pip3`

### Setup Gazebo
Above steps will also install everything needed for Gazebo as well as jMAVSim. One extra step I had to do was reinstall a homebrew package
```console
brew reinstalll icu4c
```

Build VTOL for Gazebo
```console
make px4_sitl gazebo_standard_vtol
```



# jMAVSim Ubuntu
I have not tested the following steps for Ubuntu. For more information go to https://docs.px4.io/main/en/dev_setup/building_px4.html.

Clone the PX4 repository
```console
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

Install requirements
```console
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
```
Build the simulator
```console
make px4_sitl jmavsim
```

# jMAVSim Windows
## Not supported

# Commands
### Set Location
Before building, run these commands to set the starting location of the drone. The following example uses RIIS office coordinates. If lat and long are invalid then the simulator will not build.
```console
cd PX4-Autopilot

export PX4_HOME_LAT=42.557965

export PX4_HOME_LON=-83.154303
```
### Set Altitude
Set default altitude
```console
export PX4_HOME_ALT=28.5
```

### Run Sim
```console
make px4_sitl jmavsim
```
### Start Mavlink for Remote GCS
After building the sim, a remote GCS can be set. Place the IP of the device running the ground control station for `<REMOTE DEVICE IP>`. This command is not needed if running ground control on same device as simulator. Do not include port in IP address. Proper IP example `10.5.2.168`
```console
mavlink start -x -u 14550 -f -p -t <REMOTE DEVICE IP>
```

### Build Gazebo for VTOL
```console
make px4_sitl gazebo_standard_vtol
```





