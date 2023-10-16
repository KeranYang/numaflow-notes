# Test Kafka locally on Numaflow

This note documents how to test Kafka locally on Numaflow. All resoures created are using `keran-` as prefix.

### Install Kafka

Use helm to install Kafka: https://bitnami.com/stack/kafka/helm

```bash
# Install bitnami
helm repo add bitnami https://charts.bitnami.com/bitnami
# Install Kafka with release name "keran"
helm install keran bitnami/kafka --set replicaCount=2
```

After running the installation, you should have 3 running pods

```
keran-kafka-0
keran-kafka-1
keran-zookeeper-0
```

### Start Kafka client

To create a pod that you can use as a Kafka client, run the following commands

```bash
# Create the pod
kubectl run keran-kafka-client --restart='Never' --image docker.io/bitnami/kafka:3.4.0-debian-11-r15 --namespace default --command -- sleep infinity
# Execute bash command in the pod
kubectl exec --tty -i keran-kafka-client --namespace default -- bash
```

### Prepare input and output Kafka topics

```bash
kafka-topics.sh --bootstrap-server keran-kafka.default.svc.cluster.local:9092 --topic input-topic-partition-debug --create --partitions 2 --replication-factor 2
```

```bash
kafka-topics.sh --bootstrap-server keran-kafka.default.svc.cluster.local:9092 --topic output-topic-partition --create --partitions 2
```

### Create Test Numaflow pipeline

```yaml
apiVersion: numaflow.numaproj.io/v1alpha1 
kind: Pipeline 
metadata:
  name: sample-kafka-pipeline 
spec: 
  vertices: 
    - name: input
      scale:
        min: 1
      source: 
        kafka: 
          brokers: 
            - 'keran-kafka-0.keran-kafka-headless.default.svc.cluster.local:9092'
            - 'keran-kafka-1.keran-kafka-headless.default.svc.cluster.local:9092'
          topic: input-topic-partition-debug
          consumerGroup: keran-consumer-group 
    - name: output 
      scale:
        min: 1
      sink: 
        kafka: 
          brokers:
            - 'keran-kafka-0.keran-kafka-headless.default.svc.cluster.local:9092'
            - 'keran-kafka-1.keran-kafka-headless.default.svc.cluster.local:9092'
          topic: output-topic-partition
  edges: 
    - from: input
      to: output
```

### Frequently used Kafka CLI commands

```bash
# Send messages to a topic
kafka-console-producer.sh --broker-list keran-kafka-0.keran-kafka-headless.default.svc.cluster.local:9092,keran-kafka-1.keran-kafka-headless.default.svc.cluster.local:9092 --topic input-topic-partition-debug
# Send messages to a topic with keys (using ":"" as key/value separator)
kafka-console-producer.sh --broker-list keran-kafka-0.keran-kafka-headless.default.svc.cluster.local:9092 --topic input-topic-partition --property "parse.key=true" --property "key.separator=:"
# Inspect partitions
kafka-consumer-groups.sh --bootstrap-server keran-kafka.default.svc.cluster.local:9092 --describe --all-groups
# Receive messages
kafka-console-consumer.sh --bootstrap-server keran-kafka.default.svc.cluster.local:9092 --topic output-topic-partition --from-beginning
# Delete source topic
kafka-topics.sh --bootstrap-server keran-kafka.default.svc.cluster.local:9092 --topic input-topic-partition --delete

# Perf Test
kafka-producer-perf-test.sh --topic input-topic-partition-debug --num-records 9000 --producer-props bootstrap.servers=keran-kafka-0.keran-kafka-headless.default.svc.cluster.local:9092,keran-kafka-1.keran-kafka-headless.default.svc.cluster.local:9092 --throughput 9000 --record-size 100
```

### Clean up

Uninstall Kafka
```bash
helm uninstall keran
```

Delete client pod
```bash
kubectl delete pod keran-kafka-client
```
