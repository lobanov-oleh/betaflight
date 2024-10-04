## SITL in AirSim

SITL (software in the loop) simulator allows you to run betaflight without any hardware.

Environment: Ubuntu 22.04, UnrealEngine 4.27, AirSim 1.8.1

### Install AirSim

1. Setup Conda:
```sh
cd /tmp

curl -O https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh

chmod +x Anaconda3-2020.11-Linux-x86_64.sh
bash Anaconda3-2020.11-Linux-x86_64.sh

source ~/.bashrc

conda -V

conda update conda  
conda update anaconda
```
2. Create and activate conda's env:
```sh
conda create -n airsim
conda env list
conda activate airsim
```
3. Install UnrealEngine:
```sh
git clone -b 4.27 git@github.com:EpicGames/UnrealEngine.git
cd UnrealEngine

git clean -dfX

./Setup.sh
./GenerateProjectFiles.sh
make
```
4. Install AirSim:
```sh
git clone git@github.com:lobanov-oleh/AirSim.git
cd AirSim
./setup.sh
./build.sh

sudo apt install python3-pip

pip install numpy
pip install msgpack-rpc-python
pip install airsim
```
5. Download and unpack AirSim environment: https://github.com/microsoft/AirSim/releases/tag/v1.8.1 (I use Blocks)

### Build betaflight firmware for AirSim

```sh
make TARGET=SITLAIRSIM
```

### Settings

To avoid simulation speed slow down, suggest to set some settings belows:

In `configuration` page:

1. `ESC/Motor`: `PWM`, disable `Motor PWM speed Sparted from PID speed`
2. `PID loop frequency` as high as it can.

### Start and run

1. start betaflight:
```sh
./obj/main/betaflight_SITLAIRSIM.elf
```
2. start airsim:
```sh
conda activate airsim

cd LinuxBlocks1.8.1/LinuxNoEditor
chmod +x Blocks.sh
./Blocks.sh -ResX=640 -ResY=480 -windowed
```
4. connect your transmitter and fly/test, I used a app to send `MSP_SET_RAW_RC`, code available [here](https://github.com/cs8425/msp-controller).

### Notes (draft)
betaflight	->	gazebo	`udp://127.0.0.1:9002`
gazebo	->	betaflight	`udp://127.0.0.1:9003`

UARTx will bind on `tcp://127.0.0.1:576x` when port been open.

`eeprom.bin`, size 8192 Byte, is for config saving.
size can be changed in `src/main/target/SITL/pg.ld` >> `__FLASH_CONFIG_Size`
