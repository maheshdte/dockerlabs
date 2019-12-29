# Visualizer + Redis + Docker Swarm

# Pre-requisite:

- Browse through https://play-with-docker.com
- Set up 5 Node Docker Swarm Cluster

## Clone the Repository

```
git clone https://github.com/collabnix/dockerlabs
cd dockerlabs/solution/redis/viz-web-redis
```

```
docker stack deploy -c docker-compose.yml myredis
```

## Verifying the Services

```
$ docker service ls
ID                  NAME                 MODE                REPLICAS            IMAGE                             PORTS
hmistkdxnird        myredis_redis        replicated          1/1                 redis:6.0-rc1                     *:6379->6379/tcp
xnvbfxjhhoy2        myredis_visualizer   replicated          1/1                 dockersamples/visualizer:stable   *:8080->8080/tcp
f49rjg16u1n8        myredis_web          replicated          5/5                 ajeetraina/hellowhale:latest      *:80->80/tcp
```

![My Image](https://github.com/collabnix/dockerlabs/blob/master/solution/redis/viz-web-redis/redis1.png)

# Verifying if redis is running successfully

```
$ docker service ps myredis_redis
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
robvimouagqj        myredis_redis.1     redis:6.0-rc1       manager1            Running             Running about a minute ago 
```


## Verifying the Redis Volume



```
$ docker volume inspect myredis_data
[
    {
        "CreatedAt": "2019-12-29T02:18:00Z",
        "Driver": "local",
        "Labels": {
            "com.docker.stack.namespace": "myredis"
        },
        "Mountpoint": "/var/lib/docker/volumes/myredis_data/_data",
        "Name": "myredis_data",
        "Options": null,
        "Scope": "local"
    }
]
```

# Checking the redis logs 

```
[manager1] (local) root@192.168.0.45 ~/dockerlabs/solution/redis/viz-web-redis
$ docker service ps myredis_redis
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
robvimouagqj        myredis_redis.1     redis:6.0-rc1       manager1            Running             Running about a minute ago                       
[manager1] (local) root@192.168.0.45 ~/dockerlabs/solution/redis/viz-web-redis
$ docker service logs -f myredis_redis
myredis_redis.1.robvimouagqj@manager1    | 1:C 29 Dec 2019 02:35:54.400 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
myredis_redis.1.robvimouagqj@manager1    | 1:C 29 Dec 2019 02:35:54.400 # Redis version=5.9.101, bits=64, commit=00000000, modified=0, pid=1, just started
myredis_redis.1.robvimouagqj@manager1    | 1:C 29 Dec 2019 02:35:54.400 # Configuration loaded
myredis_redis.1.robvimouagqj@manager1    | 1:M 29 Dec 2019 02:35:54.402 * Running mode=standalone, port=6379.
myredis_redis.1.robvimouagqj@manager1    | 1:M 29 Dec 2019 02:35:54.402 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
myredis_redis.1.robvimouagqj@manager1    | 1:M 29 Dec 2019 02:35:54.402 # Server initialized
myredis_redis.1.robvimouagqj@manager1    | 1:M 29 Dec 2019 02:35:54.402 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
myredis_redis.1.robvimouagqj@manager1    | 1:M 29 Dec 2019 02:35:54.402 * Ready to accept connections
```

# Where is my redis service running?

```
$ docker service ps myredis_redis
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
robvimouagqj        myredis_redis.1     redis:6.0-rc1       manager1            Running             Running 3 minutes ago
```

# Inspecting Redis Service

```
$ docker service inspect myredis_redis
[
    {
        "ID": "hmistkdxnirdm5vq2f41aaqr9",
        "Version": {
            "Index": 127
        },
        "CreatedAt": "2019-12-29T02:35:47.7810801Z",
        "UpdatedAt": "2019-12-29T02:35:47.78773254Z",
        "Spec": {
            "Name": "myredis_redis",
            "Labels": {
                "com.docker.stack.image": "redis:6.0-rc1",
                "com.docker.stack.namespace": "myredis"
            },
            "TaskTemplate": {
                "ContainerSpec": {
                    "Image": "redis:6.0-rc1@sha256:c2227b1e5c4755cb94f18eef10b34fb4eac116ce8c5ea0a40d0ca806927b8311",
                    "Labels": {
                        "com.docker.stack.namespace": "myredis"
                    },
                    "Args": [
                        "redis-server",
                        "--appendonly",
                        "yes"
                    ],
                    "Privileges": {
                        "CredentialSpec": null,
                        "SELinuxContext": null
                    },
                    "Mounts": [
                        {
                            "Type": "volume",
                            "Source": "myredis_data",
                            "Target": "/home/docker/data",
                            "VolumeOptions": {
                                "Labels": {
                                    "com.docker.stack.namespace": "myredis"
                                }
                            }
                        }
                    ],
                    "StopGracePeriod": 10000000000,
                    "DNSConfig": {},
                    "Isolation": "default"
                },
                "Resources": {},
                "RestartPolicy": {
                    "Condition": "any",
                    "Delay": 5000000000,
                    "MaxAttempts": 0
                },
                "Placement": {
                    "Constraints": [
                        "node.role == manager"
                    ],
                    "Platforms": [
                        {
                            "Architecture": "amd64",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "386",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "ppc64le",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "s390x",
                            "OS": "linux"
                        }
                    ]
                },
                "Networks": [
                    {
                        "Target": "rolenrgn8nqibx2h16wd2tac6",
                        "Aliases": [
                            "redis"
                        ]
                    }
                ],
                "ForceUpdate": 0,
                "Runtime": "container"
            },
            "Mode": {
                "Replicated": {
                    "Replicas": 1
                }
            },
            "UpdateConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "RollbackConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "EndpointSpec": {
                "Mode": "vip",
                "Ports": [
                    {
                        "Protocol": "tcp",
                        "TargetPort": 6379,
                        "PublishedPort": 6379,
                        "PublishMode": "ingress"
                    }
                ]
            }
        },
        "Endpoint": {
            "Spec": {
                "Mode": "vip",
                "Ports": [
                    {
                        "Protocol": "tcp",
                        "TargetPort": 6379,
                        "PublishedPort": 6379,
                        "PublishMode": "ingress"
                    }
                ]
            },
            "Ports": [
                {
                    "Protocol": "tcp",
                    "TargetPort": 6379,
                    "PublishedPort": 6379,
                    "PublishMode": "ingress"
                }
            ],
            "VirtualIPs": [
                {
                    "NetworkID": "sl1ecujt79razdyhjvmbohhjo",
                    "Addr": "10.255.0.26/16"
                },
                {
                    "NetworkID": "rolenrgn8nqibx2h16wd2tac6",
                    "Addr": "10.0.1.15/24"
                }
            ]
        }
    }
]
```

```
$ docker exec -it 2db redis-cli --cluster help
Cluster Manager Commands:
  create         host1:port1 ... hostN:portN
                 --cluster-replicas <arg>
  check          host:port
                 --cluster-search-multiple-owners
  info           host:port
  fix            host:port
                 --cluster-search-multiple-owners
  reshard        host:port
                 --cluster-from <arg>
                 --cluster-to <arg>
                 --cluster-slots <arg>
                 --cluster-yes
                 --cluster-timeout <arg>
                 --cluster-pipeline <arg>
                 --cluster-replace
  rebalance      host:port
                 --cluster-weight <node1=w1...nodeN=wN>
                 --cluster-use-empty-masters
                 --cluster-timeout <arg>
                 --cluster-simulate
                 --cluster-pipeline <arg>
                 --cluster-threshold <arg>
                 --cluster-replace
  add-node       new_host:new_port existing_host:existing_port
                 --cluster-slave
                 --cluster-master-id <arg>
  del-node       host:port node_id
  call           host:port command arg arg .. arg
  set-timeout    host:port milliseconds
  import         host:port
                 --cluster-from <arg>
                 --cluster-copy
                 --cluster-replace
  backup         host:port backup_directory
  help           

For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.
```


# [Optional] Running Redis Service under Docker Swarm Mode

Method: 2

```
$ docker service create --name myredis --publish 6379:6379 --replicas 5 --mount type=volume,source=data,destination=/home/docker/data redis:6.0-rc1
dkbe281eyx1ixadec1q6iexq7
overall progress: 5 out of 5 tasks 
1/5: running   
2/5: running   
3/5: running   
4/5: running   
5/5: running   
verify: Service converged
```

