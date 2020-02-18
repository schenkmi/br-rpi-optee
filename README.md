# buildroot-rpi-optee
Buildroot based Raspberry Pi 3 / RPI3 example for OP-TEE

## Clone repository with submodules
You must clone this project with
```
git clone --recursive https://github.com/schenkmi/buildroot-rpi-optee.git
```

## Build instructions plain Linux

You can build the image with
```
cd buildroot-rpi-optee
mkdir -p out && cd out
make -C ../buildroot O="$(pwd)" BR2_EXTERNAL=".." rpi3_defconfig
make
```

## Build instructions using Docker

The first step is to build the Docker image and create a container. You can specify a directory in your hosts system which will be mounted inside the container. This allows you to copy the built system easily. The current directory must contain the provided Dockerfile which will be compiled into an image. The directory called `build` will contain the sources and the images which will be built later.
```
mkdir -p build
docker build -t br-optee-rpi .
docker create -it --name br-optee-rpi --mount type=bind,source="$(pwd)",destination=/build br-optee-rpi
```

You can start the container if it was created successfully. This command gives you an interactive shell inside the container.
```
docker start -ia br-optee-rpi
```

The next step is to create a build directory called `out` which will contain the downloaded package source files and the output images. We will use this directory for an out-of-tree Buildroot build, by adding the `O=` parameter to the make command. We have to specify the directories where the external trees are stored, which can be done by adding the `BR2_EXTERNAL=` parameter to make (We can specify multiple directories by using `:` as a separator). The default config file is called `stm32mp1_defconfig` which is inside the `br-external/configs` directory.
```
mkdir -p out && cd out
make -C ../buildroot O="$(pwd)" BR2_EXTERNAL=".." rpi3_defconfig
```

After the configuration has been finalized you can issue the make command to start building the sources. This can take a long time, so be patient.
```
make
```

## Flashing
Please change /dev/sdX with the correct mounting point of your sd card.
```
sudo dd if=images/sdcard.img of=/dev/sdX bs=1M conv=fdatasync status=progress
```

## Console log
```
sudo picocom -b 115200 /dev/ttyUSB1
```
