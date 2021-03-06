# Schema-Registry Helm Chart
This helm chart creates a [Confluent Schema-Registry server](https://github.com/confluentinc/schema-registry).

## Prerequisites
* Kubernetes 1.6
* A running Kafka Installation
* A running Zookeeper Installation

## Chart Components
This chart will do the following:

* Create a Schema-Registry deployment
* Create a Service configured to connect to the available Schema-Registry pods on the configured
  client port.

Note: Distributed Schema Registry Master Election is done via Kafka Coordinator Master Election
https://docs.confluent.io/current/schema-registry/docs/design.html#kafka-coordinator-master-election

## Installing the Chart
You can install the chart with the release name `mysr` as below.

```console
$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ helm install --name mysr incubator/schema-registry
```

If you do not specify a name, helm will select a name for you.

### Installed Components
You can use `kubectl get` to view all of the installed components.

```console{%raw}
$ kubectl get all -l app=schema-registry
NAME                          DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deploy/mysr-schema-registry   1         1         1            1           23m

NAME                                DESIRED   CURRENT   READY     AGE
rs/mysr-schema-registry-bcb4c994c   1         1         1         23m

NAME                                      READY     STATUS    RESTARTS   AGE
po/mysr-schema-registry-bcb4c994c-qjqbj   1/1       Running   1          23m
```

1. `deploy/mysr-schema-registry` is the Deployment created by this chart.
1. `rs/mysr-schema-registry-bcb4c994c` is the ReplicaSet created by this Chart's Deployment.
1. `po/mysr-schema-registry-bcb4c994c-qjqbj` is the Pod created by the ReplicaSet under this Chart's Deployment.

## Configuration
You can specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```console
$ helm install --name my-release -f values.yaml incubator/schema-registry
```

> **Tip**: You can use the default [values.yaml](values.yaml)

### Parameters
The following table lists the configurable parameters of the SchemaRegistry chart and their default values.

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `image` | The `SchemaRegistry` image repository | `confluentinc/cp-schema-registry` |
| `imageTag` | The `SchemaRegistry` image tag | `4.0.0` |
| `imagePullPolicy` | Image Pull Policy | `IfNotPresent` |
| `replicaCount` | The number of `SchemaRegistry` Pods in the Deployment | `1` |
| `configurationOverrides` | `SchemaRegistry` [configuration setting](https://github.com/confluentinc/schema-registry/blob/master/docs/config.rst#configuration-options) overrides in the dictionary format `setting.name: value` | `{}` |
| `resources` | CPU/Memory resource requests/limits | `{}` |
| `servicePort` | The port on which the SchemaRegistry server will be exposed. | `8081` |
| `overrideGroupId` | Group ID defaults to using Release Name so each release is its own Schema Registry worker group, it can be overridden | `{- .Release.Name -}}` |
| `kafkaStore.overrideBootstrapServers` | Defaults to Kafka Servers in the same release, it can be overridden in case there was a separate release for Kafka Deploy | `{{- printf "PLAINTEXT://%s-kafka-headless:9092" .Release.Name }}`
| `kafka.enabled` | If `true`, install Kafka/Zookeeper alongside the `SchemaRegistry`. This is intended for testing and argument-less helm installs of this chart only and should not be used in Production. | `true` |
| `kafka.replicas` | The number of Kafka Pods to install as part of the `StatefulSet` if `kafka.Enabled` is `true`| `1` |
| `kafka.zookeeper.servers` | The number of Zookeeper Pods to install as part of the `StatefulSet` if `kafka.Enabled` is `true`| `1` |
| `kafka.ZookeeperUrl` | The URL of the Zookeeper servicing the Kafka installation if `Kafka.Enabled` is `false` | `""` |
| `kafka.ZookeeperPort` | The Port of the Zookeeper servicing the Kafka installation if `Kafka.Enabled` is `false` | `2181` |
