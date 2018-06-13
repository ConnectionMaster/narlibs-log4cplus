# Maven NAR packaging for log4cplus

Platform-independent (noarch) NAR packaging for log4cplus.

log4cplus is a simple to use C++11 logging API providing thread-safe, flexible, and arbitrarily granular control 
over log management and configuration. It is modeled after the Java log4j API.

Uplink information :

* https://sourceforge.net/projects/log4cplus/
* https://sourceforge.net/p/log4cplus/wiki/Home/

Using the Maven NAR plugin, you can then simply add NAR dependencies to your build - Log4cplus headers and binaries 
will automatically be integrated in your build.

# Release Notes

## Version 2.0.0

Upstream log4cplus version **2.0.0**

Binaries built on :

* CERN CentOS 7 - gcc 4.8.5, libstdc++ 6, pthread (posix)
* Windows 10 64 bits - MSVC 14 (MS Visual Studio 2015)


# How to use

Simply add the dependency to your Maven NAR like so :

```xml
<dependency>
  <groupId>com.github.cern.narlibs</groupId>
  <artifactId>log4cplus</artifactId>
  <version>2.0.0</version>
  <type>nar</type>
</dependency>
```

Note that Log4cplus v2+ requires C++ 11  (e.g. for GCC you need the option -std=c++11).


# How to rebuild the upstream library

## On Linux / CentOS 7

```shell
git clone --recursive https://github.com/log4cplus/log4cplus.git
cd log4cplus
# Note : we disable SO version to ease relinking
./configure --enable-so-version=no
# If aclocal is reported missing...
autoreconf -f -i
make
cp .libs/liblog4cplus.so ../narlibs-log4cplus/src/nar/resources/aol/amd64-Linux-gpp/lib/
cp .libs/liblog4cplus.so ../narlibs-log4cplus/src/nar/resources/aol/amd64-Linux-gpp/lib/liblog4cplus-nar-2.0.0.so
# Copy all headers into the NAR src/nar/resources/noarch/include
cp -r includes/log4cplus ../narlibs-log4cplus/src/nar/resources/noarch/include
```

## On Windows

* Open Visual Studio 
* Go to subfolder msvc14
* Open the log4cplus solution
* Select the "release" configuration with 64 bits architecture
* Build binaries
* Place resulting .lib, .dll in ../narlibs-log4cplus/src/nar/resources/aol/amd64-Windows-msvc/lib
