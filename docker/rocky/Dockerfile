#rocky linux
FROM docker.io/rockylinux:8

#update
RUN dnf update -y

#install epel
RUN dnf install yum-utils -y && sed -i 's/enabled=0/enabled=1/g' /etc/yum.repos.d/Rocky-PowerTools.repo && dnf install epel-release -y

#add lux repo
RUN rpm -Uvh http://repo.iotti.biz/CentOS/8/noarch/lux-release-8-2.noarch.rpm && dnf repoquery lux

#install packages
RUN dnf install nano wget ghostscript libtiff-tools libtiff-devel -y && dnf group install "Development Tools" -y

#cd /usr/src
#sudo wget https://sourceforge.net/projects/hylafax/files/hylafax/hylafax-7.0.11.tar.gz/download -O /usr/src/hylafax-7.0.11.tar.gz
#sudo tar xzvf /usr/src/hylafax-7.0.11.tar.gz

