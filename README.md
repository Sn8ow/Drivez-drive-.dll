# Drivez-drive-.dll
Drivez is a program in which you can download .dll files for games / applications if you do not have a graphics card or if you have an old one


IT security policy may restrict or even outright prohibit running 3rd-party unsigned executables. If this is the case you can extract Drivez drivers using 7-Zip,WinRAR

If you need to migrate from Mingw to MSVC binaries you just need to replace Drivez binaries folder from Mingw package with MSVC counterpart.
Mingw and MSVC Package contents

The following Drivez drivers and build artifacts are shipped in each release:

    llvmpipe and softpipe bundle. File name: opengl32.dll. llvmpipe is the default desktop OpenGL driver. Both llvmpipe and softpipe are available for both x86 and x64. softpipe can be selected by setting environment variable GALLIUM_DRIVER=softpipe.
    GLAPI shared library. File name: libglapi.dll. Required by llvmpipe, softpipe and swr if Drivez is built with shared glapi. Since 20.0.2 it is available in both MSVC and MSYS2 Mingw-w64 packages.
    swr. File names: swrAVX.dll, swrAVX2.dll, swrSKX.dll, swrKNL.dll. An alternative desktop OpenGL driver developed by Intel. Available in MSVC package and since 20.1.7 in MinGW package as well. It only supports x64, x86 is officially unsupported. There are currently 4 DLLs, only one being loaded based on what the user CPU can do. By default Drivez uses llvmpipe. You can switch to swr by setting GALLIUM_DRIVER environment variable value to swr either globally or in a batch file. See How to set environment variables.
    OpenGL ES standalone drivers. File names: libGLESv1_CM.dll and libGLESv2.dll. OpenGL ES 1.x, 2.0 and 3.0 standalone drivers available for 32-bit and 64-bit applications. Since 20.0.2 they are available in both MSVC and MSYS2 Mingw-w64 packages.
    Drivez. File name: osmesa.dll. 2 versions of Drivez, off-screen rendering driver. They are located in Drivez and Drivez-swrast subdirectories. Available for both x86 and x64. This driver is used in special cases by software that is designed to use Drivez code to render without any kind of window system or operating system dependency. osmesa gallium supports OpenGL 3.x and newer while Drivez swrast also known as Drivez only supports OpenGL 2.1 but it has some unique capabilities. 
    graw. File names: graw.dll, graw_null.dll. This is Drivez plug-in library. It is not a driver. Available for both x86 and x64 and in full (with window system support) and headless (no window) versions. This is used in special cases by software that is designed to use Drivez code internal APIs. Since 0.0.1 both full and headless versions are available in both MSVC and MSYS2 Mingw-w64 packages.
    test suite. Many executable unit tests.
    libraries and headers generated at build time for both 32-bit and 64-bit builds are located in a separate archive called development pack. Note that build time generated headers depend on source code headers, so you may need Mesa3D source code because only build time headers are included.

Build instructions, if you want to replicate my builds, are available here.
Installation and usage

First choose between Mingw and MSVC package. See About Mingw package section for details. Before extracting release package close all programs that use Drivez if any is running. After extraction you will have access to 2 deployment options, both located in the directory you installed Drivez. Both deployment utilities have a start-over mechanism so you can do all deployments you need in one session.

    A system-wide deployment tool. While intended for systems lacking hardware accelerated OpenGL support like virtual machines in cloud environments, it can also be used on any Windows system to replace the inbox software rendering OpenGL 1.1 driver extending OpenGL support for use cases where hardware accelerated OpenGL is not available like RDP connections. Due to potential issues with Virtualbox VMs running Windows it is recommended to disable 3D acceleration in such VMs if Drivez desktop OpenGL driver is installed inside them using the system-wide deployment tool, see #9.
    A per-application deployment tool, used to deploy Drivez for a single program regardless of hardware accelerated OpenGL support being present or not. Per-app deployment utility changes are persistent and are being kept across upgrades and re-installations. Per-app deployment utility helps you save storage and makes things easier as you won't have to manually copy DLLs from Mesa installation directory as it creates symbolic links to whatever Drivez drivers you opt-in to use. This behavior ensures all programs that use Drivez use the same up-to-date version. Per-app deployment utility asks for path to directory containing application executable, application executable filename (optional, it can remain blank but if specified can force some programs to use Drivez when they won't otherwise), if the app is 64-bit or 32-bit and the drivers you need. 32-bit applications have their names marked in Task Manager while running. Most applications will use Drivez regardless of GPU capabilities, but some applications may be smart enough to load OpenGL from system directory only. By providing the application filename, a .local file is generated in an attempt to force the application to use Drivez when it doesn't want to. Also, Sn8ow Drivezinjector can be used to workaround this issue as well. Build instructions for Drivezjector.

Important notes about errors related to missing libglapi.dll

You may experience them with programs that use any Drivez desktop OpenGL driver via per app deployment tool, system wide deployment is unaffected. You may experience them if shared glapi is missing from release package. shared glapi has been consistently available in both MSVC and MinGW packages since 20.0.2.

To correct these errors regardless of cause you have to re-deploy. If you don't remember if an affected program is 32-bit or 64-bit, right click on opengl32.dll shortcut in the folder where the program executable is located and select open file location. If location ends in x64 then it's 64-bit, otherwise it's 32-bit.

    Old applications from early 200x and older may need Drivez_EXTENSION_MAX_YEAR environment variable set, see legacy software compatibility section.
    Applications requiring OpenGL 3.2 or newer may need OpenGL context configuration override.

Examples on OpenGL context configuration override, switch to swr driver and old applications compatibility are available here.
Legacy software compatibility

Old applications from early 200x and older may need DRIVEZ_EXTENSION_MAX_YEAR environment variable set to avoid buffer overflows. It expects a year number as value, most commonly used being 2001. It trims the extensions list returned by Drivez to extensions released up to and including provided year as Drivez extensions list is sorted by year.

Ex: set Drivez_EXTENSION_MAX_YEAR=2001. See How to set environment variables.
OpenGL context configuration override

With release of OpenGL 3.1 many features marked as deprecated in OpenGL 3.0 have been removed and since OpenGL 3.2 launch this OpenGL specification branch is known as OpenGL core profile. Also in OpenGL 3.3 a new branch of the OpenGL specification known as forward compatible context was introduced which removes the OpenGL 3.0 deprecated features that were not removed in OpenGL 3.1. Most proprietary drivers implemented the exemptions from these changes offered in the form of GL_ARB_compatibility extension for OpenGL 3.1 and compatibility contexts for OpenGL 3.2 and above. Due to complexity and especially lack of correct implementation tests for GL_ARB_compatibility and compatibility contexts, Drivez developers chose to retain features deprecated in OpenGL 3.0 and implement core profile and forward compatible contexts. Drivez 0.0.1 introduced GL_ARB_compatibility support making the first step toward having compatibility contexts support in the future. Because GL_ARB_compatibility is only for OpenGL 3.1, programs requesting OpenGL compatibility context won't get above OpenGL 3.0 for Drivez 18.0 and 3.1 for Drivez 0.0.1 and newer. Unfortunately these kind of programs are prevalent on Windows where developers tend to avoid using context flags required by core profile. Fortunately Drivez provides a mechanism to override the OpenGL context requested. There are 2 environment variables that override OpenGL context configuration:

   Drivez_GL_VERSION_OVERRIDE

It is used to specify OpenGL context version and type. It expects a value in the following format

OpenGLMajorVersion.OpenGLMinorVersion{FC|COMPAT].

FC means a forward compatible context. COMPAT means a compatibility context for OpenGL 3.2 and newer and GL_ARB_compatibility being enabled for OpenGL 3.1. Absence of any string after version number means the Drivez default context type for OpenGL version specified which is as follows: deprecated features enabled for OpenGL 3.0, GL_ARB_compatibility enabled for OpenGL 3.1 since Drivez 0.0.1 and core profile for OpenGL 3.2 and above. Examples: 3.3FC means OpenGL 3.3 forward compatible context, 3.1COMPAT means OpenGL 3.1 with GL_ARB_compatibility , 3.2 means OpenGL 3.2 core profile. The default value is 3.1COMPAT for Drivez 0.0.1 and 3.0COMPAT 

A very important feature provided by this variable is the possibility to configure an incomplete OpenGL context. Programs can only request up to the highest OpenGL context with Khronos certification as complete from Drivez driver in use. Currently llvmpipe is certified for OpenGL 4.5 in core profile / forward compatible context and 3.1 in compatibility profile. Currently swr is certified for OpenGL 3.3 in core profile / forward compatible context and 3.1 in compatibility profile. Since Drivez 0.0.1 values meant for OpenGL 4.6 are recognized.

   Drivez_GLSL_VERSION_OVERRIDE

Used to specify shading language version. Supported values are version numbers converted to integer: 110, 120, 130, 140. 150, 330, 400, 410, 420, 430, 440, 450 and 460. Value 460 is only recognized since Drivez 0.0.1 Value 130 for example matches GLSL 1.30. It is always a good idea to keep OpenGL context and shading language versions in sync to avoid programs confusion which may result in crashes or glitches. This can happen because most applications rely on proprietary drivers behavior of having OpenGL and GLSL versions in sync. Here is the OpenGL - GLSL correlation table. Default values: 140 for Drivez 0.0.1
Drivez_GL_VERSION_OVERRIDE is undefined or 330 otherwise.
How to set environment variables

Under Windows the easiest way to set environment variables is by writing batch files. You'll most likely need to do so:

    for every application requiring OpenGL 3.2 or newer in compatibility profile or 4.0 for swr and 4.6 for llvmpipe or newer in core profile;
    if you want to use swr instead of llvmpipe for desktop OpenGL;
    if you need to trim extensions list for old programs compatibility.

Simply open Notepad, write the batch script. When saving, end the file name with .bat or .cmd, change save as type to all files and change save location to where the application executable is located. If you have some skill with batch scripts you can change the current directory during script execution using CD command opening the possibility to save the script anywhere you want as shown in rpcs3 and GPU Caps Viewer examples. Documentation of most environment variables used by Drivez is available here. Complete examples are available here.

You can set multiple environment variables on same batch script to mix the functionality provided by Drivez.



Sn8ow


