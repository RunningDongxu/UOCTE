UOCTE - _The_ Unified OCT Explorer
==================================

Setup
-----

### Linux

```
sudo aptitude install g++ cmake libqt5opengl5-dev libglew-dev libexpat-dev libarchive-dev libopenjpeg-dev
```


### Windows

Install MSYS2: https://www.msys2.org/

```
pacman -S git openssh
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-make
pacman -S mingw-w64-x86_64-qt5
pacman -S mingw-w64-x86_64-glew
pacman -S mingw-w64-x86_64-cmake
pacman -S mingw-w64-x86_64-expat
```

```
pacman -S mingw-w64-x86_64-libarchive
```

```
pacman -S mingw-w64-x86_64-openjpeg
pacman -S mingw-w64-x86_64-openjpeg2
pacman -S mingw-w64-x86_64-jasper
```

Run `C:/msys64/mingw64.exe`


### UCOTE

```
mkdir build
cd build
cmake ..
# cmake .. -G "MinGW Makefiles" -DCMAKE_SH="CMAKE_SH-NOTFOUND"
```

```
make
# mingw32-make
```

```
mingw32-make install
sudo make install
```


```
copy all .dll from C:\msys64\mingw64\bin and C:\msys64\mingw64\share\qt5\plugins\platforms to the installed bin folder 
```

Usage
-----

```
./bin/uocte
```


