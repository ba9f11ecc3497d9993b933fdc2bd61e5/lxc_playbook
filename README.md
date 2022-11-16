# lxc_playbook
- an ansible playbook to deploy lxc on a debian 11 server (systemd 247) to run **rootless containers** easily
- takes care of cpuset and cpu cgroup2 delegation (usually not done by default for rootless containers)
- comes with a wrapper to issue various lxc-commands expose ports, etc

WARNING : USE AN EMPTY MACHINE AS A TARGET TO DEPLOY THIS PLAYBOOK, IT MAY INTERFER WITH YOUR NETWORK, FIREWALL ETC. 

### deploy the playbook
`ansible-playbook -i inventory lxc_setup.yaml`

### use the wrapper script to manage lxc containers
```
Usage: lx-containers

        -lo     --list-os
        -ls     --list-containers
        -cc     --create-container [CONTAINER NAME]
        -sc     --start-container [CONTAINER NAME]
        -hc     --halt-container [CONTAINER NAME]
        -ac     --attach-container [CONTAINER NAME]
        -tc     --tune-container [CONTAINER NAME] [cupset] [examples of values : [ 0 | 0,1 | 0-3 ]] # core 0 or core 0 and 1  or core 0 to 3
                         [CONTAINER NAME] [cpumax] [example of value : 10000 ] # this is 10000 out of 100000 so 10%
                         [CONTAINER NAME] [memmax] [example of value : 200M]
        -Dc     --destroy-container [CONTAINER NAME]
        -dc     --deploy-container [CONTAINER NAME] [CONTAINER IMAGE] [cpuset-value] [cpumax-value] [memmax-value]
        -sn     --shapshot-container [CONTAINER NAME] "description of the snapshot"
        -lsn    --list-shapshot-container [CONTAINER NAME]
        -rsn    --restore-shapshot-container [CONTAINER NAME] [SNAPSHOT NAME]
        -ep     --expose-port [public port] [container ip] [container port] [protocol] # this is only useful for ipv4
        -up     --unexpose-port [public port] [container ip] [container port] [protocol] # this is only useful for ipv4
        -lep    --list-exposed-ports
        -h      --help
```

### the good 
- you can deploy a container with cgroups limits and start it in a single command
```
lx-containers -dc mycontainer "alpine latest" 0 10000 200M
```
- you can get lazy with iptables (the rule will be populated for you)
```
debian@lxc:~$ /usr/local/bin/lx-containers -ep 80 10.0.128.108 80 tcp
sudo iptables -i br0 -t nat -I PREROUTING -p tcp --dport 80 -j DNAT --to 10.0.128.108:80
```

### the bad
```
hacker news will fill this section
```

### the ugly
```
I wrote this playbook+wrapper in bash in the course of a few evenings this week... so yeah
```

### list of [rootless containers](http://uk.lxd.images.canonical.com/) that works out of the box according to me (haven't test all of them / in depth)
```
alpine 3.15 (works fine)
alpine latest (works fine)
amazonlinux latest (ipv6 only, no dns resolution)
centos 9 (stream) (works fine)
debian 10 (works fine)
debian 11 (works fine)
rockylinux 8 (works fine)
rockylinux 9 (works fine)
ubuntu 20.04 (works fine)
ubuntu 22.04 (works fine)
archlinux current (ipv6 connectivity only, must install dhclient to get ipv4 and set default gw manually)
busybox 1.34.1(error metadata missing on tarball)
kali current (works fine)
``` 

P.S : if you have an account with vultr, you can use my other [script](https://github.com/ba9f11ecc3497d9993b933fdc2bd61e5/vultr_api) to deploy a test machine

*** -  this repository is mirrored from gitea ***
