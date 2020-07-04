# Prerequisites

For Windows use these [Notes](Building on Windows). For all other systems, please ensure that you have [CMake](http://www.cmake.org/) installed, as well as development versions of the following libraries:

- [Qt5](http://www.qt.io/)
- [glew](http://glew.sourceforge.net/)
- [expat](http://expat.sourceforge.net/)
- [libarchive](http://www.libarchive.org/) (optional, for Eyetec reader)
- [OpenJPEG](http://www.openjpeg.org/) (version 1.5 or 2.1) or [Jasper](http://www.ece.uvic.ca/~frodo/jasper/) (optional, for Topcon reader, OpenJPEG 1.5 recommended)

On Debian/Ubuntu/etc. the packages can be installed using

```
sudo aptitude install g++ cmake libqt5opengl5-dev libglew-dev libexpat-dev libarchive-dev libopenjpeg-dev
```

# Building

Assuming you put the downloaded uocte sources in a directory called `uocte` the following commands issued in a command line will configure the build process.

```
mkdir uocte-build
cd uocte-build
cmake ../uocte
```

Instead of `cmake` you can use `ccmake` or `cmake-gui` to change build parameters or set paths to libraries not found automatically. On Windows, you should add `-G "MinGW Makefiles"` after `cmake`.

To actually build uocte issue

```
make
```

inside the build directory. Under Windows you need to use `mingw32-make` instead of `make`.

# Installing

The final binary is in `uocte-build/bin`. To install the program system-wide issue

```
make install
```
