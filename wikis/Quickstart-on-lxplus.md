# WARNING: this page is outdated

##Quick start recipe

**NOTE**: this quick start recipe details the procedure for compiling and running tkLayout revision 856 (on Google Code) or greater on **lxplus machines with 64-bit SLC6**.

Some tinkering with library paths, environment variables and/or Makefile options might be needed to successfully compile tkLayout on other architectures/operating systems.

tkLayout requires:
  * ROOT version > 5.34.09
  * BOOST version > 1.55
  * gcc version > 4.7



**1.** All the necessary libraries and compiler versions are already installed on lxplus machines running 64-bit SLC6, thus after logging on, like this:

```

ssh lxplus6.cern.ch```

**2.** Then, you just need to source the following scripts and to set the following variables to setup an appropriate environment:

```

source /afs/cern.ch/sw/lcg/app/releases/ROOT/5.34.09/x86_64-slc6-gcc47-dbg/root/bin/thisroot.sh
source /afs/cern.ch/sw/lcg/contrib/gcc/4.7/x86_64-slc6-gcc47-opt/setup.sh
export LD_LIBRARY_PATH=/afs/cern.ch/sw/lcg/external/Boost/1.55.0_python2.7/x86_64-slc6-gcc47-opt/lib:$LD_LIBRARY_PATH
export BOOST_LIB=/afs/cern.ch/sw/lcg/external/Boost/1.55.0_python2.7/x86_64-slc6-gcc47-opt/lib
export BOOST_INCLUDE=/afs/cern.ch/sw/lcg/external/Boost/1.55.0_python2.7/x86_64-slc6-gcc47-opt/include/boost-1_55
export BOOST_SUFFIX=-gcc47-mt-1_55
```
You can also put those commands on a script and launch the script on startup (i.e. calling it in .bashrc)

**3.** Then, proceed to the checkout of the code:

```

svn checkout http://tkgeometry.googlecode.com/svn/trunk/ tkgeometry
cd tkgeometry```

**4.** Then, check your compiler version by running:

```bash

gcc -v```

**5.** If gcc version is < 4.7, please edit the file Makefile and comment out the following two lines:
```

COMPILERFLAGS+=-Wno-narrowing
COMPILERFLAGS+=-Wno-delete-non-virtual-dtor```

So that they look like this:
```

#COMPILERFLAGS+=-Wno-narrowing
#COMPILERFLAGS+=-Wno-delete-non-virtual-dtor```


**6.** You can finally compile and install tkLayout with the following command:
```

make clean && make -j4 && make install```

**7.** The install process will ask some questions, namely: where to copy the executable, where to store the html output and where to store the "standard output" (xml and some configuration files).
Pay attention to specify **different paths for them than the source code path**, lest the installer fail.

tkLayout stores the installation configuration in the file "~/.tkgeometryrc", so that in case wrong data is entered, one can remove the file and rerun "make install".


**8.** Once tklayout is built and installed, you can see the command line options with:
```

tklayout --help```