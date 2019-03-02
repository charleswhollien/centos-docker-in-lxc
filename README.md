# centos-docker-in-lxc
Run docker containers in LXC on Centos 7

LXC is a fast way to have a test server up and running. LXC has some security  in place that makes running docker in LXC on centos require a new config (docker-centos.conf). 

# To Use:
Install and configure LXC on Centos 7. 
yum install lxc lxc-extra lxc-templates-y

You will need a brige configured or you can use a macvlan brige. You can update the file /etc/lxc/default.conf with your required network configuration. 

Example macvlan config: 
lxc.network.type = macvlan
lxc.network.macvlan.mode = bridge
lxc.network.flags = up
lxc.network.link = em1

Create a centos container: 
lxc-create -t download -n docker01 -- -d centos -r 7 -a amd64

Place the config in the lxc config directory:
cp docker-centos.conf /usr/share/lxc/config/docker-centos.conf

Create a Centos container:
lxc-create -t download -n docker01 -- -d centos -r 7 -a amd64

Edit the lxc config in: 
/var/lib/lxc/${CONTAINERNAME}/config 

Comment out the centos.common.conf line:
#lxc.include = /usr/share/lxc/config/centos.common.conf

and add a new lxc.include:
lxc.include = /usr/share/lxc/config/docker-centos.conf

Start your container and install docker. 
lxc-start -n docker01 -d
lxc-attach -n docker01
yum install docker -y && systemctl enable docker && systemctl start docker

