# Docker Networking

When create docker containers you can specify the --network option with the following values:

none - This will mean that the container will not be attached to any network nad therefore inaccessible.
host - This means that there is no isolation between the running container and the host. So if the container is listening on 
port 80 then it will be accessible from the host.

**Be aware that you will not be able to run multiple containers using the host network and the same port as they will clash.**

bridge - With this option an internal network is created on the host. Running containers will then be isolated and connected to the bridge using the steps 
shown within the [namespaces](../namespaces/README.md) README.md.

If the docker containers are exposing a port then you will use docker mapping functionality to expose a port on the host which then maps into the container. This is down using a prerouting nat, as shown in the namespaces section. 