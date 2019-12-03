# Redis configuration in oxd

OXD maintains a copy of client details registered in `OpenID Connect provider` in its storage. The storage used in oxd can be either `H2` or `Redis` depending on need of the project. OXD has feature to configure `Redis` storage in standalone, sharded and cluster configuration modes which will be covered in detail in this section.

## Redis in `Standalone` mode

In standalone mode oxd stores client details in single Redis server. To configure Redis in standalone mode we need to follow
