# -------------------------------------------------
# GCC Base Image (mrneo240/dcdev-gcc-base)
# -------------------------------------------------
#
# This image is meant to be a minimally sized image to be used as a base image to create the GCC cross-compiler
# toolchain image that will be used to build the final fully functional KOS toolchain images to prevent the need to
# download all of the dependencies each time when rebuilding images locally (or CI if image caching is available).
# When cached locally, this saves hundreds of megabytes of package downloads plus install time.
#
# Build Command:
# --------------
# `docker build -t mrneo240/dcdev-gcc-base:latest .`


FROM debian:stretch-slim
LABEL maintainer "Hayden Kowalchuk <hayden@hkowsoftware.com>"

# Download all necessary dependencies needed to build the KOS GCC cross-compiler toolchain
RUN \
    # Install dependencies
    printf "Installing dependencies...\n" \
    && apt update \
    && apt upgrade -y \
    && apt install -y --no-install-recommends --no-upgrade \
        binutils pkg-config gawk patch bzip2 tar xz-utils unzip libgmp-dev libmpfr-dev libmpc-dev gettext libelf-dev texinfo bison flex sed build-essential diffutils \
        git curl wget python subversion openssl ca-certificates \
        libjpeg-dev libpng-dev \
        cmake make \
    && echo "dash dash/sh boolean false" | debconf-set-selections \
    && dpkg-reconfigure --frontend=noninteractive dash \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* \
    && printf "Done.\n" \
\
    # Prepare .bashrc
    && printf ". /etc/profile\n" > ~/.bashrc \
\
    # Create verbose output toggle script used in child images (file can be appended to by child images to store more environment variables)
    && printf 'if $VERBOSE; then\n  export REDIRECT="/dev/stdout";\n  export NL="\\n";\nelse\n  export REDIRECT="/dev/null";\n  export NL="";\nfi\n' > /tmp/dockerenv.sh \
    && chmod a+x /tmp/dockerenv.sh

# Default to /src directory
WORKDIR /src

# Run any command passed in a bash shell
ENTRYPOINT ["/bin/bash", "-c"]

# If no command is passed, run an interactive bash shell
CMD ["/bin/bash"]
