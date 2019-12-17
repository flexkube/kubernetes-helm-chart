# kubernetes

Installs Kubernetes control plane.

## Configuration

The following tables list the configurable parameters of the kubernetes chart and their default values.

### General
| Parameter  | Description                                                                                                                  | Default |
|------------|------------------------------------------------------------------------------------------------------------------------------|---------|
| `replicas` | Number of replicas of control plane components to run. Ideally it should equal to number of controller nodes in the cluster. | `1`     |
