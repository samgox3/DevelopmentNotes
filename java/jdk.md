# Check JAVA_HOME
```
sudo update-alternatives --config java
```

## Install JDK
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys B1998361219BD9C9
sudo apt-add-repository 'deb http://repos.azul.com/zulu/deb stable main'

sudo apt update

sudo apt install zulu17-jdk # dbeaver required

sudo apt install zulu11-jdk
sudo apt install zulu8-jdk

```

## Config JAVA_HOME
```
vi ~/.bashrc

# JAVA_HOME
export JAVA_HOME=/usr/lib/jvm/zulu11
export PATH=$JAVA_HOME/bin:$PATH

source ~/.bashrc     

bash
samgox3@ubuntu:~/k8slab/2023-course$ echo $JAVA_HOME

```