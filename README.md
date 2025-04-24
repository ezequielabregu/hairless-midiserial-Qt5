# Hairless MIDI<->Serial Bridge

is the easiest way to connect serial devices (like Arduinos) to send and receive MIDI signals.

The project home page is <http://projectgus.github.com/hairless-midiserial/>

Please see that page for information on running and using Hairless Midiserial, and downloadable compiled versions for Windows, OS X and Linux.

## UPDATE: April 2025

---

**IMPORTANT:** This repository has been **updated** to work with Qt 5.

---

The original code was written for Qt 4, and the changes made were necessary to ensure compatibility with modern Qt 5.
The changes were necessary to adapt the Hairless MIDI<->Serial Bridge codebase, originally written for Qt 4, to work with Qt 5 on modern macOS systems. Qt 5 introduced significant changes, such as moving `QApplication` to the `QtWidgets` module and deprecating methods like `QString::fromAscii`, which required replacing them with `QString::fromLatin1`. Additionally, we updated the `.pro` file to include the `widgets` module and used the `CONFIG+=sdk_no_version_check` flag to bypass warnings about unsupported macOS SDK versions.

These updates ensured compatibility with Qt 5 while maintaining the functionality of the original application. Setting the correct PATH for Qt 5 and cleaning the build directory were also crucial steps to ensure a smooth compilation process.

---

## If you want to build the project from original source, please follow these instructions

This section provides a step-by-step guide to compile the Hairless MIDI<->Serial Bridge from source on macOS with an M1 chip or similar architecture.

### 1. Install Required Tools

1. Install [Homebrew](https://brew.sh/) if you haven't already:

   ```sh
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

### 2. Install the required dependencies

   ```sh
   brew install qt@5 cmake git
   ```

This will install the necessary Qt libraries and tools, as well as CMake and Git.

### 3. Clone the Repository

```bash
git clone --recursive <repository-url>
cd hairless-midiserial
```

### 4. Configure QT5

```bash
export PATH="/usr/local/opt/qt@5/bin:$PATH"
```

(Optional) Add this line to your shell configuration file (e.g., ~/.zshrc) to make it permanent:

```bash
echo 'export PATH="/usr/local/opt/qt@5/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

- Open src/main.cpp and replace:

```cpp
#include <QtGui/QApplication>
```

with:

```cpp
#include <QtWidgets/QApplication>
```

- Open hairless-midiserial.pro and ensure the QT variable includes widgets:

```bash
QT       += core gui widgets
```

- Open Bridge.cpp and replace:

```cpp
QString debug_msg = QString::fromAscii(msg_data.mid(4, msg_data[3]).data());
```

with:

```cpp
QString debug_msg = QString::fromLatin1(msg_data.mid(4, msg_data[3]).data());
```

- Open aboutdialog.cpp and replace:

```cpp
QString text = ui->label_info->text().arg(QString::fromAscii(VERSION)).arg(QString::fromAscii(__DATE__));
```

with:

```cpp
QString text = ui->label_info->text().arg(QString::fromLatin1(VERSION)).arg(QString::fromLatin1(__DATE__));        
```

### 5. Compile the Project

Run qmake to generate the Makefile:

```bash
qmake CONFIG+=sdk_no_version_check hairless-midiserial.pro
```

Then compile the project using make:

```bash
make
```

### 6. Run the Application

After compilation, the executable should be in the build directory. Run it.

## Building Hairless Bridge from source (Original README.md)

(Note again, prebuilt versions *are available for download* at the above URL.)

Hairless uses git submodules for library dependencies, so you should use `git clone --recursive URL` when cloning from Github. Alternatively, you can run `git submodule update --init` in order to fetch the submodules to an already-cloned directory.

Hairless Midiserial Bridge release 0.4 was built with Qt 4.7.3. It's also been built and run under Qt 4.7.4 & 4.8.6. Newer Qt version 5.0 will probably require code changes in order to compile and/or run.

The Qt package should contain all dependencies, the graphical IDE "Qt Creator" or the program "qmake" can be used to compile the project hairless-midiserial.pro.

On Windows I recommend building with the [MingGW compiler](http://www.mingw.org/), Visual Studio has not been tested. Neither the MinGW site nor Qt's new owners Digia still distribute older MinGW builds, and MinGW 4.7 is too new for precompiled Qt 4.7.x, so it can be a bit hard to find a prebuilt combination that work. Recently I downloaded `mingw-static-4.4.5-all.7z` from [this Google Code project](https://code.google.com/p/qp-gcc/downloads/list), and can confirm that works.

(For the Windows release builds I actually [cross-build under Linux using wine, as described here](http://projectgus.com/2011/09/developing-qt-apps-for-windows-using-linux-wine/).)

## Libraries

- [qextserialport](https://code.google.com/p/qextserialport/) is hosted on Github and is linked into the source tree as a git submodule.

- [The RtMidi library](https://github.com/thestk/rtmidi) is hosted on Github and is linked into the source tree as a git submodule.

Both libraries are small so they compiled as source files directly into Hairless Bridge, not linked as libraries.

## Release builds

The official releases are static linked, so they've actually been built against Qt versions that were compiled from source, configured with `-static` for static linking.
