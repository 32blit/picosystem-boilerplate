# PicoSystem 32blit Boilerplate <!-- omit in toc -->

![Build](https://github.com/32blit/32blit-boilerplate/workflows/Build/badge.svg)

This is a basic template for starting 32blit projects for the Pimoroni PicoSystem.

It shows a minimal code layout and asset pipeline, giving you a starting point
for a new project.

It's based on the original `template` project from the 
[32blit beta](https://github.com/pimoroni/32blit-beta), with added asset
handling, and some tidying up to fit in with how I do things.

- [Why use 32blit SDK on PicoSystem?](#why-use-32blit-sdk-on-picosystem)
- [How to use this template](#how-to-use-this-template)
  - [Documentation](#documentation)
  - [Examples](#examples)
- [Requirements](#requirements)
  - [Compilers & Libraries](#compilers--libraries)
  - [32blit Tools](#32blit-tools)
  - [32blit & Pico SDKs](#32blit--pico-sdks)
- [Building](#building)
- [Copying your game to your PicoSystem](#copying-your-game-to-your-picosystem)
- [Extra configuration](#extra-configuration)
- [API Limitations & Board Details](#api-limitations--board-details)
  - [Unsupported Features](#unsupported-features)
  - [Limitations](#limitations)

## Why use 32blit SDK on PicoSystem?

The number 1 reason is portability! 32blit SDK will build for:

* Windows
* macOS
* Linux
* Emscripten (Web assembly)
* PicoSystem
* 32blit

And is portable to any platform supporting SDL2.

This means you can ship your game to more people on more platforms, share it online to play, and reach a little further than the confines of PicoSystem!

Additionally the 32blit SDK has some conveniences:

* Tiled editor .tmx support for levels
* An asset pipeline for converting fonts & spritesheets for use on device
* A boilerplate project with GitHub Actions

## How to use this template

[Use this template](https://github.com/32blit/picosystem-boilerplate/generate) to
generate your own project.

1. Edit the CMakeList.txt file to set the name of your project
2. Edit the metadata.yml file to set the information for your project
3. Edit the LICENSE file to set your name on the license
4. Delete the contents of this README.md and tell us about your game!
5. Write lots of super cool code!

### Documentation

Consult the [32blit wiki](https://github.com/32blit/32blit-sdk/wiki) for guides on various parts of the SDK:

* [Drawing Sprites](https://github.com/32blit/32blit-sdk/wiki/Sprites)
* [Drawing Text](https://github.com/32blit/32blit-sdk/wiki/Text)
* [Playing Sounds](https://github.com/32blit/32blit-sdk/wiki/Audio) - Note, PicoSystem is a mono buzzer only!
* [Timers & Tweens](https://github.com/32blit/32blit-sdk/wiki/Timers-&-Tweens)
* [Working With Files](https://github.com/32blit/32blit-sdk/wiki/File) - 4MB of PicoSystem's flash is reserved as a filesystem
* [Adding Metadata](https://github.com/32blit/32blit-sdk/wiki/Metadata)

### Examples

* [Snake](https://github.com/32blit/snake/)
* [Rocks & Diamonds](https://github.com/32blit/rocks-and-diamonds)
* [Dots](https://github.com/gadgetoid/32blit-dots)
* [SDK Examples](https://github.com/32blit/32blit-sdk/tree/master/examples)

## Requirements

We recommend using Linux to work with PicoSystem/Pico SDK. It's the path of least resistance!

This guide was tested with Ubuntu 21.04, and most of these instructions will work in its WSL
(Windows Subsystem for Linux) equivalent.

### Compilers & Libraries

You'll need a compiler and a few other dependencies to get started building C++ for PicoSystem:

```
sudo apt install git gcc g++ gcc-arm-none-eabi cmake make \
python3 python3-pip python3-setuptools \
libsdl2-dev libsdl2-image-dev libsdl2-net-dev unzip
```

### 32blit Tools

And the 32blit tools:

```
pip3 install 32blit
```

If pip gives you warnings about 32blit being installed in a directory not on PATH, make sure you add it, eg:

```
export PATH=$PATH:~/.local/bin
```

You might also want to add this to the bottom of your `~/.bashrc`.

### 32blit & Pico SDKs

You'll also need the various SDKs for PicoSystem and 32blit.

It's recommended you keep all of the SDKs in a directory alongside your project,
this makes it easier for CMake to find them:

* 32blit SDK - `git clone https://github.com/32blit/32blit-sdk`
* Pico SDK - `git clone https://github.com/raspberrypi/pico-sdk`
* Pico Extras - `git clone https://github.com/raspberrypi/pico-extras`

## Building

If you've got local copies of the Pico SDK, Pico Extras and 32blit SDK alongside your project,
then you can configure and build your .uf2 like so:

```
mkdir build.pico
cd build.pico
cmake .. -DCMAKE_TOOLCHAIN_FILE=../../32blit-sdk/pico.toolchain -DPICO_BOARD=pimoroni_picosystem
```

If you'd like the Pico SDK to handle grabbing Pico SDK and Pico Extras for you, you can use:

```
mkdir build.pico
cd build.pico
cmake .. -DCMAKE_TOOLCHAIN_FILE=../../32blit-sdk/pico.toolchain -DPICO_BOARD=pimoroni_picosystem -DPICO_SDK_FETCH_FROM_GIT=true -DPICO_EXTRAS_FETCH_FROM_GIT=true ..
```

:warning: Note: This approach is not recommended, since you might be reconfiguring a few times during
your project and re-downloading things unecessarily!

## Copying your game to your PicoSystem

Connect your PicoSystem to your computer using a USB Type-C cable.

From a power-off state, hold down X (the top face button) and press Power (the button at the top left, next to the USB Type-C port).

Your PicoSystem should mount as "RPI-RP2". On Linux this might be `/media/<username>/RPI-RP2`:

```
cp your-project-name.uf2 /media/`whoami`/RPI-RP2
```

The file should copy over, and your PicoSystem should automatically reboot into your game.

## Extra configuration

If you're not using `hires` mode and need some more RAM, it can be disabled:
```cmake
...

blit_executable(amazing-lores-game ...)

...

target_compile_definitions(amazing-lores-game PRIVATE ALLOW_HIRES=0)
```

## API Limitations & Board Details

### Unsupported Features

These features of the 32blit API are currently unsupported on any pico-based device:

- Joystick
- `HOME` and `MENU` buttons
- Accelerometer
- Vibration
- Paletted screen mode
- JPEG decoding
- `OpenMode::cached`

### Limitations

Additionally some supported features have limitations:

- The `screen` surface is RGB565 instead of RGB888
- `hires` screen mode is not double-buffered, usually resulting in a lower framerate
- `get_metadata` is missing the `author` and `category` fields
- `blit::random` is not a hardware generator
- Multiplayer has no host support
- Using the MP3 decoder is probably not a good idea
