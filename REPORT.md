![](images/media/image1.png){width="6.268055555555556in"
height="1.4680993000874891in"}

**eSim Summer Fellowship 2026 Tasks**

[Screening Task Report]{.underline}

Submitted by : Vipul Kumar Verma

B.Tech CSE (AIML), Second Year

VIT Bhopal University

Kothrikalan, Sehore,

Madhya Pradesh, 466114

**Task 4. eSim Upgradation(CSE and related fields)**

**eSim 2.5 Installation Report on Ubuntu 25.04**

**1. Introduction**

This report documents the installation and debugging process of **eSim
2.5** on **Ubuntu 25.04** as part of the **FOSSEE eSim Summer Fellowship
2026 screening task**.

The objective of this task was to:

-   Install eSim on Ubuntu 25.04

-   Identify dependency and installation issues

-   Fix at least one of the issues by modifying installer scripts or
    configuration files

-   Document the entire process clearly

During the installation process, multiple issues were encountered
related to:

-   Unsupported Ubuntu version detection

-   Missing dependencies

-   Missing frontend resources

-   Missing installer files

These issues were analyzed and fixed to ensure successful installation
and execution of eSim.

**2. System Environment**

Operating System: Ubuntu 25.04\
Architecture: 64-bit\
Shell: Bash\
Installation Type: Native Ubuntu Installation

**3. Repository Setup**

The official eSim repository was forked from the FOSSEE GitHub
repository and cloned locally.

Commands used:

git clone https://github.com/vipul-09/eSim.git

cd eSim

git checkout installers

**4. Installation Procedure**

The installation was initiated using the provided installer script.

cd Ubuntu

chmod +x install-eSim.sh

./install-eSim.sh \--install

However, the installer initially failed due to **unsupported Ubuntu
version detection**.

**5. Issue 1 -- Unsupported Ubuntu Version**

Error Message:

Detected Ubuntu Version: 25.04

Unsupported Ubuntu version: 25.04

Cause:

The installer script only supported Ubuntu versions **22.04, 23.04, and
24.04**, and did not recognize **Ubuntu 25.04**.

Fix Implemented:

The install-eSim.sh script was modified to allow Ubuntu **25.04** to use
the **23.04 installer script**.

Step 1 : open eSim.sh in nano editor

**nano \~/eSim/Ubuntu/install-eSim-scripts/install-eSim-23.04.sh**

Step 2 : press ctrl+w and search case \$VERSION_ID in

Step 3: add "25.04" after "22.04" in ' **case \$VERSION_ID in
\"22.04\"'**

Step 4 : Save and rerun install command

Modified Code:

case \$VERSION_ID in

\"22.04\"\|\"25.04\")

if \[\[ \"\$FULL_VERSION\" == \"22.04.4\" \]\]; then

SCRIPT=\"\$SCRIPT_DIR/install-eSim-22.04.sh\"

else

SCRIPT=\"\$SCRIPT_DIR/install-eSim-23.04.sh\"

fi

;;

Result:

The installer successfully continued using the **23.04 installation
script**.

![](images/media/image2.png){width="7.125in" height="3.59375in"}

**6. Issue 2 -- Missing Python Package**

During installation another error occurred:

E: Package \'python3-distutils\' has no installation candidate

Cause:

Ubuntu 25.04 removed the python3-distutils package which was required by
the installer.

Fix Implemented:

The missing dependency was replaced with:

python3-setuptools

This allowed the Python environment to install successfully.

![](images/media/image3.png){width="7.125in" height="4.1875in"}

**7. Issue 3 -- Missing KiCad Dependency**

During installation, KiCad dependencies required by eSim were not
automatically installed.

Error encountered:

kicad: command not found

Cause:

The installer script assumed KiCad was already installed or available in
the repository during installation.

Fix Implemented:

KiCad was manually installed using:

sudo apt install kicad

Result:

KiCad schematic editor and PCB tools required by eSim became available.

![](images/media/image4.png){width="7.041666666666667in"
height="2.2604166666666665in"}

**8. Issue 4 -- copyKicadLibrary Function Failure**

During the installation process an issue occurred while executing the
copyKicadLibrary function inside the installer script.

Error Message:

cp: cannot stat \'kicadLibrary/template/sym-lib-table\': No such file or
directory

Cause:

The installer script assumed a fixed KiCad configuration directory:

\~/.config/kicad/7.0

However, in **Ubuntu 25.04**, the KiCad configuration directory may vary
depending on the installed KiCad version. Additionally, the script
expected the sym-lib-table file at a specific location in the
repository.

If the directory structure differed or the required path was missing,
the script failed while copying the symbol library configuration.

Original Function:

function copyKicadLibrary\
{\
\
\# Extract custom KiCad Library\
tar -xJf library/kicadLibrary.tar.xz\
\
if \[ -d \~/.config/kicad/7.0 \]; then\
echo \"kicad config folder already exists\"\
else\
echo \".config/kicad/7.0 does not exist\"\
mkdir -p \~/.config/kicad/7.0\
fi\
\
\# Copy symbol table for eSim custom symbols\
cp kicadLibrary/template/sym-lib-table \~/.config/kicad/7.0/\
echo \"symbol table copied in the directory\"\
\
}

Fix Implemented:

The function was modified to make the installer more robust by:

-   Using a generic KiCad configuration directory

-   Checking multiple possible locations for the sym-lib-table file

-   Creating the configuration directory automatically if it does not
    exist

Modified Function:

function copyKicadLibrary\
{\
\
echo \"Setting up KiCad library\...\"\
\
kicad_config=\"\$HOME/.config/kicad\"\
\
\# Create KiCad config directory if not present\
mkdir -p \"\$kicad_config\"\
\
\# Possible locations of sym-lib-table\
if \[ -f \"kicadLibrary/template/sym-lib-table\" \]; then\
cp kicadLibrary/template/sym-lib-table \"\$kicad_config/\"\
echo \"Symbol table copied from kicadLibrary\"\
elif \[ -f \"library/kicadLibrary/template/sym-lib-table\" \]; then\
cp library/kicadLibrary/template/sym-lib-table \"\$kicad_config/\"\
echo \"Symbol table copied from library folder\"\
else\
echo \"sym-lib-table not found in repository\"\
fi\
\
}

Result:

After modifying the copyKicadLibrary function, the installer was able to
correctly locate and copy the KiCad symbol library configuration. This
ensured that eSim could properly detect the custom KiCad symbol
libraries required for schematic design.

![](images/media/image5.png){width="6.84375in"
height="3.5416666666666665in"}

**9. Issue 5 -- xz-utils Installation Command Error**

During the installation process an error occurred while installing the
xz-utils dependency.

Error Message:

E: Invalid operation xz-utils\
Error! Kindly resolve above error(s) and try again.\
Aborting Installation\...

Cause:

The installer script attempted to install the xz-utils package using an
incorrect apt command. The script used:

sudo apt xz-utils

However, the correct syntax for installing a package using apt requires
the install keyword.

Original Code:

sudo apt xz-utils

Fix Implemented:

The installer script was modified to use the correct installation
command.

Modified Code:

sudo apt install xz-utils

The xz-utils package provides the xz compression utility required to
extract .tar.xz archives used during the installation of certain eSim
components. Without installing this package correctly, the installer
cannot extract compressed files.

By adding the install keyword to the command, the apt package manager
was able to correctly install the xz-utils dependency required for
archive extraction.

Result:

After modifying the installer script, the xz-utils package was installed
successfully and the installation process continued without errors.

![](images/media/image6.png){width="6.979166666666667in"
height="1.9392333770778654in"}

**10. Issue 6 -- Missing NGHDL Dependency**

eSim requires **NGHDL** for VHDL simulation.

During installation the following issue appeared:

nghdl: command not found

Cause:

The installer script did not properly install NGHDL for Ubuntu 25.04.

Fix Implemented:

NGHDL was installed manually using:

sudo apt install ghdl

Result:

The VHDL simulation backend required by eSim became functional.

![](images/media/image7.png){width="6.333333333333333in"
height="3.1770833333333335in"}

**11. Issue 7 -- Missing logo.png File**

During installation the script failed with the following error:

cp: cannot stat \'images/logo.png\': No such file or directory

Cause:

The installer expected the file:

images/logo.png

However, the file was missing in the installation directory.

Fix Implemented:

The missing file was downloaded from the official repository.

mkdir -p images\
\
wget -O images/logo.png \\\
https://raw.githubusercontent.com/FOSSEE/eSim/master/images/logo.png

Result:

The installer was able to continue successfully after adding the
required image.

![](images/media/image8.png){width="7.0625in"
height="3.8229166666666665in"}

**12. Issue 8 -- Missing Frontend Directory**

After installation, running the command:

esim

produced the following error:

/usr/bin/esim: line 2: cd: /home/user/eSim/Ubuntu/src/frontEnd: No such
file or directory

Cause:

The installer script expected the directory:

Ubuntu/src/frontEnd

However, the directory structure in the cloned repository did not
contain this path.

Fix Implemented:

The path in /usr/bin/esim was corrected to point to the correct project
directory.

Result:

The eSim GUI launched successfully.

![](images/media/image9.png){width="7.114583333333333in"
height="3.34375in"}

**13. Issue 9 -- Missing GUI Icons**

After launching eSim, some GUI icons were not displayed correctly.

Cause:

The application could not locate image resources due to incorrect
relative paths.

Fix Implemented:

The missing image files were copied into the correct images directory
within the eSim installation folder.

Result:

All icons were successfully loaded in the eSim GUI.

![](images/media/image10.png){width="4.34375in"
height="1.6979166666666667in"}

**14. Issue 10 -- SKY130 PDK Installation Permission Error**

During the installation process an error occurred while installing the
**SKY130 Process Design Kit (PDK)**.

Error Message:

\[Errno 13\] Permission denied: \'/usr/share/local\'\
Error! Kindly resolve above error(s) and try again.\
Aborting Installation\...

Cause:

The installer script attempted to install the SKY130 PDK in the system
directory:

/usr/share/local

This directory requires **root privileges** to write files. However, the
installation script executed the SKY130 installation command without
proper permission handling.

Additionally, newer versions of **Volare install the SKY130 PDK inside
the user\'s home directory**, which caused a mismatch between the
expected installer path and the actual installation path.

Original Code:

volare enable \--pdk sky130 \--pdk-root /usr/share/local/
0fe599b2afb6708d281543108caf8310912f54af

Fix Implemented:

The installer script was modified to install the SKY130 PDK in the
**user directory** instead of the system directory.

Modified Code:

\$HOME/.local/bin/volare enable \--pdk sky130 \--pdk-root \$HOME/.volare
0fe599b2afb6708d281543108caf8310912f54af

The path used later in the script to copy the SKY130 libraries was also
updated to match the correct installation location.

mv
\$HOME/.volare/volare/sky130/versions/\*/sky130A/libs.ref/sky130_fd_pr
\$HOME/.local/share/

Why this Solution was Applied:

Installing the SKY130 PDK inside /usr/share/local requires
administrative privileges and can cause permission errors when the
installer is executed by a normal user. Installing the PDK inside the
**user's home directory** avoids permission conflicts and follows modern
Linux practices for user-space installations.

How the Fix Solved the Issue:

By modifying the installer script to install the SKY130 PDK inside the
user\'s home directory and updating the paths accordingly, the installer
was able to download and extract the PDK successfully without requiring
elevated permissions.

Result:

After applying this fix, the SKY130 PDK installation completed
successfully and the eSim installer proceeded to the next steps without
encountering permission errors.

![](images/media/image11.png){width="6.53125in"
height="4.145833333333333in"}

**15. Installation of Dependencies**

The installer successfully installed multiple dependencies including:

-   Qt5 libraries

-   Python 3.13

-   PyQt5

-   Matplotlib

-   NumPy

-   Tkinter

-   OpenModelica

-   Ngspice

-   System utilities required by eSim

These dependencies are required for the **eSim graphical user interface
and simulation environment**.

**16. Final Result**

After fixing the issues, eSim was installed successfully.

The application launched using:

esim

The GUI opened correctly with all icons and functionalities available.

**17. Conclusion**

During the installation of eSim 2.5 on Ubuntu 25.04, several
compatibility issues were encountered due to newer Ubuntu packages and
installer assumptions.

These issues were resolved by:

-   Updating the installer script to support Ubuntu 25.04

-   Fixing missing Python dependencies

-   Installing missing KiCad and NGHDL dependencies

-   Adding missing resource files such as logo.png

-   Correcting frontend directory paths

-   Fixing GUI icon loading issues

After applying these fixes, eSim installed and executed successfully.

**18. Repository Link**

GitHub Fork:

<https://github.com/vipul-09/eSim>

<https://github.com/vipul-09/eSim/tree/installers>
