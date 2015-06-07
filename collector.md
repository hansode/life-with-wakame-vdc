# Collector

[TOC]

# Code Reading

## bin

+ `dcmgr/bin/collector`

## dcmgr/lib/dcmgr

### configurations

+ `dcmgr/lib/dcmgr/configurations.rb`
+ `dcmgr/lib/dcmgr/configurations/dcmgr.rb`

### node_modules (collector)

+ `dcmgr/lib/dcmgr/node_modules/alarm_collector.rb`
   + initialize_hook
      + rpc.register_endpoint 'alarm-collector'
   + event.subscribe 'hva/instance_terminated'
   + event.subscribe 'hva/instance_terminated'
   + event.subscribe 'hva/instance_turnedoff'
   + event.subscribe 'hva/instance_started'
   + event.subscribe 'hva/instance_turnedon'
+ `dcmgr/lib/dcmgr/node_modules/hva_collector.rb`
   + initialize_hook
      + rpc.register_endpoint 'hva-collector'
   + rpc 'finalize_instance'
   + rpc 'get_dhcp_conf'
   + rpc 'get_external_monitor_target_host_nodes'
   + rpc 'get_host_node'
   + rpc 'get_host_nodes_index'
   + rpc 'get_instance'
   + rpc 'get_instance_monitor_data'
   + rpc 'get_netfilter_data'
   + rpc 'get_netfilter_network'
   + rpc 'get_netfilter_security_group'
   + rpc 'get_netfilter_vnic'
   + rpc 'get_netfilter_vnic_with_node_id'
   + rpc 'get_network'
   + rpc 'get_networks'
   + rpc 'get_nic'
   + rpc 'get_referencees_of_security_group'
   + rpc 'get_referencers_of_security_group'
   + rpc 'get_resource_monitor_data'
   + rpc 'get_resource_monitor_ids'
   + rpc 'get_rules_of_security_group'
   + rpc 'get_security_groups_of_vnic'
   + rpc 'prepare_host_node_vnet'
   + rpc 'switch_instance_host_node'
   + rpc 'update_image'
   + rpc 'update_instance'
   + job.submit "local-store-handle.#{v.volume_device.host_node.node_id}", 'delete_volume'
   + job.submit "sta-handle.#{v.volume_device.storage_node.node_id}", 'delete_volume'
+ `dcmgr/lib/dcmgr/node_modules/natbox_collector.rb`
   + initialize_hook
      + rpc.register_endpoint 'natbox-collector'
   + rpc 'get_alive_network_routes'
   + rpc 'get_network_route'
+ `dcmgr/lib/dcmgr/node_modules/resource_collector.rb`
   + initialize_hook
      + rpc.register_endpoint 'resource-collector'
   + rpc 'resource_store'
+ `dcmgr/lib/dcmgr/node_modules/sta_collector.rb`
   + initialize_hook
      + rpc.register_endpoint 'sta-collector'
   + rpc 'get_available_volumes'
   + rpc 'get_backup_object'
   + rpc 'get_image'
   + rpc 'get_snapshot'
   + rpc 'get_volume'
   + rpc 'post_process_backup_image'
   + rpc 'register_backup_object_transfer'
   + rpc 'register_image_transfer'
   + rpc 'update_backup_object'
   + rpc 'update_image'
   + rpc 'update_snapshot'
   + rpc 'update_volume'

### node_modules

+ `dcmgr/lib/dcmgr/node_modules/scheduler.rb`
   + event.subscribe 'schedule_instance'
   + event.subscribe 'schedule_start_instance'
   + event.subscribe 'schedule_volume'
   + job.submit "sta-handle.#{vol.volume_device.storage_node.node_id}", 'create_volume_and_run_instance'
   + job.submit "local-store-handle.#{instance.host_node.node_id}", 'run_local_store'
   + job.submit "hva-handle.#{instance.host_node.node_id}", 'run_vol_store'
   + job.submit "local-store-handle.#{instance.host_node.node_id}", 'run_local_store'
   + job.submit "sta-handle.#{volume.storage_node.node_id}", 'create_volume'
   + job.submit "hva-handle.#{src_host_node.node_id}", 'cleanup'
   + job.submit "local-store-handle.#{instance.host_node.node_id}", 'run_local_store'
   + job.submit "hva-handle.#{instance.host_node.node_id}", 'run_vol_store'
+ `dcmgr/lib/dcmgr/node_modules/maintenance.rb`
   + event.subscribe 'expire_ip_handles'
+ `dcmgr/lib/dcmgr/node_modules/event_hook.rb`
   + event.subscribe 'unregister_instance'
   + event.subscribe 'update_load_balancer'
+ `dcmgr/lib/dcmgr/node_modules/job_queue_proxy.rb`
   + initialize_hook
      + rpc.register_endpoint 'jobqueue-proxy'
   + event.subscribe 'submit'
   + event.subscribe 'pop'
   + event.subscribe 'cancel'
   + event.subscribe 'find'
   + event.subscribe 'finish_success'
   + event.subscribe 'finish_fail'
   + event.subscribe 'find_job'

### rpc.start
