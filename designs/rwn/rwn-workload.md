# Worklaods Configurations for RWN

- Protecting running applications on RWN: Use Pod tolerations to mitigate the effects on Pods running on RWN after the control plane sets `node.kubernetes.io/unreachable` taint to `NoExecute` when it cannot reach a node and the `pod-eviction-timeout` has expired.

```yaml
spec:
    ...
    tolerations:
        - key: "node.kubernetes.io/unreachable"
          operator: "Exists"
          effect: "NoExecute"
        - key: "node.kubernetes.io/not-ready"
          operator: "Exists"
          effect: "NoExecute"
        - key: "node.kubernetes.io/unschedulable"
          operator: "Exists"
          effect: "NoExecute"
    ...
```

A more aggressive toleration is as follows:

```yaml
spec:
    ...
    tolerations:
        - operator: "Exists"
    ...
```

