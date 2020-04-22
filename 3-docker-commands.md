# Docker Commands hands on

## Docker Networking
1. Create Bridge network. ` docker network create demo-bridge`
2. `docker pull ubuntu`
3. Default bridge `docker run -d -i --rm --name ubuntu ubuntu`
4. `docker run -d -i --rm --network demo-bridge --name ubuntu ubuntu`

## multi containers interaction
1. `docker run -d -i --rm --network demo-bridge --name server ubuntu`
2. `docker run -d -i --rm --network demo-bridge --name client ubuntu`
3. Interaction with host `docker run -d -i --rm --network demo-bridge -p 8888:8888 --name server ubuntu`
4. docker exec into the server and netcat to listen on port 8888 `nc -l -p 8888`
5. docker exec into the client and send message to server `nc server 8888`
6. telnet from a host machine. `telnet localhost 8888`

## Docker Volumes
1. Create Volume. `docker volume create demo-vol`
2. Mount on container `docker run -d -i --rm --network demo-bridge -v demo-vol:/app c1 ubuntu`
3. Access volume from another container: `docker run -d -i --rm --network demo-bridge -v demo-vol:/app c2 ubuntu`
