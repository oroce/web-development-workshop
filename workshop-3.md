title: Web development practices
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
.bottom-bar[
  {{title}}
]

---

class: impact

# {{title}}
## Local development and deployment

---
class: center, middle

![](https://cdn.meme.am/cache/instances/folder588/500x/64027588.jpg)

---

## We should have an environment which is:

* repeatable
* beginner friendly
* easy to configure

---

# Vagrant

* DRY (development) infrastructure
* can be used with docker, virtualbox, vmware or even cloud
* configured via Vagrantfile (yaml)

--

- installing vagrant: [https://www.vagrantup.com/](https://www.vagrantup.com/)
--

- start a new terminal

---

# A basic `Vagrantfile`

```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.synced_folder ".", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo "we provisioned our own server" >HELLO_DOGFISH
  SHELL
end
```

---

# Start the server

## `vagrant up`

---

# Connect to the server

## `vagrant ssh`

---

# Can we make the `koa-api` run in vagrant?

---

# [oroce/koa-api#Vagrantfile](https://github.com/oroce/koa-api/blob/master/Vagrantfile)

---

# We have stable local environment, let's put it onto AWS Beanstalk

---

# Preparation

1. Get an AWS account
2. Get AWS Access Key and AWS Secret Key
3. Place it into `./aws/credentials` on your machine
4. Install [`awsebcli`](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-osx.html)
5. Create a new environment on Beanstalk

---

# `eb init`

---
class: middle, center

# We'll end up having a `.elasticbeanstalk` directory

---

`eb deploy`

???
hopefully, it works :)
---

# Deploy via bitrise

---

# A bitrise config: [oroce/koa-api#bitrise.yml](https://github.com/oroce/koa-api/blob/master/bitrise.yml)

---

# Docker

---

## Container technology, think of like a prebuilt app/module/image/virtualbox file which aims to be:

* fast
* mobile
* repeatable
* easily composable with other images

---

# You need a mysql server?

## That's easy.

---

```
docker run --name my-mysql-container -e MYSQL_ROOT_PASSWORD=root -d -p 3306 mysql/mysql-server
```

* `--name`: name of the image (you can reference the container using this name)
* `-e MYSQL_ROOT_PASSWORD=root`: the mysql password
* `-d`: you can use `--detach` as well, run the image in the background
* `-p 3306`: publish to the host operating this port
* `mysql/mysql-server`: name of the image to be run
---

# A node app: [dockerhub](https://hub.docker.com/_/node/)

```
docker run -it --rm node:8.1 /bin/bash
```

* `-i`: interactive run
* `-t`: allocate pseudo tty
* `--rm`: remove the container once we close the tty
* `node:8.1`: image name followed by tag name
* `/bin/bash`: the command to run, otherwise the [default command is invoked](https://github.com/nodejs/docker-node/blob/12ba2e5432cd50037b6c0cf53464b5063b028227/8.1/Dockerfile#L53) (`node`)

---

# You can even mix vagrant and docker

## using docker provider with vagrant

* [oroce/koa-api#Vagrantfile](https://github.com/oroce/koa-api/blob/chore/docker-in-vagrant/Vagrantfile)
* [oroce/koa-api#Dockerfile](https://github.com/oroce/koa-api/blob/chore/docker-in-vagrant/Dockerfile)

---
class: middle, center

# `vagrant up`

---

![](https://cdn.meme.am/cache/instances/folder124/500x/39737124/dwight-from-the-office-keep-your-fingers-crossed.jpg)

---
class: middle,center

```
curl localhost:3000/astronauts
```
---

# To see the logs

`docker vagrant docker-logs --follow`

---
# Since SSH is not enabled, to run a command you need to issue:

```
vagrant docker-exec -it -- /bin/bash
```

--

## which does not work for me

???
and not even for others: [wkolean/vagrant-docker-exec#4](https://github.com/wkolean/vagrant-docker-exec/issues/4)
---

# Luckily it's just docker

```
docker ps | grep koa-api
```

or

```
docker ps --filter name=koa-api
```

---

# Get into that container

```
docker exec -it koa-api-example_default_1498407798 /bin/bash
```

---

# When it's about deployment you have multiple options

* managed deployment (like AWS Beanstalk)

--

> great for small apps with decent traffic

--

* managed docker deployment (like AWS Beanstalk)

--

> great for smaller teams with fairly complex use cases and decent traffic

--

* more complex/self managed docker deployment (kubernetes)

--

> for microservices (my suggestion is 10+ services)

--

* self managed hosts (orchestration)
--

> great for hosts behind firewall

> complex use cases

> not allowed to use external services (eg government)

---
class: middle
# Self managed solution

* ansible
* chef
* puppet

---

# My favourite is ansible

* manages hosts via SSH (no agent)
* powered by python, but you don't touch python code
* yaml based configuration
* can be used for repeatable and ad hoc tasks

---

# and the best reason to use ansible: it's result oriented

```
- name: install package
  apt:
    pkg: nodejs
    state: installed
```
???
It's not `result oriented`, but I don't know how it's called

---

# Ansible

## playbooks
> tasks to execute

## handlers
> hooks to be executed if a task failed or succeeded

## inventory
> list of hosts where the playbooks should be executed (can be dynamically obtained from API, like from AWS)
---

# An example where ansible is used inside vagrant to provision a node app

## [geerlingguy/ansible-vagrant-examples](https://github.com/geerlingguy/ansible-vagrant-examples/tree/master/nodejs)

---
class: middle, center

* You can use ansible to provision docker
* You can use ansible to provision vagrant
* You can use docker in vagrant
* You can use ansible to provision docker in vagrant
* You can use ansible to manage kubernetes which manages dockers
* You can use ansible to manage Google Cloud/DigitalOcean/Linode/AWS cloud servers where you host your own node app

---

# For the future

* Kubernetes
* Serverless solutions (both AWS, Google Cloud offer such solutions)
