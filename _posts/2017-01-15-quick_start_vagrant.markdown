---
layout: post
title: "Vagrant로 쉽고 빠른 가상머신 만들기"
date: 2017-01-15 14:08:00 +0900
categories: linux
---

Vagrant는 Virtualbox, Vmware 등 과 같은 가상환경을 만들어주는 소프트웨어를 이용하여 쉽고 빠르게 가상머신을 생성하여 개발 환경을 구축할 수 있게 해주는 도구입니다.
Vagrant 파일을 통해 VM 리소스 설정을 쉽게 할 수 있습니다.

# QuickStart

## 1.Prerequisite
Vagrant 를 이용하기 위해서는 사전에 가상환경을 만들어주는 소프트웨어가 설치되어있어야한다. 가장 많이 쓰이는 Virtualbox 를 먼저 설치한 후 아래 내용을 진행한다.

## 2. Vagrant 설치

Vagrant 는 Windows, Linux, MacOS 모든 운영체제를 지원하고 있습니다. [여기](https://www.vagrantup.com/downloads.html)를 통해 Vagrant 설치 파일을 다운로드 받을 수 있습니다.

## 3. Vagrant 시작하기
### Vagrant Box 검색
vagrant box 란 vagrant 에서 사용할 가상머신 이미지를 의미한다. [여기](https://atlas.hashicorp.com/boxes/search)에서 원하는 가상머신 이미지를 검색할 수 있다.

ubuntu 16.04 를 사용하고 싶다면 'ubuntu/xenial64' 를 검색하며 된다. 해당 box 는 [여기](https://atlas.hashicorp.com/ubuntu/boxes/xenial4)에서 확인할 수 있다.

### Vagrantfile 생성
```vagrant init``` 는 원하는 box 로 Vagrantfile 을 생성하는 명령어이다. ```Vagrantfile``` 은 vagrant 가 가상머신을 생성하기 위한 여러가지 설정을 명시한 파일이다. 아래 명령어를 통해 ubuntu/xenial64 box를 사용하는 Vagrantfile 을 생성한다.

```
vagrant init ubuntu/xenial64
```

위 명령어를 통해 생성된 ```Vagrantfile``` 일부를 살펴보면 다음과 같다.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"
```

```config.vm.box``` 에 우리가 원하는 box 이름이 들어간 것을 확인할 수 있다.

### 가상머신 시작하기
vagrant 로 가상머신을 시작하려면 반드시 명령어를 실행하는 위치에 Vagrantfile 이 존재해야한다. 실행은 아래 명령어로 간단하게 할 수 있다.

```
vagrant up
```

명령어를 실행하면 아래와 같은 메세지가 나오면서 가상머신이 이미지를 다운로드 받고 실행시킨다.

```
nexusz99@trinity:~/work/nhnent/blog$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Checking if box 'ubuntu/xenial64' is up to date...
==> default: A newer version of the box 'ubuntu/xenial64' is available! You currently
==> default: have version '20161213.0.0'. The latest is version '20170113.0.0'. Run
==> default: `vagrant box update` to update.
==> default: Clearing any previously set forwarded ports...
==> default: Fixed port collision for 22 => 2222. Now on port 2200.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 4000 (guest) => 64000 (host) (adapter 1)
    default: 22 (guest) => 2200 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2200
    default: SSH username: ubuntu
    default: SSH auth method: password
    default: Warning: Remote connection disconnect. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /home/nexusz99/work/nhnent/blog
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run.
```

### 가상머신 사용하기
생성된 가상머신에 들어가는 방법은 ssh 가 대표적이다. 아래 명령어를 통해 생성된 가상머신에 접속할 수 있다.

```
$ vagrant ssh

---- EXAMPLE ----
nexusz99@trinity:~/work/nhnent/blog$ vagrant ssh
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-59-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

39 packages can be updated.
0 updates are security updates.


Last login: Sun Jan 15 08:43:34 2017 from 10.0.2.2
ubuntu@ubuntu-xenial:~$
```

----

## 4. 마무리
지금까지 vagrant 로 빠르게 가상머신을 생성하고 이용하는 방법에 대해 알아보았다. vagrant 를 이용하면 매번 가상머신을 생성하고 운영체제를 설치할 필요없이 빠르게 새로운 환경을 구축할 수 있으며, Vagrantfile 에서 설정을 통해 port forwarding 등 다양하게 설정하면서 이용할 수 있다.