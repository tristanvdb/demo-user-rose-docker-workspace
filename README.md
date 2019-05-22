Demo of ROSE Compiler's Docker Workspace
========================================

This repository demonstrates how to use ROSE Compiler's Docker images and associated tools.

## Getting Started

```
# Clone the demonstration workspace (target direcotry name does not matter)
git clone --recursive https://github.com/tristanvdb/demo-user-rose-docker-workspace rose-docker-workspace

# Load the environment variable and aliases (can be added to your .bashrc)
source rose-docker-workspace/init.rc

# Select a Linux distribution and compiler toolchain:
#  - in this case: Ubuntu 18.04 with default GCC and Boost
#  - can also be added to .bashrc (or at the end of rose-docker-workspace/init.rc)
export distrib_name=ubuntu distrib_version=18.04 toolchain=native

# Set the version of ROSE
#  - using `rd-last release` to obtain the tag for the latest release of ROSE Compiler
#  - select a `release` build of ROSE, we also provide Docker images for `debug` builds
#  - can also be added to .bashrc, however you should use the actual value of `$rose_version` instead of the call to `rd-last`
export rose_version=$(rd-last release) rose_build=release

# Pull the docker image and personalize to your Linux user:
#  - pull (generic) image from Docker Hub
#  - add user to the image with same username and UID as you
#      - simplify file-system sharing between host and container
#  - you can install packages using the environment variable `$extra_packages` (`extra_packages="vim emacs gdb" rd-pull`)
rd-pull

# Test an hello-world

```



