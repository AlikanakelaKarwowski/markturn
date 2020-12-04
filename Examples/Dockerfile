# Linux Markturn build environment
FROM ubuntu:20.10
LABEL com.3cdigitalmedianetwork.distro="ubuntu" \
    com.3cdigitalmedianetwork.email="alex@3cdigitalmedianetwork.com"

# Install packages for C++ build environmt
RUN apt-get update && apt-get install -y \
    # Build
    make \
    ninja-build \
    # Utilitys
    curl \
    # Dev Libraries
    libcurl4-openssl-dev \
    libyaml-cpp-dev \
    libjsoncpp-dev \
    libxml2-dev \
    # Clone
    git \
    # Compile
    g++ \
    # Cleanup
    && rm -rf /var/lib/apt/lists/*

# Install cmake binary directly
ARG CMAKE_VERSION=3.18.4
ARG CMAKE_URL=https://cmake.org/files/v3.18/cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz
RUN curl -L $CMAKE_URL | tar xvz --strip-components=1 -C /usr/local

# Install Local version of CLI11
# ADD CLI11.hpp /usr/include/

# Install CLI11.hpp from URL
ARG CLI11_VERSION=1.9.1
ARG CLI11_URL=https://github.com/CLIUtils/CLI11/releases/download/v${CLI11_VERSION}/CLI11.hpp
ADD $CLI11_URL /usr/local/include/
