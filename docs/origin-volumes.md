
[Managing Volumes](https://docs.openshift.org/latest/dev_guide/volumes.html)

``` bash
$ oc volume dc --all 
deploymentconfigs/apache-kafka
  empty directory as kafka-logs
    mounted at /tmp/kafka-logs in container apache-kafka
  empty directory as zookeeper
    mounted at /tmp/zookeeper in container apache-zookeeper
deploymentconfigs/kafka-debug


$ oc volume po --all 
pods/apache-kafka-1-v539g
  empty directory as kafka-logs
    mounted at /tmp/kafka-logs in container apache-kafka
  empty directory as zookeeper
    mounted at /tmp/zookeeper in container apache-zookeeper
  secret/default-token-8khf2 as default-token-8khf2
    mounted at /var/run/secrets/kubernetes.io/serviceaccount in container apache-kafka
    mounted at /var/run/secrets/kubernetes.io/serviceaccount in container apache-zookeeper
pods/kafka-debug-1-2nq4c
  secret/default-token-8khf2 as default-token-8khf2
    mounted at /var/run/secrets/kubernetes.io/serviceaccount

$ oc volume rc --all 
replicationcontrollers/apache-kafka-1
  empty directory as kafka-logs
    mounted at /tmp/kafka-logs in container apache-kafka
  empty directory as zookeeper
    mounted at /tmp/zookeeper in container apache-zookeeper
replicationcontrollers/kafka-debug-1


$ oc volume svc --all 
error: services/apache-kafka the object is not a pod or does not have a pod template
```

# docker 




```
03:31:52 $ docker volume ls
DRIVER              VOLUME NAME
local               10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d
local               1250c69ce26f9b259ee0a73d6968d31ebc54a6fbc338f32f2430a424875efd1e
local               1b7413e83e4dd02a806ce40ed3a9fb2f46604be3b48a936cb3729e6482e6c062
local               269015f69e74b861bcb4b3886aa4477749e7e4699a510837003fb2224e28874d
local               2ebf0f03f09537581377f42c98a5492dcc8a74e9fc57f0197aa247f5ea6c8c64
local               369349f7b959383da7376ee5504a04220a8ea0e10f99da139a7fa34702967761
local               5c4a0069aefa093704e21c41d90a30496ce3a93559f28307bb48bbb00efcfb2f
local               70cfa802b9e64d109c055463197fd24be47e3fd51597cb0dadbf80e8e6897c40
local               839f1fcb4859b7732de1385dedb30b13d726a305981a952f345632a80b73a175
local               8f603012c148ef505c7b46376dd21864844a00f657a65e5a06df712ea4f8ca58
local               93998d8ee3031c646f36c3de390eff0104fe3fa149dd6d470c2fb9960174890e
local               b724695bddab8d535fe5b6aee6259e5598f058cd900a2e4fc880575d65796328
local               c0fadb2cb2d5496b120f381712063d0e9027afbdf897d5125b95e03f84b1c04a
local               fb4187a3eb88f255425a38945ca3a375aab616cd0ab06809f4e52ff4bc894413


$ docker volume inspect 10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d
[
    {
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d/_data",
        "Name": "10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d",
        "Options": {},
        "Scope": "local"
    }
]
```

notare che questo lo ritrovemo nei mounts del container associato al pod *kafka-debug_kafka-debug* come:
        "Mounts": [
            {
                "Type": "volume",
                "Name": "10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d",
                "Source": "/var/lib/docker/volumes/10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d/_data",
                "Destination": "/tmp/kafka-logs",


```
$ docker ps
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS                                                              NAMES
b214f360de63        rondinif/openshift-kafka           "bash"                   15 minutes ago      Up 15 minutes                                                                          k8s_kafka-debug_kafka-debug-1-l09l2_myproject_0567948a-91de-11e7-af2e-965306c2f103_0
c087ec778ba3        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           15 minutes ago      Up 15 minutes                                                                          k8s_POD_kafka-debug-1-l09l2_myproject_0567948a-91de-11e7-af2e-965306c2f103_0
561259aae4e6        rondinif/openshift-kafka           "bin/zookeeper-ser..."   About an hour ago   Up About an hour                                                                       k8s_apache-zookeeper_apache-kafka-1-v539g_myproject_d1faecf0-91d4-11e7-af2e-965306c2f103_0
fb7826400032        rondinif/openshift-kafka           "bin/kafka-server-..."   About an hour ago   Up About an hour                                                                       k8s_apache-kafka_apache-kafka-1-v539g_myproject_d1faecf0-91d4-11e7-af2e-965306c2f103_0
e17a618d20a6        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           About an hour ago   Up About an hour                                                                       k8s_POD_apache-kafka-1-v539g_myproject_d1faecf0-91d4-11e7-af2e-965306c2f103_0
782d1dc9dd54        openshift/origin-docker-registry   "/bin/sh -c '/usr/..."   About an hour ago   Up About an hour                                                                       k8s_registry_docker-registry-1-f8c2g_default_c2567eb3-91d4-11e7-af2e-965306c2f103_0
b7fce15b4912        openshift/origin-haproxy-router    "/usr/bin/openshif..."   About an hour ago   Up About an hour                                                                       k8s_router_router-1-r5rlt_default_c239b947-91d4-11e7-af2e-965306c2f103_0
18110cc4f6ab        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           About an hour ago   Up About an hour                                                                       k8s_POD_docker-registry-1-f8c2g_default_c2567eb3-91d4-11e7-af2e-965306c2f103_0
7f99d34eccb8        openshift/origin-pod:v3.6.0        "/usr/bin/pod"           About an hour ago   Up About an hour    0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:1936->1936/tcp   k8s_POD_router-1-r5rlt_default_c239b947-91d4-11e7-af2e-965306c2f103_0
e4a634b9a963        openshift/origin:v3.6.0            "/usr/bin/openshif..."   About an hour ago   Up About an hour                                                                       origin


$ docker container inspect b214f360de63
[
    {
        "Id": "b214f360de63c17f7f4c0aa4833ea11f5eb37284b0113f2927844379b4f9de6d",
        "Created": "2017-09-05T02:00:45.516446095Z",
        "Path": "bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 16268,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2017-09-05T02:00:45.900252388Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:c89d684441277277c131380a35344da0bc8fa959fd7559e9f5c957c6f70160d0",
        "ResolvConfPath": "/var/lib/docker/containers/c087ec778ba3c79b7666eb29cb3d46c90ee0702e6c7868a7614ff7be78587300/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/c087ec778ba3c79b7666eb29cb3d46c90ee0702e6c7868a7614ff7be78587300/hostname",
        "HostsPath": "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/etc-hosts",
        "LogPath": "/var/lib/docker/containers/b214f360de63c17f7f4c0aa4833ea11f5eb37284b0113f2927844379b4f9de6d/b214f360de63c17f7f4c0aa4833ea11f5eb37284b0113f2927844379b4f9de6d-json.log",
        "Name": "/k8s_kafka-debug_kafka-debug-1-l09l2_myproject_0567948a-91de-11e7-af2e-965306c2f103_0",
        "RestartCount": 0,
        "Driver": "overlay2",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/volumes/kubernetes.io~secret/default-token-8khf2:/var/run/secrets/kubernetes.io/serviceaccount:ro",
                "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/etc-hosts:/etc/hosts",
                "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/containers/kafka-debug/cb5bc353:/dev/termination-log"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "container:c087ec778ba3c79b7666eb29cb3d46c90ee0702e6c7868a7614ff7be78587300",
            "PortBindings": null,
            "RestartPolicy": {
                "Name": "",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": [
                "KILL",
                "MKNOD",
                "SETGID",
                "SETUID",
                "SYS_CHROOT"
            ],
            "Dns": null,
            "DnsOptions": null,
            "DnsSearch": null,
            "ExtraHosts": null,
            "GroupAdd": [
                "1000050000"
            ],
            "IpcMode": "container:c087ec778ba3c79b7666eb29cb3d46c90ee0702e6c7868a7614ff7be78587300",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 1000,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": [
                "label=level:s0:c7,c4",
                "seccomp=unconfined"
            ],
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 2,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "/kubepods/besteffort/pod0567948a-91de-11e7-af2e-965306c2f103",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DiskQuota": 0,
            "KernelMemory": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": -1,
            "OomKillDisable": false,
            "PidsLimit": 0,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/bc4156ac3b25253f7291fc7415819bf3b902260d925bae96371fba6c1588b733-init/diff:/var/lib/docker/overlay2/65044a455881a2c057b620cded81c6eea15f499013835a20620bdee9b623f938/diff:/var/lib/docker/overlay2/b9408255dc22cf3e5d7a417719ed2df797fdd2087485b7936fd54b9dbce1540d/diff:/var/lib/docker/overlay2/9fd397864de8fcb83851514296e6a820422b1e726ac216f671b348456fe3ffaa/diff",
                "MergedDir": "/var/lib/docker/overlay2/bc4156ac3b25253f7291fc7415819bf3b902260d925bae96371fba6c1588b733/merged",
                "UpperDir": "/var/lib/docker/overlay2/bc4156ac3b25253f7291fc7415819bf3b902260d925bae96371fba6c1588b733/diff",
                "WorkDir": "/var/lib/docker/overlay2/bc4156ac3b25253f7291fc7415819bf3b902260d925bae96371fba6c1588b733/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [
            {
                "Type": "volume",
                "Name": "10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d",
                "Source": "/var/lib/docker/volumes/10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d/_data",
                "Destination": "/tmp/kafka-logs",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "1b7413e83e4dd02a806ce40ed3a9fb2f46604be3b48a936cb3729e6482e6c062",
                "Source": "/var/lib/docker/volumes/1b7413e83e4dd02a806ce40ed3a9fb2f46604be3b48a936cb3729e6482e6c062/_data",
                "Destination": "/tmp/zookeeper",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "bind",
                "Source": "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/volumes/kubernetes.io~secret/default-token-8khf2",
                "Destination": "/var/run/secrets/kubernetes.io/serviceaccount",
                "Mode": "ro",
                "RW": false,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/etc-hosts",
                "Destination": "/etc/hosts",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/var/lib/origin/openshift.local.volumes/pods/0567948a-91de-11e7-af2e-965306c2f103/containers/kafka-debug/cb5bc353",
                "Destination": "/dev/termination-log",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "kafka-debug-1-l09l2",
            "Domainname": "",
            "User": "1000050000",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "2181/tcp": {},
                "2888/tcp": {},
                "3888/tcp": {},
                "9092/tcp": {}
            },
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": false,
            "Env": [
                "KUBERNETES_PORT_53_TCP_PORT=53",
                "APACHE_KAFKA_SERVICE_PORT=9092",
                "APACHE_KAFKA_SERVICE_PORT_KAFKA=9092",
                "APACHE_KAFKA_PORT_2181_TCP_PROTO=tcp",
                "KUBERNETES_SERVICE_PORT_DNS_TCP=53",
                "KUBERNETES_PORT_443_TCP=tcp://172.30.0.1:443",
                "KUBERNETES_PORT_53_UDP_PORT=53",
                "KUBERNETES_PORT_53_TCP=tcp://172.30.0.1:53",
                "APACHE_KAFKA_PORT_2181_TCP=tcp://172.30.170.131:2181",
                "KUBERNETES_PORT_443_TCP_PORT=443",
                "KUBERNETES_PORT_53_UDP_ADDR=172.30.0.1",
                "APACHE_KAFKA_PORT_9092_TCP=tcp://172.30.170.131:9092",
                "APACHE_KAFKA_PORT_9092_TCP_PROTO=tcp",
                "APACHE_KAFKA_PORT_9092_TCP_ADDR=172.30.170.131",
                "KUBERNETES_SERVICE_PORT=443",
                "KUBERNETES_SERVICE_PORT_HTTPS=443",
                "KUBERNETES_PORT_443_TCP_ADDR=172.30.0.1",
                "APACHE_KAFKA_SERVICE_PORT_ZOOKEEPER=2181",
                "KUBERNETES_PORT=tcp://172.30.0.1:443",
                "KUBERNETES_PORT_53_UDP_PROTO=udp",
                "KUBERNETES_SERVICE_HOST=172.30.0.1",
                "APACHE_KAFKA_PORT_9092_TCP_PORT=9092",
                "APACHE_KAFKA_PORT_2181_TCP_ADDR=172.30.170.131",
                "KUBERNETES_PORT_443_TCP_PROTO=tcp",
                "KUBERNETES_PORT_53_TCP_PROTO=tcp",
                "APACHE_KAFKA_PORT=tcp://172.30.170.131:9092",
                "KUBERNETES_PORT_53_UDP=udp://172.30.0.1:53",
                "APACHE_KAFKA_SERVICE_HOST=172.30.170.131",
                "APACHE_KAFKA_PORT_2181_TCP_PORT=2181",
                "KUBERNETES_SERVICE_PORT_DNS=53",
                "KUBERNETES_PORT_53_TCP_ADDR=172.30.0.1",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": null,
            "Image": "rondinif/openshift-kafka@sha256:460c0476aff586aab535363faee4c29a330746d4ce582203951258a2e99ecb9a",
            "Volumes": {
                "/tmp/kafka-logs": {},
                "/tmp/zookeeper": {}
            },
            "WorkingDir": "/opt/kafka",
            "Entrypoint": [
                "bash"
            ],
            "OnBuild": null,
            "Labels": {
                "annotation.io.kubernetes.container.hash": "c19a793f",
                "annotation.io.kubernetes.container.restartCount": "0",
                "annotation.io.kubernetes.container.terminationMessagePath": "/dev/termination-log",
                "annotation.io.kubernetes.container.terminationMessagePolicy": "File",
                "annotation.io.kubernetes.pod.terminationGracePeriod": "30",
                "build-date": "20170801",
                "io.kubernetes.container.logpath": "/var/log/pods/0567948a-91de-11e7-af2e-965306c2f103/kafka-debug_0.log",
                "io.kubernetes.container.name": "kafka-debug",
                "io.kubernetes.docker.type": "container",
                "io.kubernetes.pod.name": "kafka-debug-1-l09l2",
                "io.kubernetes.pod.namespace": "myproject",
                "io.kubernetes.pod.uid": "0567948a-91de-11e7-af2e-965306c2f103",
                "io.kubernetes.sandbox.id": "c087ec778ba3c79b7666eb29cb3d46c90ee0702e6c7868a7614ff7be78587300",
                "license": "GPLv2",
                "name": "CentOS Base Image",
                "vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {}
        }
    }
]


```

# Check Volumes on docker vm

``` bash 
cd ~/Library/Containers/com.docker.docker/Data 
cd com.docker.driver.amd64-linux
screen ./tty
```


``` bash
/ # cd /var/lib/docker/volumes/
/var/lib/docker/volumes # ls -lrt
total 88
drwxr-xr-x    3 root     root          4096 Aug  8 03:04 5c4a0069aefa093704e21c41d90a30496ce3a93559f28307bb48bbb00efcfb2f
drwxr-xr-x    3 root     root          4096 Aug  8 03:04 839f1fcb4859b7732de1385dedb30b13d726a305981a952f345632a80b73a175
drwxr-xr-x    3 root     root          4096 Aug  8 03:04 1250c69ce26f9b259ee0a73d6968d31ebc54a6fbc338f32f2430a424875efd1e
drwxr-xr-x    3 root     root          4096 Aug  8 03:04 269015f69e74b861bcb4b3886aa4477749e7e4699a510837003fb2224e28874d
drwxr-xr-x    3 root     root          4096 Aug 23 09:49 b724695bddab8d535fe5b6aee6259e5598f058cd900a2e4fc880575d65796328
drwxr-xr-x    3 root     root          4096 Aug 23 09:49 2ebf0f03f09537581377f42c98a5492dcc8a74e9fc57f0197aa247f5ea6c8c64
drwxr-xr-x    3 root     root          4096 Aug 23 09:51 c0fadb2cb2d5496b120f381712063d0e9027afbdf897d5125b95e03f84b1c04a
drwxr-xr-x    3 root     root          4096 Aug 23 09:51 fb4187a3eb88f255425a38945ca3a375aab616cd0ab06809f4e52ff4bc894413
drwxr-xr-x    3 root     root          4096 Aug 23 09:54 8f603012c148ef505c7b46376dd21864844a00f657a65e5a06df712ea4f8ca58
drwxr-xr-x    3 root     root          4096 Aug 23 09:54 369349f7b959383da7376ee5504a04220a8ea0e10f99da139a7fa34702967761
drwxr-xr-x    3 root     root          4096 Sep  5 00:58 70cfa802b9e64d109c055463197fd24be47e3fd51597cb0dadbf80e8e6897c40
drwxr-xr-x    3 root     root          4096 Sep  5 00:58 93998d8ee3031c646f36c3de390eff0104fe3fa149dd6d470c2fb9960174890e
-rw-------    1 root     root         65536 Sep  5 02:00 metadata.db
drwxr-xr-x    3 root     root          4096 Sep  5 02:00 1b7413e83e4dd02a806ce40ed3a9fb2f46604be3b48a936cb3729e6482e6c062
drwxr-xr-x    3 root     root          4096 Sep  5 02:00 10891885ae03a542ce163932311f877ca6ef4b251eb60b57cac3123a5a02cf1d
/var/lib/docker/volumes # 
```


