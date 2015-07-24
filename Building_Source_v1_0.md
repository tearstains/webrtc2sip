webrtc2sip v1.0 depends on [Resiprocate](http://www.resiprocate.org/Main_Page) and is **deprecated**.



## Supported systems ##
  * All Linux distributions
  * Windows XP, Vista and 7
  * OS X

## Building the source code ##
The complete guide about how to build [Resiprocate](http://www.resiprocate.org/Main_Page) is available [here](http://www.resiprocate.org/Quick_Subversion_Checkout_and_Compilation_HOWTO) but please note that **SVN [r9737](https://code.google.com/p/webrtc2sip/source/detail?r=9737) is required**.
  1. install required libraries
    * Mac OS X 10.5 (with MacPorts installed)
```
sudo port install openssl db42 patch
```
    * CentOS
```
sudo yum update
sudo yum install make libtool autoconf subversion patch wget g++ libssl-dev libdb4.2++-dev
```
    * Ubuntu (Tested with version **12.04**) / Debian
```
sudo apt-get update
sudo apt-get install make libtool autoconf subversion patch wget g++ libssl-dev libdb++-dev
```
  1. checkout Resiprocate source code **[revision 9737](https://code.google.com/p/webrtc2sip/source/detail?r=9737)**
```
svn checkout -r 9737 http://svn.resiprocate.org/rep/resiprocate/main  resiprocate
```
  1. copy the [resiprocate-deprecated.patch](http://code.google.com/p/webrtc2sip/source/browse/trunk/resiprocate-deprecated.patch) into **resiprocate** folder
```
cd resiprocate
wget http://webrtc2sip.googlecode.com/svn/trunk/resiprocate-deprecated.patch
```
  1. apply the patch
```
 patch -p0 -i ./webrtc2sip.patch
```
  1. prepare makefiles
```
autoreconf --install && ./configure
```
  1. configure for build
```
export PREFIX=/opt/resiprocate
./configure --with-ssl --enable-ipv6 --prefix=$PREFIX
cp -f config.h resip/stack/config.hxx
```
  1. build and install
```
make clean && make && make install
cp -f ./repro/repro.config $PREFIX/sbin/repro.config
```
  1. open **$PREFIX/sbin/repro.config** and set the port number (e.g. **4062**) to bind to for incoming websocket connections
```
# Local port to listen on for SIP messages over WS (WebSocket) - 0 to disable
WSPort = 4062
```
  1. Run the sever
```
cd /opt/resiprocate/sbin
./repro
```

### Known build issues ###
  * ../repro/BerkeleyDb.hxx:8:19: fatal error: db\_cxx.h: No such file or directory
    1. Find BerkeleyDB package for c++ dev
```
sudo apt-cache search libdb
```
    1. select from the result a package named **libdb(x).(y)++-dev** where **(x).(y)** is the version (e.g. **libdb4.2++-dev**) and install it
```
sudo apt-get install libdb4.2++-dev
```

## Testing the gateway ##
  1. open [http://sipml5.org/call.htm](http://sipml5.org/call.htm) in your browser and press on **Expert mode?** button
  1. fill _WebSocket Server URL_ field with the IP address and port where your webrtc2sip gateway listen for incoming connections (e.g **ws://192.168.0.1:4062**). IMPORTANT:Do not forget the the url scheme (**ws://**)