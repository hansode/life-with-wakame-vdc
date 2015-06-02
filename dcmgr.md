# Dcmgr

[TOC]

# Code Reading

## rack config

+ `dcmgr/config-dcmgr.ru`

## dcmgr/lib/dcmgr

### configurations

+ `dcmgr/lib/dcmgr/configurations.rb`
+ `dcmgr/lib/dcmgr/configurations/dcmgr.rb`

### /api

+ `dcmgr/lib/dcmgr/rack/request_logger.rb`

#### /api/12.03

+ `dcmgr/lib/dcmgr/endpoints/12.03/core_api.rb`

#### Require

+ `dcmgr/lib/sinatra/dcmgr_api_setup.rb`
+ `dcmgr/lib/sinatra/quota_evaluation.rb`
+ `dcmgr/lib/sinatra/internal_request.rb`
+ `dcmgr/lib/dcmgr/endpoints/errors.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/quota_definitions.rb`

#### Dcmgr::Endpoints::V1203::CoreAPI

+ `dcmgr/lib/dcmgr/endpoints/12.03/accounts.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/alarms.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/backup_objects.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/backup_storages.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/dc_networks.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/host_node_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/host_nodes.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/images.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/instances.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/ip_handles.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/ip_pools.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/jobs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/load_balancers.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/network_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/network_vifs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/networks.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/reports.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/security_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/ssh_key_pairs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/storage_node_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/storage_nodes.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/text_logs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/volume_snapshots.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/volumes.rb`

##### /instances

##### Dcmgr.messaging.submit

+ scheduler
   + schedule_instance
   + schedule_start_instance
+ hva-handle.
   + terminate
   + reboot
   + stop
   + poweron
   + poweroff
   + soft_poweroff
+ local-store-handle.
   + backup_volume
   + backup_image
+ migration-handle.
   + run_vol_store
+ sta-handle.
   + backup_volume
   + backup_image

##### Dcmgr.messaging.event_publish

+ vnic_joined
+ vnic_left
+ joined_group
+ left_group
+ referencer_added
+ referencer_removed
+ instance.monitoring.refreshed
