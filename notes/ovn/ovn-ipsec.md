# OVN IPSec for east-west traffic

This is an OCP 4.7 feature. With OCP 4.7 IPSec for east-west traffic is a day-1 configuraiton. With OCP 4.8 this can also be applied as a day-2 configuration.

- Setup `install-config.yaml` to use IPSec

```yaml
...
networking:
  networkType: OVNKubernetes
...
```

- Generate installation manifests and inject custom manifest `manifest/manifest_cluster-network-03-config.yaml` with the following content
```yaml
apiVersion: operator.openshift.io/v1
kind: Network
metadata:
  creationTimestamp: null
  name: cluster
spec:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  externalIP:
    policy: {}
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
  defaultNetwork:
    type: OVNKubernetes
    ovnKubernetesConfig:
      ipsecConfig: {}
```
