# Custom Kernel Modules

## Extracting Kernel Modules RPMs

When building out-of-tree Kernel modules, the following procedure can be used to extract the Kernel headers and kernnel modules RPMs from the OpenShift distribution

```bash
RELEASE_IMAGE=$( oc adm release info --image-for=machine-os-content  quay.io/openshift-release-dev/ocp-release:4.6.16-x86_64 )

echo $RELEASE_IMAGE
quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:09e3228d294d07940efebc06301179cf376f50ca16efe21ba0e343ab12ffa131

podman pull --authfile pull-secret.json $RELEASE_IMAGE
# run and login into the container
podman run -it --entrypoint /bin/bash $RELEASE_IMAGE
# run container and detach
podman run -dit --entrypoint /bin/bash $RELEASE_IMAGE

# Find the container ID
podman ps | grep $RELEASE_IMAGE
# List available kernels 
podman exec -ti <container-id> bash -c "ls /extensions/kernel*"

podman cp <container-id>:/extensions/kernel-rt-core-4.18.0-193.41.1.rt13.91.el8_2.x86_64.rpm   .
podman cp <container-id>:/extensions/kernel-rt-devel-4.18.0-193.41.1.rt13.91.el8_2.x86_64.rpm  .
```

