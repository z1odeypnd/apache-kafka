# "Apache Kafka for OpenShift"

Run [Apache Kafka](https://kafka.apache.org/) and [Apache ZooKeeper](https://zookeeper.apache.org/) on [OpenShift v3](https://www.openshift.com/).

This was developed for demonstration purposes. Use in environments where you do not need persistence in the face of failures or configuration tuning for scale.

The architecture is as follows:

* 1 pod containing a container for Apache Kafka and Apache ZooKeeper
* 1 service to access the containers


## Quick start

###  Load resources (Templates, DeploymentConfigs, Services)

```bash
oc create -f https://raw.githubusercontent.com/rondinif/openshift-kafka/master/resources.yaml
```

### Deploy the Apache Kafka + Apache Zookeeper pod

``` bash
oc new-app apache-kafka
```
#### Optional: Follow container logs
choose one of: [apache-kafka apache-zookeeper]
``` bash 
$ oc get pods
NAME                   READY     STATUS    RESTARTS   AGE
apache-kafka-1-ln40b   2/2       Running   0          1s

$ oc logs --follow apache-kafka-1-ln40b -c apache-kafka
[2017-08-10 12:25:17,652] INFO KafkaConfig values: 
	advertised.host.name = apache-kafka

$ oc logs --follow apache-kafka-1-ln40b -c apache-zookeeper
[2017-08-10 12:25:17,652] INFO KafkaConfig values: 
	advertised.host.name = apache-kafka
```

...for specific diagnostic purpose log grepping could be useful :
``` bash 
$ oc logs --follow apache-kafka-1-ln40b -c apache-zookeeper | grep "socket connection"
[2017-08-10 12:28:25,291] INFO Accepted socket connection from /172.17.0.7:57822 (org.apache.zookeeper.server.NIOServerCnxnFactory)
[2017-08-10 12:28:25,324] INFO Closed socket connection for client /172.17.0.7:57822 which had sessionid 0x15dcc1b2ea40001 (org.apache.zookeeper.server.NIOServerCnxn)
[2017-08-10 12:28:38,960] INFO Accepted socket connection from /172.17.0.7:57838 (org.apache.zookeeper.server.NIOServerCnxnFactory)
[2017-08-10 12:28:38,996] INFO Closed socket connection for client /172.17.0.7:57838 which had sessionid 0x15dcc1b2ea40002 (org.apache.zookeeper.server.NIOServerCnxn)
[2017-08-10 12:30:33,112] INFO Accepted socket connection from /172.17.0.7:57996 (org.apache.zookeeper.server.NIOServerCnxnFactory)
[2017-08-10 12:30:33,332] INFO Closed socket connection for client /172.17.0.7:57996 which had sessionid 0x15dcc1b2ea40003 (org.apache.zookeeper.server.NIOServerCnxn)
```

### Follow the [Apache Kafka Documentation Quick Start](https://kafka.apache.org/documentation.html#quickstart)

1. Deploy a debugging container and connect to it

``` bash
oc run -it --rm kafka-debug --image=rondinif/openshift-kafka --command -- bash
```

2. Create a topic

``` bash
bin/kafka-topics.sh --create --zookeeper apache-kafka --replication-factor 1 --partitions 1 --topic test
```

3. List topics

``` bash
bin/kafka-topics.sh --list --zookeeper apache-kafka
```

4. Send some messages

``` bash
bin/kafka-console-producer.sh --broker-list apache-kafka:9092 --topic test <<EOF
foo
bar
baz
EOF
```

5. Receive some messages

``` bash
bin/kafka-console-consumer.sh --bootstrap-server apache-kafka:9092 --topic test --from-beginning
```

## Credits

* This is based on work original by [Jim Minter](https://github.com/jim-minter) and a fork of https://github.com/mattf/openshift-kafka by [Matthew Farrellee](https://github.com/mattf)
