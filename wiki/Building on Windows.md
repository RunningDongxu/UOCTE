For Windows it is highly recommended to use [MSYS2](http://msys2.github.io/). Please follow the instructions for installation on that site, then open a mingw-w64 Win32 or Win64 shell and install programs and libraries necessary for uocte (If you want a 32 bit version, replace x86_64 by i686 in the following commands)

```
pacman -S git openssh
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-make
pacman -S mingw-w64-x86_64-qt5
pacman -S mingw-w64-x86_64-glew
pacman -S mingw-w64-x86_64-cmake
pacman -S mingw-w64-x86_64-expat
```

To enable the optional Eyetec reader you also need:

```
pacman -S mingw-w64-x86_64-libarchive
```

To enable the optional Topcon reader you need one of the following:

```
pacman -S mingw-w64-x86_64-openjpeg
pacman -S mingw-w64-x86_64-openjpeg2
pacman -S mingw-w64-x86_64-jasper
```

Then proceed with [general build instructions](Building with CMake)