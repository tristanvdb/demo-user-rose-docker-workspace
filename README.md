Demo of ROSE Compiler's Docker Workspace
========================================

This repository demonstrates how to use ROSE Compiler's Docker images and associated tools.

## Getting Started

### Initial Setup

Clone the demonstration workspace, the target directory name does not matter.
```
git clone --recursive https://github.com/tristanvdb/demo-user-rose-docker-workspace rose-docker-workspace
```

Load the environment variable and aliases. This command can be added to `~.bashrc` to make the ROSE Docker Environment available by default.
```
source rose-docker-workspace/init.rc
```

### Pull an image

Select a Linux distribution and compiler toolchain:
  - Ubuntu 18.04
  - Default GCC and Boost
  - Could also be added to `~/.bashrc`
```
export distrib_name=ubuntu distrib_version=18.04 toolchain=native
```

Set the version of ROSE:
  - using `rd-last release` to obtain the tag for the latest release of ROSE Compiler
  - select a `release` build of ROSE, we also provide Docker images for `debug` builds
  - can also be added to .bashrc, however you should use the actual value of `$rose_version` instead of the call to `rd-last`
```
export rose_version=$(rd-last release) rose_build=release
```

Pull the docker image and personalize to your Linux user:
  - pull (generic) image from Docker Hub
  - add user to the image with same username and UID as current user for seamless FS sharing between container and host
  - you can install packages using the environment variable `$extra_packages` (for example: `extra_packages="vim emacs gdb" rd-pull`)
```
rd-pull
```

### Running basic command in the container

You can launch the container and get to an interractive bash shell with:
```
rd-run
```

However, you can also provide the command to run directly after `rd-run`.
For example, you can check ROSE version with:
```
rd-run rose-compiler --version
```

Next, you can set a variety of environment variables for `rd-run`:
 - `$workspace` expects a path relative to the host, `rd-run` mounts that path as `/workspace` in the container.
 - `$workdir` expects a path relative to the container, `rd-run` sets it as the working directory of the container (defaults to `/workspace` if `$workspace` is provided)
 - `$extradockeropts` is used to provide additional options to `docker run`. WARNING: this variable is usually already set by `init.rc` to mount the `compdb` submodule
 - `$is_interractive` forces the container to start in interractive mode. It takes a `yes`/`no` value, default is `no` if a command is provided.

### Test hello-world(s)

ROSE Docker images are configured with support for C/C++, Fortran, and Binaries.

Let us create a directory anywhere on your filesystem and create a C++ hello-world.

```
mkdir my-test-directory
cd my-test-directory
cat > hello-world.cxx << EOL
#include <iostream>

int main() {
  std::cout << "Hello World !!!" << std::endl;
  return 0;
}
EOL
```

We will use `workspace=.` to tell `rd-run` to mount the current directory as `/workspace` and use workspace as the working directory:
```
workspace=. rd-run ls
workspace=. rd-run pwd
```

First, we check `hello-world.cxx` with GCC:
```
workspace=. rd-run g++ hello-world.cxx -o hello-world-gcc
workspace=. rd-run ./hello-world-gcc
```

Finally, we can compile with ROSE C++:
```
workspace=. rd-run rose-c++ hello-world.cxx -o hello-world-rose
workspace=. rd-run ./hello-world-rose
```

We can look at the generated files from the host.
Specifically, the C++ file unparsed by ROSE, `rose_hello-world.cxx`:
```
#include <iostream>

int main()
{
  (std::cout<<"Hello World !!!") << std::endl;
  return 0;
}
```

## Available Images

We are providing docker images for Ubuntu 16.04, Ubuntu 18.04, and CentOS 7.6.
Associated with each of these distributions are different toolchains corresponding to different compilers (vendor and version) and boost libraries.
There is three type of toolchains:
 - `native`: the default toolchain of the distribution (obtained through the package manager). Simplest case!
 - `hosted`: specific compiler (usually boostrapped using default compiler), boost compiled with that compiler. Use to get edge compiler & boost!
 - `emulated`: two compilers: one to compile ROSE, one to be ROSE's backend. Very specific use case and legacy backend compiler. Not distributed at this time!!!

| `$distrib_name` | `$distrib_version` | `$toolchain` | Description |
| ----------------| ------------------ | ------------ | ----------- |
| `ubuntu` | `16.04`    | `native`                          | Ubuntu 16.04 -- GCC 5.4 and Boost 1.58     |
| `ubuntu` | `18.04`    | `native`                          | Ubuntu 18.04 -- GCC 7.4 and Boost 1.65.1   |
| `centos` | `7.6.1810` | `native`                          | CentOS 7.6 -- GCC 4.8.5 and Boost 1.53     |
| `centos` | `7.6.1810` | `hosted/gcc/6.1.0/boost/1.70.0`   | CentOS 7.6 -- GCC 6.1.0 and Boost 1.70     |
| `ubuntu` | `18.04`    | `hosted/clang/8.0.0/boost/1.70.0` | Ubuntu 18.04 -- Clang 8.0.0 and Boost 1.70 |
| `ubuntu` | `18.04`    | `hosted/gcc/9.1.0/boost/1.70.0`   | Ubuntu 18.04 -- GCC 9.1.0 and Boost 1.70   |
| `ubuntu` | `16.04`    | `hosted/icc/2017u8/boost/1.70.0`  | Ubuntu 16.04 -- ICC 2017 and Boost 1.70    |

The above list shows the images currently distributed for each push on the `release` branch of ROSE.
This list is subject to change (and might get out-of-sync).

The first three are mostly stable but the version of GCC can change when Ubuntu update it (happened recently with Ubuntu 18.04 switching from GCC 7.3 to 7.4).
The nomenclature is not sensitive to GCC update by Ubuntu as it is simply the native toolchain.
We will (probably) retire Ubuntu 16.04 sometime after 20.04 is released.

The hosted toolchain on CentOS corresponds to a slightly more modern compiler.
The compiler itself might be updated.
Boost will be tracking the latest version as long as possible.

The last three are subject to change without notice as they correspond to the edge cases.
They can change either because of a compiler update or because a new Boost release.

### Note about ICC and Licence

For obvious reason, the ICC image is shipped without licence!
To use it, you will need to provide your own licence.
The licence folder is saved in `$licsdir` (after `init.rc`).
The ICC licence file must be called `icc.lic`.

## Do analysis/transformation

We will focus on using ROSE's plugins.

TODO

## Work with applications

TODO

