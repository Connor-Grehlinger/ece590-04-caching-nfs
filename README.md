ECE590-020: Enterprise Storage Architecture -- Fall 2018

Course Project: Network File System with Caching


[Build](https://gitlab.oit.duke.edu/cmg88/ece590-020-storage-project-group3/badges/master/build.svg)

[Coverage](https://gitlab.oit.duke.edu/cmg88/ece590-020-storage-project-group3/badges/master/coverage.svg)

[Coverage Report](https://cmg88.pages.oit.duke.edu/ece590-020-storage-project-group3)




## Compile

### First time setup

This DIY Caching Network Filesystem requires an Ubuntu 18.04 LTS operating system (both client & server-side).
Although two machines are needed for the intended nfs use case, integration testing scripts are provided in the project's integration_test/ directory which allow for running the nfs on a single machine monitoring both client and server-side operations using tmux.

To prepare the project, and install the needed dependencies, begin by performing the following commands in both the intended client and server-side environment (or your single machine if you intend to perform demos & integration tests):

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

Install needed dependencies:

```bash
sudo apt-get install python3 python3-pip meson ninja-build \
git pkg-config libpoco-dev tmux -y
```
Note: Also ensure you have C++ compiler compatible with the C++17 standard


Upon first git clone, `libfuse` , which resides in `fuse-3`, must be properly compiled. This is achieved by

```bash
git clone https://github.com/Connor-Grehlinger/ece590-04-caching-nfs.git
cd ece590-04-caching-nfs/
./prepare.sh
```



### After adding/removing source files or changing compilation flags

Writing a `makefile` manually for a large project can be a pain in the ass. `genmake.py` is a little script that generates `makefile` for us, by finding all source files in the `client_src` and `server_src` directory and reading compilation flags from `makefile.in`.  Ideally, we do not need to touch `genmake.py`.  If files are added/removed or compilation flags are changed, `makefile` needs to be regenerated. This is accomplished by

```bash
python3 ./genmake.py
```

### After editing source files if needed

After editing source files, binaries need to be rebuilt.

```bash
make
```

`makefile` actually comes with two set of configurations: `debug` and `release`. In latter stages, when measuring performance, we might need to compile with `release` configuration. This can be done by

```bash
make config=release
```

## Unit Test

`googletest` is a nice framework for unit testing. Source files of unit tests should be in `utest_src` directory. File `utest_src/example.cpp` illustrates how to write a unit test. Again, if new source files are added, then `./genmake.py` should be run to regenerate `makefile`. Then, unit tests can be built with `make utest` or simply `make`. An executable `build/debug/utest` (or `build/release/utest`, depending on the configuration) will be generated, executing which will run all unit tests.  Typical output will look like the following

```
./build/debug/utest
[==========] Running 2 tests from 1 test case.
[----------] Global test environment set-up.
[----------] 2 tests from example
[ RUN      ] example.test1
[       OK ] example.test1 (0 ms)
[ RUN      ] example.test2
[       OK ] example.test2 (0 ms)
[----------] 2 tests from example (0 ms total)

[----------] Global test environment tear-down
[==========] 2 tests from 1 test case ran. (0 ms total)
[  PASSED  ] 2 tests.
```

It is possible to only run selected tests by passing `--gtest_filter` to `build/debug/utest`. Details can be found by checking help info: `./build/debug/utest --help` or docs. 

In the integration_test/ directory, you'll find the scripts:

- interactive_test.sh (sets up the Caching NFS with client server running on same local machine)
- setup_server.sh (script to run to setup the storage server of the Caching NFS)
- remote_mount.sh (script to set up each client's NFS and enter the shared directory, usage: ./remote_mount.sh <ip-of-storage-server>)

Before using the read & write tests in the shared directory, ensure 'pv' is installed. This program monitors the rate of data as it's piped through with: 
(<stream_output> | pv | <dest>).

```bash
sudo apt-get install pv
```


Before setting up your storage server & clients (or running on single machine), ensure port 55555 is open (the port used for our Caching NFS communication)


