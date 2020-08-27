CLion example project for STM32H745 MCU and NUCLEO-H745ZI-Q board
====
Example project for [STM32H745 Nucleo-144](https://www.st.com/en/evaluation-tools/nucleo-h755zi-q.html) board. 
Supports two kernels, both compile and debug. This is temporary solution, until full support is implemented in CLion.

**This example is specially made for multi-core MCUs, like some of STM32H7xx chips**

**Single-core STM32 MCUs are [supported out-of-the-box](https://www.jetbrains.com/help/clion/embedded-development.html)**

  

Tools required
====
 * [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html) and STM32H7 software pack
 * [CLion 2020.2+](https://www.jetbrains.com/clion/) and it's bundled `Embedded Development Support` plugin
 * [GNU Arm Embedded Toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)
 * [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
 * (Windows only) [MinGW](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)

Functionality
====
Two LEDs are blinking. One is controlled by CM4 kernel, another obe by CM7 kernel

Project structure
====
The project contains two separate sub-projects, and each one is a complete CLion+CMake project 
with some shared code at the top level. Obviously CM7 is for Cortex-M7 kernel, CM4 forCortex-M4

Each of the projects is open in it's own CLion instance, and then builds, flashes, and debugs independently. 

Steps to make a similar project
====
 1. Install and configure all the required tools. See [CLion Quick start guide](https://www.jetbrains.com/help/clion/clion-quick-start-guide.html)
 1. Start STM32CubeMX and make a new project. 
    * **Note I. On the `Project Manager` tab, `Toolchain/IDE` field must be set to `STM32CubeIDE`**
    * **Note II. Do not use space, international, or special characters for project name or path**
 1. Generate the project code. STM32CubeMX will create a project folder with two separate subprojects
 1. Open each of them in [different CLion windows](https://www.jetbrains.com/help/clion/opening-reopening-and-closing-projects.html).
 Do not open the root project!
 1. Add `CMakeLists.txt` to each of the subprojects using a [template](https://gist.github.com/elmot/3b4f0e9f8b23864fdf8bb509c329d051)  
 1. Now check all the `TODO` comments in those `CMakeLists.txt` files and put actual values instead.
  [TODO tool window](https://www.jetbrains.com/help/clion/todo-tool-window.html)
 is a great feature for that. It's recommended to set subproject name to a form of `<project name>_<kernel name>`
 1. Right-click `CMakeLists.txt`in the `Project` tool window and then click `Load CMake Project`
 1. Your CMake project will be parsed. If there are errors, correct them and select `Tools -> CMake -> Reset Cache and Reload Prpject` from the main menu.
 Repeat until everything is fixed and the CMakeLists is successfully parsed.
 1. Create `Run Configurations` for both projects
    1. Select create new [Embedded GDB Server](https://www.jetbrains.com/help/clion/embedded-gdb-server.html) run configuration.
    1. Select `Target`. There shall be the only one.
    1. Select `Executable`. There shall be the only one.
    1. Set `tcp::<port number>` to `'target remote' args` field. 
    The port number may be virtually any in range 1024..65535 but it must nt clash with the conterpart project port number.
    Use the same number below  
    1. Locate `ST-LINK_gdbserver` executable and select for as `GDB server`
    The executable resides in the in `STM32CubeIDE` installation folder, 
    `plugins/com.st.stm32cube.ide.mcu.externaltools.stlink-gdb-server.????/tools/bin/` subfolder. 
    Actual name varies from version too version, and exact name can't be provided
    1. Locate `STM32_Programmer_CLI` executable similar way, but the folder expected to be 
    `plugins/com.st.stm32cube.ide.mcu.externaltools.cubeprogrammer.???/tools/bin/` then add 
    *path to the folder* as a `-cp` key value to `GDB Server args` field
    1. Add `-t -d -p <port number> -m <core num>` keys to `GDB Server args` field. 
       * The `<core num>` parameter is `0` for Cortex-M7 or `3` for Cortex-M4 kernels. Other MCU models may support other numbers.
       * Final arguments form is `-cp <STM32_Programmer_CLI bin folder path> -t -d -p <port number> -m <core num>`
 1. Try to build both projects. If something goes wrong, fix your `CMakeLists.txt` and sources.
 1. Click `Debug` in both projects. The firmware for both kernels should be flashed and started.
 1. Enjoy your newly-created project.
