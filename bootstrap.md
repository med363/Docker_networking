### show network
```bash
docker network ls
```
### create network
```bash
docker network create -d bridge 
```
### create container 1
```bash
docker run -d -p 8080:80 --name srv nginx:alpine
```
### show  all information about container 1
```bash
docker inspecte srv
```
### define network in other container
```bash
docker run -d -p 8080:80 --name srv nginx:alpine
```
