# -------------------------------------------------------------
# KOS Complete Toolchain Image w/ Cmake (mrneo240/dcdev-kos-toolchain-cmake)
# -------------------------------------------------------------
#
# This image is meant to be used as a fully featured KOS development environment. It includes both the SH4 and
# ARM cross-compiler toolchains, the full KallistiOS library, all included utilities and addons, and some extra
# useful utilities and programs that you might need to compile any Dreamcast project. It's based on Alpine to keep
# the size to a minimum, though convenience is preferred over a slightly larger image, hence the preinstalled tools
# and the inclusion of the KOS and KOS-PORTS source code rather than the compiled binaries and headers only.
#
# This image contains an entry scipt that automatically loads the KOS environment variables, so it can compile a
# project or use any of the included tools without entering the interactive shell. If no command is provided, an
# interactive bash shell is automatically launched.
#
# Build Commands:
# ---------------
# GCC 9 Toolchain: `docker build -t mrneo240/dcdev-kos-toolchain-cmake:gcc-9 .`
# GCC 4 Toolchain: `docker build --build-arg KOS_GCC_VER=4 -t mrneo240/dcdev-kos-toolchain-cmake:gcc-4 .`
#
# Example GCC 9 Usage (run from your project directory):
# --------------
# Simple Makefile:   `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-9 make`
# Build Script:      `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-9 ./dc_build.sh`
# Interactive Shell: `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-9`
#
# Example GCC 4 Usage (run from your project directory):
# --------------
# Simple Makefile:   `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-4 make`
# Build Script:      `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-4 ./dc_build.sh`
# Interactive Shell: `docker run -it --rm -v $PWD:/src mrneo240/dcdev-kos-toolchain-cmake:gcc-4`
#
# Notes
# -----
# More specific tags are provided so you can pin your builds to an exact version of this image if needed. For
# example, you can specify GCC 9.3 by using the mrneo240/dcdev-kos-toolchain-cmake:gcc-9.3 image, or you can specify
# a certain GCC 9.3 image by using a version tag like the mrneo240/dcdev-kos-toolchain-cmake:gcc-9.3__v1.0.0 image.
# This is mostly because while the GCC toolchains don't change very often and are usually compatible as long as
# you're using the same GCC major version (e.g. 9.2 and 9.3), however the KOS library changes more frequently
# and may prevent your project from compiling on a newer version without making some (usually minor) changes.


#
# Global Arguments
#

# Defaults to verbose output off as it should build faster without all of the console printing.
# To debug build problems, add the flag `--build-arg VERBOSE=true` when building the image to
# have it print all output from each download and compilation step.
# NOTE: If you are using the latest Docker versions with buildx, you'll also need to increase
#       the log limit (https://github.com/docker/buildx/issues/484) and add optionally include
#       the flag `--progress=plain` if you want to see the full output.
ARG VERBOSE=false

# Choose GCC major version. Supported values are 9 (SH4 9.3/ARM 8.4), 4 (both 4.7.4)
# Can be set using `--build-arg KOS_GCC_VER=4` when building, default is 9)
# NOTE: You can technically use any available tag number from the `einsteinx2/dcdev-gcc-toolchain` image here
ARG KOS_GCC_VER=9

FROM mrneo240/dcdev-kos-toolchain:gcc-$KOS_GCC_VER as toolchain
LABEL maintainer "Hayden Kowalchuk <hayden@hkowsoftware.com>"

# Set build arguments
ARG VERBOSE

# Switch back to root
USER root

# Install Cmake
RUN printf "Installing Cmake...$NL" \
    # Install useful tools for building Dreamcast projects
    && apt update \
    && apt upgrade -y \
    && apt install -y --no-install-recommends --no-upgrade \
        cmake  \
    && echo "dash dash/sh boolean false" | debconf-set-selections \
    && dpkg-reconfigure --frontend=noninteractive dash \
    && apt clean \
    && printf "Done.\n"

# Default to /src directory
WORKDIR /src

# Activate user
USER dev

# Run any command passed in a bash shell with the KOS environment loaded
ENTRYPOINT ["/usr/local/bin/entry.sh"]

# If no command is passed, run an interactive bash shell
CMD ["/bin/bash"]
