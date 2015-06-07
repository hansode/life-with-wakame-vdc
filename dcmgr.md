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
   + Dcmgr.messaging.submit(alarm_endpoint(alarm.metric_name, i.host_node.node_id), 'update_alarm'
   + Dcmgr.messaging.submit(alarm_endpoint(al.metric_name, i.host_node.node_id), 'delete_alarm'
   + Dcmgr.messaging.submit(alarm_endpoint(al.metric_name, i.host_node.node_id), 'update_alarm'
+ `dcmgr/lib/dcmgr/endpoints/12.03/backup_objects.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/backup_storages.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/dc_networks.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/host_node_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/host_nodes.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/images.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/instances.rb`
   + Dcmgr.messaging.event_publish "#{group.canonical_uuid}/vnic_joined"
   + Dcmgr.messaging.event_publish "#{group.canonical_uuid}/vnic_left"
   + Dcmgr.messaging.event_publish "#{ref_sg.canonical_uuid}/referencer_added"
   + Dcmgr.messaging.event_publish "#{ref_sg.canonical_uuid}/referencer_removed"
   + Dcmgr.messaging.event_publish "#{vnic.canonical_uuid}/joined_group"
   + Dcmgr.messaging.event_publish "#{vnic.canonical_uuid}/left_group"
   + Dcmgr.messaging.event_publish "instance.monitoring.refreshed"
   + Dcmgr.messaging.submit "hva-handle.#{i.host_node.node_id}", 'reboot'
   + Dcmgr.messaging.submit "hva-handle.#{i.host_node.node_id}", 'stop'
   + Dcmgr.messaging.submit "hva-handle.#{i.host_node.node_id}", 'terminate'
   + Dcmgr.messaging.submit "hva-handle.#{instance.host_node.node_id}", 'poweroff'
   + Dcmgr.messaging.submit "hva-handle.#{instance.host_node.node_id}", 'soft_poweroff'
   + Dcmgr.messaging.submit "hva-handle.#{instance.host_node.node_id}", 'poweron',
   + Dcmgr.messaging.submit "local-store-handle.#{@instance.host_node.node_id}", 'backup_volume'
   + Dcmgr.messaging.submit "local-store-handle.#{instance.host_node.node_id}", 'backup_image'
   + Dcmgr.messaging.submit "migration-handle.#{dest_host_node.node_id}", 'run_vol_store'
   + Dcmgr.messaging.submit "scheduler", 'schedule_instance'
   + Dcmgr.messaging.submit "scheduler", 'schedule_start_instance'
   + Dcmgr.messaging.submit "sta-handle.#{@volume.volume_device.storage_node.node_id}", 'backup_volume'
   + Dcmgr.messaging.submit "sta-handle.#{volume.storage_node.node_id}", 'backup_image'
   + Dcmgr.messaging.submit "sta-handle.#{volume.storage_node.node_id}", 'backup_volume'
+ `dcmgr/lib/dcmgr/endpoints/12.03/ip_handles.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/ip_pools.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/jobs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/load_balancers.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/network_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/network_vifs.rb`
   + Dcmgr.messaging.event_publish "#{group.canonical_uuid}/vnic_joined"
   + Dcmgr.messaging.event_publish "#{group.canonical_uuid}/vnic_left"
   + Dcmgr.messaging.event_publish "#{ref_sg.canonical_uuid}/referencer_added"
   + Dcmgr.messaging.event_publish "#{ref_sg.canonical_uuid}/referencer_removed"
   + Dcmgr.messaging.event_publish "#{vnic.canonical_uuid}/joined_group"
   + Dcmgr.messaging.event_publish "#{vnic.canonical_uuid}/left_group"
   + Dcmgr.messaging.event_publish "instance.monitoring.refreshed"
   + Dcmgr.messaging.event_publish "vif.monitoring.created"
   + Dcmgr.messaging.event_publish "vif.monitoring.deleted"
   + Dcmgr.messaging.event_publish "vif.monitoring.updated"
+ `dcmgr/lib/dcmgr/endpoints/12.03/networks.rb`
   + Dcmgr.messaging.event_publish "vnet/network_services"
   + Dcmgr.messaging.submit "hva-handle.#{instance.host_node.node_id}", 'attach_nic'
   + Dcmgr.messaging.submit "hva-handle.#{instance.host_node.node_id}", 'detach_nic'
+ `dcmgr/lib/dcmgr/endpoints/12.03/reports.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/security_groups.rb`
   + Dcmgr.messaging.event_publish "#{g.canonical_uuid}/rules_updated"
   + Dcmgr.messaging.event_publish "#{ref.canonical_uuid}/referencer_added"
   + Dcmgr.messaging.event_publish "#{ref.canonical_uuid}/referencer_removed"
   + Dcmgr.messaging.event_publish 'hva/security_group_updated'
+ `dcmgr/lib/dcmgr/endpoints/12.03/ssh_key_pairs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/storage_node_groups.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/storage_nodes.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/text_logs.rb`
+ `dcmgr/lib/dcmgr/endpoints/12.03/volume_snapshots.rb`
   + Dcmgr.messaging.submit "sta-handle.#{sp.node_id}", 'create_snapshot'
   + Dcmgr.messaging.submit "sta-handle.#{sp.node_id}", 'delete_snapshot'
+ `dcmgr/lib/dcmgr/endpoints/12.03/volumes.rb`
   + Dcmgr.messaging.submit "hva-handle.#{i.host_node.node_id}", 'attach'
   + Dcmgr.messaging.submit "hva-handle.#{i.host_node.node_id}", 'detach'
   + Dcmgr.messaging.submit "local-store-handle.#{instance.host_node.node_id}", 'backup_volume',
   + Dcmgr.messaging.submit "scheduler", 'schedule_volume'
   + Dcmgr.messaging.submit "sta-handle.#{@volume.volume_device.storage_node.node_id}", 'backup_volume'
   + Dcmgr.messaging.submit "sta-handle.#{vol.storage_node.node_id}", 'create_volume'
   + Dcmgr.messaging.submit "sta-handle.#{vol.storage_node.node_id}", 'delete_volume'

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
