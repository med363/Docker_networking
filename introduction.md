### macvlan connect connet container to phy network
```bash
docker network create -d macvlan --subnet 10.7.1.0/24 --gateway 10.7.1.3 -o parent=enp0s3 newasgard
``` 
### attach container
```bash
docker run -itd --rm --network newagard --ip 10.7.1.92 --name thor busybox
```
### check this ip in container
```bash
docker exec -it thor sh
```
### inside container
```bash
ip address show
```
### Rq:Promiscuous mode est un mode d'opération pour les interfaces réseau qui permet à une carte réseau de recevoir tous les paquets transmis sur le réseau, plutôt que seulement les paquets destinés à sa propre adresse MAC. Ce mode est souvent utilisé pour l'analyse de réseau et la surveillance de la sécurité.
```bash 
sudo ip link set enp0s3 promisc on
```
### check this ip in container
```bash
docker exec -it thor sh
```
### inside container
```bash
ip address show
```
### first mode of macvlan docker can create sub_interface rx(layer 2)
```bash
docker network create -d macvlan --subnet 10.7.1.0/24 --gateway 10.7.1.3 -o parent=enp0s3.20 newasgard2
```
### check it
```bash
ip a
```
### second mode create ipvlan allo host share mac address with containers(layer 3)
```bash
docker network create -d ipvlan --subnet 10.7.1.0/24 --gateway 10.7.1.3 -o parent=enp0s3 newasgard3
```
### attach container
```bash
docker run -itd --rm --network newagard3 --ip 10.7.1.92 --name thor busybox
```
### check this ip in container
```bash
docker exec -it thor sh
```
### inside container
```bash
ip address show
```
### ping to it in my machine
```bash
ping 10.7.1.92
```
### if i show arp table i will check that mac adress is the same with docker host
```bash
arp -a 10.7.1.92
```
### RQ:if we assigne tow rx (ipvlan) 192.168.10.0/24 and 192.168.20.0/24 we show interface of docker hosh such as a router , no brd traffic and we can not talk with any one  
```bash
docker network create -d ipvlan --subnet 192.168.10.0/24 -o parent=enp0s3 -o ipvlan_mode=l3 --subnet 192.168.20.0/24  amine1
```
### attach containers on 192.168.10.0/24
```bash
docker run -itd --rm --network amine1 --ip 192.168.10.2 --name thor busybox
```
```bash
docker run -itd --rm --network amine1 --ip 192.168.10.3 --name loki busybox
```
### attach containers on 192.168.20.0/24
```bash
docker run -itd --rm --network amine1 --ip 192.168.20.2 --name thor1 busybox
```
```bash
docker run -itd --rm --network amine1 --ip 192.168.20.3 --name loki1 busybox
```
### check network
```bash
docker inspect amine1
```
### check this ip in container
```bash
docker exec -it thor sh
```
### inside container
```bash
ip address show
```





### three type of networks(drive) :
### bridge(default network ) in the range 172.17.0.1(docker0) 
### you can type 
```bash
brctl show
```
### or type
```bash
bridge link
```
```bash
docker run ubuntu 
```
### we would to create bridge network
```bash
sudo docker network create asgard
```
### attach this container to network
```bash
docker run -itd --rm --network asgard --name loki busybox
```
```bash
docker exec -it loki sh
```
### none no acces to container
```bash
docker run ubuntu --network=none
``` 
###  host isolation between docker host(vm) and container 
```bash
docker run ubuntu --network=host
``` 
### if multiple bridge containers run in different host => overlay network(10.0.9.0/24) create internel network that pass across all the nodes paticipate in the swarm cluster
```bash
docker network create --driver overlay --subnet 10.0.9.0/24 my-overlay-network
```
### attach containers with service (replicat)
```bash
docker service create --replicas 2 --network my-overlay-network nginx
```
### 1. if we have web run in container mapped in port 80 
```bash
docker run -p 8000:80 my-web-srv nginx
```
### but if we run multiple containers in docker swarm and we hope to mapped port So,docker swarm create ingress network build load balancer that redirect traffic from the publish port to internal port in container  
### 2. we multiple container
```bash
docker service create --replicat=2 -p 8000:80 my-web-srv nginx
```
### we know how ingress network work when we run multiple node in docker swarm 
### if we have three nodes in the docker swarm cluster runnig two instancce of web srv runnig in some port or any user able to access to any instance since thiere are part in the same cluster without ingress network we can acess in the two nodes but not the third node because no instance run in the third node while ingress is type of overlay network forward traffic for the respective instance on any  nodes essecially creating "routing mesh"==>Routing Mesh  helps in routing the user traffic that is recevied on a node that isn't even running an instance of the srv to other nodes where the instances are actually running and again all of this is the default behavior of docker swarm and you don't need to do any additional configurations.  
### Rq: docker have embedded DNS

