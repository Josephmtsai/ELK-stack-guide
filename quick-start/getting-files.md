# Preparation
## Download file

* [https://www.elastic.co/downloads/elasticsearch](https://www.elastic.co/downloads/elasticsearch)
* [https://www.elastic.co/downloads/kibana](https://www.elastic.co/downloads/kibana)
* [https://www.elastic.co/downloads/logstash](https://www.elastic.co/downloads/logstash)

## Using sftp to upload file

Connect from server  
![](Image 4.png)

> upload file to tmp folder /tmp
>
> > switch to local disk folder
> >
> > ```
> > lcd elk
> > ```
> >
> > create a remote folder to temp use
> >
> > ```
> > mkdir /tmp
> > ```
> >
> > switch to remote linux folder, if exist
> >
> > ```
> > cd /tmp
> > put elasticsearch-5.0.0.rpm
> > put kibana-5.0.0-x86_64.rpm
> > put logstash-5.0.0.rpm
> > ```
> >
> > ![](Image 5.png)  
> > ![](1.png)

## Install RPM Package \(If not install\)

Before using any RPM packages, you must know where to find them. An Internet search returns many RPM repositories, but if you are looking for RPM packages built by Red Hat, they can be found at the following locations:

The Red Hat Enterprise Linux CD-ROMs

The Red Hat Errata Page available at [http://www.redhat.com/apps/support/errata/](http://www.redhat.com/apps/support/errata/)

Red Hat Network — Refer to Chapter 12, Red Hat Network for more details on Red Hat Network.

10.2.2. Installing

RPM packages typically have file names like foo-1.0-1.i386.rpm. The file name includes the package name \(foo\), version \(1.0\), release \(1\), and architecture \(i386\). To install a package, log in as root and type the following command at a shell prompt:

```
rpm -Uvh foo-1.0-1.i386.rpm
```

## Install JAVA \(If not install\)

1. Please check with rpm is install if not    
2. Please upload file as Before previous Step

Download Java JDK RPM stable version from official site:  
[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)  
Elasticsearch and Logstash base on Java, need to install Java JDK first.

> > -ivh  v:察看更細部的安裝資訊畫面，h:以安裝資訊列顯示安裝進度  
> > -Uvh    後面接的軟體即使沒有安裝過，則系統將予以直接安裝； 若後面接的軟體有安裝過舊版，則系統自動更新至新版；  
> > -Fvh    如果後面接的軟體並未安裝到你的 Linux 系統上，則該軟體不會被安裝；亦即只有已安裝至你 Linux 系統內的軟體會被『升級』！

```
cd /tmp
rpm -ivh jdk-8u101-linux-x64.rpm
```



