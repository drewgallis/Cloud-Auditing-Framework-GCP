## Installing Docker (local machine):
=====================

### Script only works if sudo caches the password for a few minutes:

`sudo true`


`sudo apt-get update`


### Alternatively you can use the official docker install script

`wget -qO- https://get.docker.com/ | sh`

### Install docker-compose

``COMPOSE_VERSION=`git ls-remote https://github.com/docker/compose | grep refs/tags | grep -oP "[0-9]+\.[0-9][0-9]+\.[0-9]+$" | tail -n 1``

``sudo sh -c "curl -L https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose"``

`sudo chmod +x /usr/local/bin/docker-compose`

`sudo sh -c "curl -L https://raw.githubusercontent.com/docker/compose/${COMPOSE_VERSION}/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose"]`

### Install docker-cleanup command (good for cleaning up your messy docker listings)

`cd /tmp`

`git clone https://gist.github.com/76b450a0c986e576e98b.git`

`cd 76b450a0c986e576e98b`

`sudo mv docker-cleanup /usr/local/bin/docker-cleanup`

`sudo chmod +x /usr/local/bin/docker-cleanup`

[Back to home](../README.md)

[On to the next step](gks_setup.md)