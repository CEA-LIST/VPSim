cmake_minimum_required(VERSION 3.10)

project(vpsim)

set(INSTALL_RELEASE_DIR ${CMAKE_SOURCE_DIR}/vpsim-release)

##### Configure & build QEMU #####
set(QEMU_SOURCE_DIR ${CMAKE_SOURCE_DIR}/vpsim-qemu)
set(QEMU_BINARY_DIR ${CMAKE_BINARY_DIR}/qemu-build)
# set(QEMU_BRANCH "qemu-for-vpsim")

if(CMAKE_BUILD_TYPE STREQUAL Debug)
    set(QEMU_ENABLE_DEBUG --enable-debug)
endif()

# Ensure the QEMU build directory exists (create it if not)
file(MAKE_DIRECTORY ${QEMU_BINARY_DIR})

add_custom_target(qemu
    # COMMAND ${CMAKE_COMMAND} -E echo "Checking out branch ${QEMU_BRANCH}"
    # COMMAND git checkout ${QEMU_BRANCH}
    COMMAND ${QEMU_SOURCE_DIR}/configure --target-list=aarch64-softmmu
        --disable-sparse --disable-sdl --disable-gtk --disable-vnc --disable-xen
        --disable-brlapi --disable-vnc-sasl --disable-vnc-jpeg --disable-vnc-png
        --disable-curses --disable-curl --disable-bluez  --disable-kvm --disable-bsd-user
        --disable-pie --disable-coroutine-pool --disable-attr --disable-blobs --disable-docs
        --disable-spice --disable-usb-redir --disable-guest-agent --disable-cap-ng --disable-libiscsi
        --disable-libusb --disable-werror ${QEMU_ENABLE_DEBUG}
    COMMAND make
    WORKING_DIRECTORY ${QEMU_BINARY_DIR}
)

##### Build VPSim #####

set(VPSIM_SOURCE_DIR ${CMAKE_SOURCE_DIR}/vpsim-systemc)
set(VPSIM_BINARY_DIR ${CMAKE_BINARY_DIR}/vpsim-build)

add_subdirectory(${VPSIM_SOURCE_DIR} ${VPSIM_BINARY_DIR})

# target to copy artifacts to release directory
add_custom_target(copy_artifacts
    COMMAND ${CMAKE_COMMAND} -E echo "Copying vpsim and qemu artifacts to release directory"
    COMMAND ${CMAKE_COMMAND} -E copy ${QEMU_BINARY_DIR}/aarch64-softmmu/qemu-system-aarch64 ${INSTALL_RELEASE_DIR}/lib/qemu/vpsim-qemu.so
    COMMAND ${CMAKE_COMMAND} -E copy ${VPSIM_BINARY_DIR}/vpsim ${INSTALL_RELEASE_DIR}/bin
    DEPENDS qemu vpsim
)

# Custom target to build everything and copy artifacts
add_custom_target(default ALL
    DEPENDS qemu vpsim copy_artifacts
    COMMENT "Built QEMU, VPSim, and copying artifacts"
)