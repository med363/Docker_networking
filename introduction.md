### three type of networks :
### bridge(default network ) in the range 172.17.0.1(docker0)
```bash
docker run ubuntu 
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

