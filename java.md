## Install Oracle Java 8 in ubuntu 18.04

Download it from this site: https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

* Install 
```
sudo tar xvf ~/Downloads/jdk-8u241-linux-x64.tar.gz --directory /usr/lib/jvm/
sudo ln -s /usr/lib/jvm/jdk1.8.0_241 /usr/lib/jvm/java-8-oracle
``` 
* Setup 
```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8-oracle/bin/java 1
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-8-oracle/bin/javac 1
sudo update-alternatives --install /usr/bin/javaws javaws /usr/lib/jvm/java-8-oracle/bin/javaws 1 
sudo update-alternatives --install /usr/bin/jcontrol jcontrol /usr/lib/jvm/java-8-oracle/bin/jcontrol 1
sudo update-alternatives --config java
sudo update-alternatives --config javac
sudo update-alternatives --config javaws
sudo update-alternatives --config jcontrol
``` 

## Permit olds algorithms to load iDRAC console 

Edit this file:
```
sudo vim /usr/lib/jvm/java-8-oracle/jre/lib/security/java.security
```

Looking for the options `jdk.tls.disabledAlgorithms` options and clear it, like this
```
jdk.tls.disabledAlgorithms=""
jdk.jar.disabledAlgorithms=""
```

Add the IP address in the exception:
```
jcontrol
```

To use:
```
javaws '/home/jenciso/Downloads/viewer.jnlp(10.64.8.155@0@1580158924760)'
``` 


References:

https://antoniocampos.net/2018/01/12/java-erro-ao-utilizar-md5withrsa/

