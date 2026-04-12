# eSim 2.5 Installation on Ubuntu 25.04

### FOSSEE eSim Summer Fellowship 2026 -- Screening Task 4

This repository documents the debugging and installation of **eSim 2.5**
on **Ubuntu 25.04**, including dependency issues encountered during
installation and modifications made to installer scripts to resolve
them.

------------------------------------------------------------------------

# Author

**Vipul Kumar Verma**\
B.Tech CSE (AIML), Second Year\
VIT Bhopal University\
Madhya Pradesh, India

------------------------------------------------------------------------

# Repository Structure

eSim/ │ 
├── Ubuntu/ │ 
     └── install-eSim.sh (modified installer scripts) │
├── REPORT.md │
└── README.md

------------------------------------------------------------------------

# System Environment

  Component          Details
  ------------------ --------------
  Operating System   Ubuntu 25.04
  Architecture       64-bit
  Shell              Bash
  Package Manager    apt

------------------------------------------------------------------------

# Installation Procedure

Clone the repository:

git clone https://github.com/vipul-09/eSim.git cd eSim git checkout
installers

Run the installer:

cd Ubuntu chmod +x install-eSim.sh ./install-eSim.sh --install

------------------------------------------------------------------------

# Issues Identified During Installation

1.  Unsupported Ubuntu version detection
2.  Missing Python dependency (python3-distutils)
3.  Missing KiCad dependency
4.  copyKicadLibrary function failure
5.  Incorrect xz-utils installation command
6.  Missing NGHDL dependency
7.  Missing logo.png resource file
8.  Missing frontend directory
9.  GUI icon loading issue
10. SKY130 PDK installation permission error

------------------------------------------------------------------------

# Key Fixes Implemented

## Ubuntu 25.04 Compatibility

Installer modified to allow Ubuntu 25.04:

case $VERSION_ID in
"22.04"|"25.04")
    if [[ "$FULL_VERSION" == "22.04.4" \]\]; then
SCRIPT="$SCRIPT_DIR/install-eSim-22.04.sh"
    else
        SCRIPT="$SCRIPT_DIR/install-eSim-23.04.sh" fi ;;

------------------------------------------------------------------------

## Python Dependency Fix

Ubuntu 25 removed python3-distutils.

Replacement:

python3-setuptools

------------------------------------------------------------------------

## KiCad Library Copy Fix

Original installer assumed:

\~/.config/kicad/7.0

Modified to:

kicad_config="$HOME/.config/kicad"
mkdir -p "$kicad_config"

------------------------------------------------------------------------

## xz-utils Installation Fix

Incorrect command:

sudo apt xz-utils

Correct command:

sudo apt install xz-utils

------------------------------------------------------------------------

## SKY130 PDK Permission Fix

Original path:

/usr/share/local

Modified installation:

\$HOME/.local/bin/volare enable --pdk sky130 --pdk-root \$HOME/.volare

------------------------------------------------------------------------

# Dependencies Installed

-   Qt5
-   Python 3.13
-   PyQt5
-   Matplotlib
-   NumPy
-   Tkinter
-   OpenModelica
-   Ngspice
-   KiCad
-   NGHDL
-   SKY130 PDK

------------------------------------------------------------------------

# Final Result

After resolving the installation issues:

-   eSim installed successfully
-   GUI launched without errors
-   All icons and frontend resources loaded correctly

Run eSim using:

esim

------------------------------------------------------------------------

# Documentation

Detailed issue documentation is provided in:

REPORT.md

------------------------------------------------------------------------

# Repository Links

GitHub Fork:

https://github.com/vipul-09/eSim

Installer Branch:

https://github.com/vipul-09/eSim/tree/installers

------------------------------------------------------------------------

# Conclusion

Ubuntu 25 introduced several compatibility changes affecting the
installation of eSim 2.5. By analyzing installer scripts, identifying
dependency issues, and applying fixes, the installation was successfully
completed.
