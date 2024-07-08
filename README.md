
# VPSim
VPSim is a digital architecture design environment used to speed up design space exploration (DSE) through simulation and rapid validation. Users can model a complex memory hierarchy and estimate its performance, for example, thanks to a wide variety of available processor and device models.

[VPSim](https://list.cea.fr/en/page/vpsim-explore-simulate-and-validate-complex-electronic-architectures)

# Installation

## Requirements
- To build VPSim & the modified QEMU on a fresh Ubuntu 22.04 LTS installation, you will need to install these libraries:

    ```sh
    apt install -y pkg-config libglib2.0-dev libpixman-1-dev
    ```
- For the modified qemu, as it is based on an old version you will need Python2 to be installed :
    ```sh
    apt install -y python2
    ```
    and export it in your shell:
    ```sh
    export PYTHON=/usr/bin/python2
    ```
Now your environment is set, please follow the following steps:

## Clone & Build
1. Clone this git repository and update its submodules:

    ```sh
    git clone git@github.com:CEA-LIST/VPSim.git
    cd VPSim
    git submodule init
    git submodule update --recursive
    ```

2. Build targets and copy artifacts to the release directory [./vpsim-release](./vpsim-release) sub-directory:

    to do so, we configured a custom command in CMakeLists that rename and copy the targets to the appropriate folder in the release test environment
    ```sh
    mkdir build && cd build
    cmake ..
    make all -j
    cd ../
    ```

# Getting to test VPSim and run your first simulation
- To try VPSim and customize your architecture to simulate, please follow the `README.md` file in the following sub-directory:
  - [vpsim-release](https://github.com/CEA-LIST/vpsim-release.git)
