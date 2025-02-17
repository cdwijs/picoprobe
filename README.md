# Picoprobe
Picoprobe allows a [Pico / RP2040](https://www.raspberrypi.com/products/raspberry-pi-pico/) to be used as USB -> SWD and UART bridge. This means it can be used as a debugger and serial console for another Pico.

# Documentation
Picoprobe documentation can be found in the [Pico Getting Started Guide](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf). See "Appendix A: Using Picoprobe".

# Hacking

For the purpose of making changes or studying of the code, you may want to compile the code yourself. To be able to compile this project, a compiler and the Raspberry Pi Pico SDK need be be installed. 

## Prepare an Arch Linux host system
On Arch Linux, the compiler can be installed by this command:
```
sudo pacman -S arm-none-eabi-binutils arm-none-eabi-gcc arm-none-eabi-newlib
```
The Raspberry Pi Pico SDK can be installed by these commands:
```
wget https://aur.archlinux.org/cgit/aur.git/snapshot/pico-sdk.tar.gz
tar -xf pico-sdk.tar.gz
cd pico-sdk
makepkg
sudo pacman -U pico-sdk-1.5.1-1-any.pkg.tar
export PICO_SDK_PATH=/usr/share/pico-sdk
```
The export command above is not needed if you logout and then login again.

## Compile Picoprobe
To compile this project firstly initialize and update the submodules: 
```
 git submodule update --init
```
then create and switch to the build directory: 
```
 mkdir build
 cd build
```
then run cmake and build the code:
```
 cmake ..
 make
```
Done! You should now have a `picoprobe.uf2` that you can upload to your Pico in the normal way. 

If you want to create the version that runs on the [Raspberry Pi Debug Probe](https://www.raspberrypi.com/products/debug-probe/), then you need to invoke `cmake` in the sequence above with the `DEBUGPROBE=ON` option: 
```
cmake -DDEBUGPROBE=ON ..
```

This will build with the configuration for the Debug Probe and call the output program `debugprobe.uf2`, as opposed to `picoprobe.uf2` for the vanilla version. 

Note that if you first ran through the whole sequence to compile for the Pico, then you don't need to start back at the top. You can just go back to the `cmake` step and start from there.


# TODO
- TinyUSB's vendor interface is FIFO-based and not packet-based. Using raw tx/rx callbacks is preferable as this stops DAP command batches from being concatenated, which confused openOCD.
- Instead of polling, move the DAP thread to an asynchronously started/stopped one-shot operation to reduce CPU wakeups
- AutoBaud selection, as PIO is a capable frequency counter
- Possibly include RTT support
