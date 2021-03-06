Stellarium Joystick Plug-in
===========================

Plug-in providing joystick and gamepad support for Stellarium,
the free/open-source astronomy application (http://stellarium.org).

It is intended as a supplement to the existing forms of control (keyboard and
mouse), not as a replacement for them in all possible operations.

In very early stages of development. Current capabilities are very limited.
As it requires an additional dependency (the Simple DirectMedia Layer (SDL)
library), I've chosen to develop it as a stand-alone ("dynamic") plug-in.


Features
--------

At this stage of development:
 - the plug-in supports only one controlling device. If there are more than one
 connected to the system, it will pick the first indexed by SDL.
 - all controls are hard-coded. Customization is planned for the future.

Joystick controls:
 - the first two axes are assumed to be the X and Y axes and pan the view
 - the third axis (throttle? yaw?), if present, controls zoom
 - any hat switches, if present, pan the view
 - button 1 (trigger?) toggles the mount mode (between alt-azimuth and
 equatorial)
 - holding down button 2 allows finer movement when panning and zooming,
 similar to holding down SHIFT when using the keyboard.

Gamepad controlls:
 - the left analog stick (if present) pans the view
 - vertical axis of the right analog stick (if present) controls zoom
 - direction buttons pan the view
 - the button quartet on the right side:
    + the bottom button (X-cross or A) toggles the mount mode
    + holding down the right button (Circle or B) allows finer movement
    + the left button (Square or X) returns to the default zoom
    + the top bottom (Triangle or Y) returns to the current time, which is
    necessary, because...
 - the left shoulder button slows down time, the right one speeds it up


Installation
------------

On all systems, the plug-in requires the appropriate version of Stellarium to
be already installed.


On Windows, an installer will be provided for each release. Just run the
executable and follow the instructions. It should detect Stellarium's
installation directory automatically. If Stellarium has already run once (and
has created its configuration file), the installer offers the option to set up
the plug-in to be loaded at Stellarium startup, thus avoiding the need to do
this manually after the installation (saves one Stellarium restart).

The installer is generated with Inno Setup and accepts the standard command line
options (when applicable), e.g. for a "silent" install:
http://www.jrsoftware.org/ishelp/index.php?topic=setupcmdline
The "load at startup" option can be controlled from the command line:
/TASKS="loadatstartup" to enable, /TASKS="!loadatstartup" to disable.


On Linux, you'll have to build the plug-in from source - either from a released
source package, or by forking the project's repository on GitHub. See the
"Development" section below for details.

CMake generates an "install" target. If everything has been set up correctly,
running "make install" after CMake will build the project and copy the compiled
library file to the appropriate plug-in directory, so it can be found by
Stellarium and recognized as a plug-in. After that, it should be listed in
the "Plugins" tab of Stellarium's configuration window.


Configuration and customization
-------------------------------

At this stage of development, almost no configuration is possible.

The plug-in uses SDL's community-sourced database of game controllers. A copy of
`gamecontrollerdb.txt` is placed in the plug-in's data directory (see below)
when the plug-in is first loaded. If your gamepad is not recognized by the
plug-in, it's possible to update the database or edit it manually to add your
device. An up-to-date copy of the database can be downloaded from its
repository at GitHub: https://github.com/gabomdq/SDL_GameControllerDB
The same page contains instructions on how to get a gamepad's mappings in the
format used in the database.

The plug-in keeps its files in the modules/JoystickSupport sub-directory of
Stellarium's user directory. This means:
 + C:\Users\UserName\AppData\Roaming\Stellarium\modules\JoystickSupport (on
 Windows Vista, Windows 7 and presumably Windows 8)
 + C:\Documents and Settings\UserName\Application Data\Stellarium\modules\JoystickSupport
 (on Windows XP)
 + $HOME/Library/Preferences/Stellarium/modules/JoystickSupport (on
 Mac OS X)
 + ~/.stellarium/modules/JoystickSupport (on Linux)


Platforms
---------

The plug-in will aim to work on the same major platforms as Stellarium (Linux,
Mac OS X, Windows, and even BSD), but in practice:
 - Windows: it's likely that the next release of Stellarium will not
 support dynamic plug-ins due to the way it's built (MSVC does not export all
 symbols like gcc does by default), unless a lot of work is done. At least
 one previous version of Stellarium (0.12.4) is supported though.
 - Mac OS X: I don't have anything running that, so any development will have to
 be done by an outsider.
 - Linux: the plug-in is currently developed on Ubuntu 13.10, but there's no
 binary package available. In the future, there can be a PPA on Launchpad,
 and/or I can submit it to Debian, from where it will percolate down to Ubuntu.


Development
-----------

This is a dynamic Stellarium plug-in (as opposed to the static, built-in default
plug-ins that are distributed with Stellarium). Since Stellarium lacks a proper
development package, in addition to the Stellarium binaries, to build
the plug-in you'll need Stellarium's source code for the same version.
On Windows, the plug-in will be able to link against libStelMain.dll only if
Stellarium had been compiled with MinGW/gcc. (Luckily, this covers 0.12.4 and
the most recent versions before that.) In all cases, it's very likely that
the resulting plug-in binary will be compatible only with the version of
Stellarium it was built against.

As this project is a work-in-progress, the code is mainly based on the current
Stellarium development version - around revision 6600 in the 'trunk' branch in
Stellarium's code repository. See https://code.launchpad.net/stellarium

Nevertheless, there is some support for previous versions - so far, it has been
successfully tested with 0.12.4 on Windows XP.

The plug-in  has the same build dependencies as Stellarium. Unfortunately,
it's necessary to use (almost) the same versions of the Qt libraries and
the same compiler as the ones used for the target version of Stellarium.
Fortunately, there seems to be some interoperability - patch versions are
supposed to be binary compatible (i.e. all 4.8.* versions are compatible,
all gcc 4.6.* versions use the same ABI, etc.).

Required:
 - for the development version, Qt 5.1 at this point (since you probably used
 the same libs for your Stellarium build, it's not a problem)
 - for Stellarium 0.12.4, on Windows, Qt 4.8.5 and MinGW with gcc 4.6
 (from the original MinGW project?) Installing the latter is somewhat difficult.
 - SDL2, version 2.0.6 or later; available from http://www.libsdl.org/ - you
 can use the pre-compiled development package on Windows

CMake is necessary to process the build script.
Source code documentation is based on Doxygen.

Before building, two paths need to be passed to CMake:
- STELLARIUM_SOURCE_DIR, the path to the src/ directory in Stellarium's
source package/version control working copy. 
- STELLARIUM_BINARY_DIR, the path to a directory that contains Stellarium's
executable, such as the build directory of a Stellarium build-from-source, or,
Stellarium's installation directory (if built on Windows with MinGW).

You can also pass a few optional parameters:
- STELLARIUM_VERSION is required when building the plug-in for a specific
version of Stellarium and should contain the version number. By default,
the development version is assumed. Example use: -DSTELLARIUM_VERSION=0.12.4 
- SDL2_DIR, the path to the main SDL2 directory (useful if the environmental
variable SDLDIR is not set)
- if you pass an empty value of CMAKE_INSTALL_PREFIX, the script will change it
to a suitable value, so running "make install" will install the plug-in in
Stellarium's user data directory. Alternatively, on Windows, setting it to
Stellarium's installation directory (e.g. C:\Program Files\Stellarium)
will install the plug-in there, allowing all users to use the same installation. 

Authors and copyright
---------------------

At this point, all of the plug-in's source code, build scripts and this file
have been written by me, Bogdan Marinov.

This project is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; either version 2 of the License, or (at your option) any later
version. See the LICENSE file for the full text of the license.

On Windows, the SDL2 runtime library will be installed together with
the plug-in, as well as a copy of the SDL community game controller database.
The SDL2 library is licensed under the zlib license. See the README-SDL and
LICENSE-SDL files in Stellarium's installation directory for further details.
The controller database is licensed under the same terms as the SDL2 library.


Links
-----

The source code of the project can be found at its git repository at GitHub:
https://github.com/daggerstab/stellarium-joystick

Releases will be announced on the Releases subpage there:
https://github.com/daggerstab/stellarium-joystick/releases

My programming blog (mostly about Stellarium programming):
http://badlyhonedbytes.wordpress.com

