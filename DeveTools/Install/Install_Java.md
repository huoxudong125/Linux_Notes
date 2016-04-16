#Install Java

##Download


[jdk-8u77-linux-x64.tar.gz](http://download.oracle.com/otn-pub/java/jdk/8u77-b03/jdk-8u77-linux-x64.tar.gz)


##Unzip using tar

```
>tar -zvxf jdk-8u77-linux-x64.tar.gz
```
the command will generate the folder:
```
>ls 
jdk1.8.0_77 jdk-8u77-linux-x64.tar.gz ...
```
then I move the 

##Enviroment settings

###Change the user to `root`,using

```
>sudo su - root
```
###Eidt the `/etc/profile` using vi

```
>vi /etc/profile
```

and then insert the follow code: 

```
#jdk home by HQF
JAVA_HOME=/home/Tools/jdk1.8.0_77
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export PATH
export CLASSPATH

```

then make the profile 

>Note:
>`Source /etc/profile`



##Test the result

```
> java -version
```

```
>javac

```
