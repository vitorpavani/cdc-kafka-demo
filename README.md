# cdc-kafka-demo

Change Data Capture with Kafka

Clone the repository:

```
git clone git@github.com:vitorpavani/cdc-kafka-demo.git

cd cdc-kafka-demo
```

1. Create Kubernetes Cluster with Kind:

```
kind create cluster --config kind/cluster.yaml
```

Validated the cluster with Lens. Install Lens from https://k8slens.dev/

2. Install Postgres Operator:

You can install the latest operator manifest for this minor release as follows:

```
kubectl apply -f \
  https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.22/releases/cnpg-1.22.1.yaml
```

You can verify that with:

```
kubectl get deployment -n cnpg-system cnpg-controller-manager
```

Create a Postgres instance:

```
kubectl apply -f postgres/initdb.yaml
```

3. Install MariaDB Operator:

You can install the latest operator manifest for this minor release as follows:

```

