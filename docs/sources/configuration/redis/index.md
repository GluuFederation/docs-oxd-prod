# Using Redis storage in oxd

OXD maintains a copy of client details registered in `OpenID Connect provider` in oxd storage. The storage used in oxd can be either `H2` or `Redis` depending on need of the project. OXD has feature to configure `Redis` storage in standalone, sharded or cluster mode which will be covered in detail in this section.

## Redis in `Standalone` mode

When Redis is configured in `Standalone` mode, oxd stores client details in single Redis server. To configure Redis in `Standalone` mode we need to follow below steps:

1. [Install](../../install/index.md) Gluu CE with oxd.

1. Download and install [Redis](https://redis.io/topics/quickstart) on local server.  

1. Start Redis server using command:

    ```
    redis-server
    ```

1. In `/opt/oxd-server/conf/oxd-server.yml` of oxd set following `storage_configuration`.

    ```
    storage: redis
    storage_configuration:
        servers: "localhost:6379"
        redisProviderType: STANDALONE
    ```
    
1. Start oxd server and execute client registration command. This will store client information in Redis storage.
    
## Redis in `Cluster` mode

In `Cluster` mode oxd stores client details in multiple running Redis server. To configure Redis in `Cluster` mode we need to follow below steps:

1. [Install](../../install/index.md) Gluu CE with oxd.

1. Download and install [Redis](https://redis.io/topics/quickstart) on different virtual machines (VMs).  

1. Start Redis server on different VMs using below command:

    ```
    redis-server
    ```

1. In `/opt/oxd-server/conf/oxd-server.yml` of oxd set following `storage_configuration`.

    ```
    storage: redis
    storage_configuration:
        servers: "redis-host1-IP:6379,redis-host2-IP:6380,redis-host3-IP:6381,redis-host4-IP:6382"
        redisProviderType: CLUSTER
    ```
    
1. Start oxd server and execute client registration command. This will store client information in any one of the Redis server instance.
