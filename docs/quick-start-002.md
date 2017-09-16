---
title: Quick Start Explained - part 2
description: Load resources Templates, DeploymentConfigs, Services
---
# Quick Start Explained ( part 2 of 4)
## Load resources (Templates, DeploymentConfigs, Services)
```bash
oc create -f https://raw.githubusercontent.com/rondinif/openshift-kafka/master/resources.yaml
```

``` yaml
kind: List
apiVersion: v1
metadata: {}

items:

- apiVersion: v1
  kind: Template
  metadata:
    name: apache-kafka
    annotations:
      description: 1-pod Apache Kafka
      tags: messaging,streaming,kafka
  parameters:
  - name: NAME
    description: Name prefix for each object created
    required: true
    value: apache-kafka
  - name: IMAGE
    description: Image with Apache Kafka and Apache ZooKeeper
    required: true
    value: rondinif/openshift-kafka
  objects:
  - apiVersion: v1
    kind: DeploymentConfig
    metadata:
      name: ${NAME}
    spec:
      replicas: 1
      selector:
        deploymentconfig: ${NAME}
      template:
        metadata:
          labels:
            deploymentconfig: ${NAME}
        spec:
          containers:
          - name: apache-kafka
            image: ${IMAGE}
            command:
            - bin/kafka-server-start.sh
            args:
            - config/server.properties
            - --override
            - advertised.host.name=${NAME}
            - --override
            - log.segment.bytes=10485760
            - --override
            - log.retention.bytes=10485760
            volumeMounts:
            - mountPath: /tmp/kafka-logs
              name: kafka-logs
            ports:
            - containerPort: 9092
          - name: apache-zookeeper
            image: ${IMAGE}
            command:
            - bin/zookeeper-server-start.sh
            args:
            - config/zookeeper.properties
            volumeMounts:
            - mountPath: /tmp/zookeeper
              name: zookeeper
            ports:
            - containerPort: 2181
          volumes:
          - name: kafka-logs
            emptyDir: {}
          - name: zookeeper
            emptyDir: {}
  - apiVersion: v1
    kind: Service
    metadata:
      name: ${NAME}
    spec:
      ports:
      - name: kafka
        port: 9092
      - name: zookeeper
        port: 2181
      selector:
        deploymentconfig: ${NAME}

```

### DeploymentConfig: dc/apache-kafka 
The **dc** has name ***apache-kafka** , this is configured in parameter ```${NAME}``` for covenience.
each pod modelled by the template of the **dc/apache-kafka** will have a couple of [containers](https://www.docker.com/what-container) both with the same ```${IMAGE}``` but launhing two different command and exposing different ports
  - **apache-kafka** run ```bin/kafka-server-start.sh``` and expose its ```containerPort: 9092```
  - **apache-zookeeper** run ```bin/zookeeper-server-start.sh``` and expose its ```containerPort: 2121```

### Service: svc/apache-kafka 
The **svc** has name ***apache-kafka*** , this is configured in parameter ```${NAME}``` for covenience.
The specifications of this service references the ```deploymentconfig: ${NAME}``` above described.
and consists of **kafka** on ```port: 9092``` and **zookeeper** on ``` port: 2181```


## Deploy the Apache Kafka + Apache Zookeeper pod

``` bash
oc new-app apache-kafka
```

```
--> Deploying template "myproject/apache-kafka" to project myproject

     apache-kafka
     ---------
     1-pod Apache Kafka

     * With parameters:
        * NAME=apache-kafka
        * IMAGE=rondinif/openshift-kafka

--> Creating resources ...
    deploymentconfig "apache-kafka" created
    service "apache-kafka" created
--> Success
    Run 'oc status' to view your app.
```

### wait until kafka and zookeper started
even if you can [inspect the status and see the logs in the browser](https://127.0.0.1:8443/console/) we wants learn about some [Basic Developer CLI Operations](https://docs.openshift.org/latest/cli_reference/basic_cli_operations.html) for [application inspection and modification](https://docs.openshift.org/latest/cli_reference/basic_cli_operations.html#application-modification-cli-operations) and [Troubleshooting and Debugging](https://docs.openshift.org/latest/cli_reference/basic_cli_operations.html#troubleshooting-and-debugging-cli-operations)

### Application inspection
``` bash
oc get all
```
example output:
```
NAME              REVISION   DESIRED   CURRENT   TRIGGERED BY
dc/apache-kafka   1          1         1         config

NAME                DESIRED   CURRENT   READY     AGE
rc/apache-kafka-1   1         1         1         8h

NAME               CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
svc/apache-kafka   172.30.45.167   <none>        9092/TCP,2181/TCP   8h

NAME                      READY     STATUS    RESTARTS   AGE
po/apache-kafka-1-r56qj   2/2       Running   0          8h
```

``` bash
get pods
```
example output:
```
NAME                   READY     STATUS    RESTARTS   AGE
apache-kafka-1-r56qj   2/2       Running   0          8h
```

I suggest taking some time to look at the details of the resources by descibing it withe the command ```oc describe``` for example ```oc descibe po/apache-kafka-1-r56qj``

### Troubleshooting and Debugging
open two new terminal windows ( or tabs ) to follow the logs:
one for: 
```
oc logs --follow apache-kafka-1-r56qj -c apache-kafka
```
the second for: 
```
oc logs --follow apache-kafka-1-r56qj -c apache-zookeeper
```
#### About commands reference documentation
PS: at the current moment I write this memo the syntax of the command reported on https://docs.openshift.org/latest/cli_reference/basic_cli_operations.html#troubleshooting-and-debugging-cli-operations is not correct because of it omits ```-c``` I see   
https://kubernetes.io/docs/user-guide/kubectl/v1.6/#logs for the correct syntax: to check what verson of kubernetes is under **oc** you can see it:
```
$ oc version
``` 

example output:
```
oc v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7
features: Basic-Auth

Server https://127.0.0.1:8443
openshift v3.6.0+c4dd4cf
kubernetes v1.6.1+5115d708d7
```
---
[back to the index](./index.md)