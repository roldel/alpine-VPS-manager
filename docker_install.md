## Docker installation

<br>
<br>


```sh

apk add docker


### adjust username
addgroup $(whoami) docker

rc-update add docker default
service docker start

# Add docker compose
apk add docker-cli-compose


```

<br>

[Back to project README](README.md)