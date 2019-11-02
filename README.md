# kubernetes

Installs Kubernetes self-hosted control plane.

## Work-Arounds for Known Issues

### Pending kube-apiserver pod when upgrading single replica self-hosted control plane

To ensure maximum survivability during updates, currently `kube-apiserver` pods are created using Deployment with `hostNetwork: true` and it directly binds into node IP address. In addition, when only single replica is requested, we set `maxUnavailable: 0` for upgrade strategy, to make sure there is at least one API server available. That means that existing kube-apiserver pod won't be removed until another one is responding. If you have only single controller node, then new one will not be able to start, since requested port is still occupied by the previous instance.

To finish the update, manually remove currently running `kube-apiserver` pod using following command:
```sh
kubectl delete pod $(kubectl get pods -l k8s-app=kube-apiserver | grep Running | cut -d" " -f1)
```

While this may seem unreasonable, this operation is safe, because:
- new `kube-apiserver` pod is already scheduled to the node, so `kubelet` itself manages it's lifetime
- `kubelet` will actively try to restart failing pods, without a need of API server

After some time (up to 5 minutes, depending on kubelet's pod restart back-off delay), `kube-apiserver` pod
should be restarted and API server should become available again.

## Configuration

The following tables list the configurable parameters of the kubernetes chart and their default values.

### General
| Parameter  | Description                                                                                                                  | Default |
|------------|------------------------------------------------------------------------------------------------------------------------------|---------|
| `replicas` | Number of replicas of control plane components to run. Ideally it should equal to number of controller nodes in the cluster. | `1`     |
