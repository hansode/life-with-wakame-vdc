# Natbox

[TOC]

# Code Reading

## bin

+ `dcmgr/bin/natbox`

## dcmgr/lib/dcmgr

### configurations

+ `dcmgr/lib/dcmgr/configurations.rb`
+ `dcmgr/lib/dcmgr/configurations/natbox.rb`

### node_modules

+ Isono::NodeModules::NodeHeartbeat
+ `dcmgr/lib/dcmgr/node_modules/service_natbox.rb`
   + event.subscribe 'vnet/network_route/created'
   + event.subscribe 'vnet/network_route/deleted'

### rpc.start

+ `dcmgr/lib/dcmgr/rpc/natbox_handler.rb`
