# RWN Nodes Design

## Configurations

- Create MachineConfigPool (MCP) for RWN

```yaml
---
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfigPool
metadata:
  name: rwn
  labels:
    machineconfiguration.openshift.io/role: rwn
spec:
  machineConfigSelector:
    matchExpressions:
      - {key: machineconfiguration.openshift.io/role, operator: In, values: [worker,rwn]}
  nodeSelector:
    matchLabels:
      node-role.kubernetes.io/rwn: ""
```

- Create MachineConfig (MC) to remove IPI leftovers for RWN nodes

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  name: 50-worker-fix-ipi-rwn
  labels:
    machineconfiguration.openshift.io/role: rwn
spec:
  config:
    ignition:
      version: 3.1.0
    storage:
      files:
      - contents:
          source: data:,
          verification: {}
        filesystem: root
        mode: 420
        path: /etc/kubernetes/manifests/keepalived.yaml
      - contents:
          source: data:,
          verification: {}
        filesystem: root
        mode: 420
        path: /etc/kubernetes/manifests/mdns-publisher.yaml
      - contents:
          source: data:,
          verification: {}
        filesystem: root
        mode: 420
        path: /etc/kubernetes/manifests/coredns.yaml
    systemd:
      units:
      - contents: |
          [Unit]
          Description=Writes IP address configuration so that kubelet and crio services select a valid node IP
          Wants=network-online.target
          After=network-online.target ignition-firstboot-complete.service
          Before=kubelet.service crio.service
          [Service]
          Type=oneshot
          ExecStart=/bin/bash -c "exit 0 "
          [Install]
          WantedBy=multi-user.target
        enabled: true
        name: nodeip-configuration.service
```

- Apply RWN role to node and remove `worker` role to avoid regular platform services running on it.

```bash
oc label node <node-name> node-role.kubernetes.io/rwn=""  node-role.kubernetes.io/worker-
```

## Caveats

- The MTU configuration of the CNI must be the lowest MTU among the nodes
- Nodes must have direct reachability to control plane and all the other nodes
