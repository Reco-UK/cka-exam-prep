# Storage Classes

## Static Provisioning

When creating volumes with providers such as GCP, it is a requirement that the underlying disk has already been created prior to the volume request.

For example:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  gcePersistentDisk:
    pdName: pd-standard
    fsType: ext4
```

Would expect that:

```shell
gcloud create compute disks --size 2GB --region us-central1 pd-standard 
```

Has been executed prior to making the above request.

## Dynamic Provisioning

We can get around the above requirements by first defining a storage class which will allow Kubernetes to automatically provision
the disk when the claim request is made.

In this scenario the creation of the PV is handed off to the storage class.

There are a number of available storage classes, such as:
  - AWSElasticBlockStore
  - AzureFile
  - AzureDisk
  - CephFS
  - FlexVolume
  - PortworxVolume
  - NFS