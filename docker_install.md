## Docker installation

<br>
<br>


```sh
# Install docker package
apk add docker

# Add user(s) to the docker group 
addgroup <username> docker

# Make docker start upon reboot and start the service
rc-update add docker default
service docker start

# Add docker compose
apk add docker-cli-compose
```

<br>

[Back to project README](README.md)