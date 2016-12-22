# Docker

Install Docker On Ubuntu 14.04

Prerequisite
1) Minimum kernel version
uname -r
3.11.0-15-generic
2) Update package information, ensure that APT works with the https method, and that CA certificates are installed.
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates
3) Add the new GPG key.
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
4) Open the /etc/apt/sources.list.d/docker.list file in your favorite editor. If the file doesn’t exist, create it.
sudo vi /etc/apt/sources.list.d/docker.list
5) Remove any existing entries from the file.
6) Add an entry for your Ubuntu operating system. On Ubuntu Trusty 14.04 (LTS)
deb https://apt.dockerproject.org/repo ubuntu-trusty main
7) Save and close the /etc/apt/sources.list.d/docker.list file.
8) Update the APT package index.
 sudo apt-get update
9) Purge the old repo if it exists.
sudo apt-get purge lxc-docker
10) Verify that APT is pulling from the right repository.
 apt-cache policy docker-engine
11) It is recommended to install the linux-image-extra kernel package. The linux-image-extra package allows you use the aufs storage driver.
 sudo apt-get update
 sudo apt-get install linux-image-extra-$(uname -r)
 sudo apt-get install apparmor
Docker Install
1) Update your APT package index.
sudo apt-get update
2) Install Docker.
sudo apt-get install docker-engine
3) Start the docker daemon.
sudo service docker start
4) Verify docker is installed correctly.
sudo docker run hello-world
Useful extras
1) Run docker without sudo
sudo groupadd docker
sudo usermod -aG docker romil
Logout and login
2) If the DNS is not working inside the container, configure it manually.
sudo gedit /etc/NetworkManager/NetworkManager.conf
comment out the following line:
#dns=dnsmasq
sudo restart network-manager
Now the /etc/resolv.conf should have correct dns addresses. Use these to configure the docker DNS
 sudo vi /etc/default/docker
Uncomment and add these:
DOCKER_OPTS="--dns 10.210.225.1 --dns 10.210.225.2 --dns 8.8.8.8 --dns 8.8.4.4"
Restart the docker service:
sudo service docker restart
3) Install docker-compose
sudo pip install docker-compose
Reference: https://docs.docker.com/engine/installation/linux/ubuntulinux/
Useful commands
It is easier to configure a Dockerfile for any build related commands and use docker-compose and configure docker-compose.yml version 2 for managing networking , volumes and services. I prefer to use that instead of docker command line commands. Refer to any docker project in the gitlab for sample.
1) System wide docker information
docker info
2) List docker images
docker images
3) List current active containers. Imagine Container as a process.
docker ps
4) List all containers
docker ps -a
5) Run a container in interactive mode -i , attach terminal -t, and execute bash. Image hash can be any unique start letters of hash or full hash
docker run -i -t <image_hash> /bin/bash
6) Build image from Dockerfile. Command executed from the current directory of Dockerfile
docker build .
Build image with tag(s)
docker build -t <reponame:tag> -t <reponame:lastest>
docker build -t kromil/hadoop:v1.0 -t kromil/hadoop:latest
7) Kill a running container.
docker kill <container_id>
8) Remove a container
docker rm <container_id>
9) Remove all containers
 docker rm $(docker ps -a -q)
10) Remove a image
docker rmi <image_id>
11) Remove all images
docker rmi $(docker images -q)
12) Open terminal on existing running container
docker exec -it <container_id> /bin/bash
13) Execute a container in detached mode. The executed command in the container shouldn't exit after execution, else it will cause container to stop.
docker run -d <image_hash> <startup_script_or_command_to_execute>
14) Start , stop or restart container
docker start <container_id>
docker stop <container_id>
docker restart <container_id>
15) It is easier to configure network and assign static ip, hostname , add entries to /etc/hosts for cluster etc using docker-compose.yml . However for some quick tests it can done from docker command line.
Create your own network to specify static ip address:
docker network create --subnet=172.18.0.0/16 <mynetworkname>
Run the container assigning hostname, network, static ip :
docker run -h <myhostname> --net <mynetworkname> --ip <myIPaddress> -i -t <imageid_hash> /bin/bash
docker-compose commands
16) Build using docker-compose
docker-compose build
17) Create container and execute using docker-compose
docker-compose up -d
18) Stop, start and restart containers defined in config file without removing them
docker-compose stop
docker-compose start
docker-compose restart
19) Execute any commands in container from outside(host) without getting in. Look at processes running inside a container or tail a log file.
 docker exec -it <container_id> ps -ef
 docker exec -it <container_id> tail -f <logfilenameincontainer>
References: https://docs.docker.com/engine/reference/commandline/
Debugging:
1) ERROR: cannot create network, conflicts with existing network, networks have overlapping IPv4.
Remove the existing containers which are currently referencing the network.
Turn the bridge down: sudo ip link set <bridge_name> down
Delete the bridge: sudo brctl delbr <bridge_name>
List the networks: docker network ls
Remove the bridge network: docker network rm <bridge_name>
