# Docker Storage

Docker uses a layered architecture when persisting data.

Any data store in the image layer will be read-only as this layer could be used by multiple containers.

When a container is created a new container layer is created, which allows you to modify files in the running container.

If you modify files which exist within the image layer a method called copy on write is used to create a duplicate of the file in your container layer, which 
allows the original file to remain unchanged in the image layer.

The data in the container layer is only available for the lifetime of the container.

## Storage Drivers

In order for the above implementations to work and data to be stored using a layered architecture, docker uses storage drivers
such as:
 - AUFS
 - ZFS
 - BTRFS
 - Device Mapper
 - Overlay
 - Overlay2

**Docker will choose the available storage driver for the OS it is running on, Ubuntu default is AUFS**

## Volume Drivers

Volume drivers as the name suggests are used to create docker volumes.

The default driver bring local, used to create volumes in the docker volumes directory on the host.

But there are a large number of third party drivers available, such as:
 - Azure File Storage
 - Convoy
 - Flocker
 - gce-docker
 - GlusterFS
 - NetApp
 - RexRay
 - Portworx
 - VMware vSphere Storage

## Volumes

If you require data to persist beyond the lifecycle of a container you can do this using docker volumes.

## Volume mounting

You can mount docker volumes using the following commands:

**If you do not create the volume prior to using it, then docker will create the volume for you under /var/lib/docker/volumes**

```shell
# Create new volume
docker create volumes my-volume

# Use existing volume
docker run -id -v my-volume:/var/lib/mysql mysql

# Use new volume
docker run -id -v my-volume2:/var/lib/mysql mysql
```

## Bind mounts

Bind mount is similar to the above, but it mounts an existing location to host to a path inside the container.

You can do this using the following commands:

```shell
docker run -id -v /data/mysql:/var/lib/mysql mysql
```

The options are shown using the legacy -v option for mounting volumes and directories.

Docker now offers the --mount option, which takes key value pairs as shown below:

```shell
docker run -id --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql
```

# CRI (Container Runtime Interface)

The CRI was introduced to allow kubernetes to work with different container runtimes.

Originally the docker implementation code would be baked in with the Kubernetes source code. 

# CNI (Container Network Interface)

The CNI was created for the same purpose but in relation to network implementations. Providing network providers adhere to the CNI standard then the driver will be compatible with Kubernetes. 

# CSI (Container Storage Interface)

The CSI as above was introduced to create a standard when interfacing Kubernetes with third part storage solutions.

This standard is meant to be universal and work with any container orchestration solution.

When drivers implement the CSI standards the drivers will provide RPC interfaces which the orchestrator can call.