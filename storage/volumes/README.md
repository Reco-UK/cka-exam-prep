# Kubernetes Volumes

## Volumes

We can also create volumes when running pods within Kubernetes. To do so, you will need supply 
volumes and volumesMounts declarations within your container Pod manifest file.

The volumes section of the manifest allows you to define volumes which map to local or external storage.

You will then use volumeMounts to attach that volume to a location within the running container.

Volume mounts can either use *hostPath* which maps to a location on the host or use other third party implementations.

*hostPath* is not suggested when running your pods across multiple nodes as the volume create will be unique to the node in question and not shared 
across all nodes.

## Persistent Volumes

Persistent Volumes allow you to allow you to create a large pool of storage, usually allocated by the system admins, which can be carved up between the running processes using claims.

## Persistent Volume Claims

As mentioned above we can define pools of storage, but in order to utilise this we have to create a persistent volume claim which binds the process making the request to the underlying storage, this is a one-to-one association.

When the claim is made, Kubernetes will check the Capacity, Access Modes, Volume Modes and Storage Class to verify is the correct PV is available for the claim.

When multiples are available a selector can be used to specify which PV is used.

**Please note that if there are no available PV which match the claims requirements exactly, then the next available PV can be used and can result in small claims being
assigned to large PVs resulting in wasted storage. Any claims which to not have any match will remain in a pending state.**

By default, persistent volume reclaim policy is set to retain PVs even when the claim for that PV has been deleted. Although the PV remains it is no longer available for use.

You can also set Delete to remove the PV and reclaim space or Recycle to allow it to be re-used.

Claims are bound to the pods by defining *persistentVolumeClaim* under the volumes block of the specification.