# cka-exam-prep - Security Contexts

When using docker you can specify security standards to be used by the running container i.e. user id etc

This can also be done within Kubernetes using security contexts, but can be configured at either a container or pod level.

If defined at a pod level it will take effect on all running containers within the pod.

Settings defined at a container level will override the settings defined at the pod level.


