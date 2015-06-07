# Collector

[TOC]

# Code Reading

## bin

+ `dcmgr/bin/collector`

## dcmgr/lib/dcmgr

### configurations

+ `dcmgr/lib/dcmgr/configurations.rb`
+ `dcmgr/lib/dcmgr/configurations/dcmgr.rb`

### node_modules

+ `dcmgr/lib/dcmgr/node_modules/alarm_collector.rb`
   + event.subscribe 'hva/instance_terminated'
   + event.subscribe 'hva/instance_terminated'
   + event.subscribe 'hva/instance_turnedoff'
   + event.subscribe 'hva/instance_started'
   + event.subscribe 'hva/instance_turnedon'
+ `dcmgr/lib/dcmgr/node_modules/hva_collector.rb`
   + job.submit "local-store-handle.#{v.volume_device.host_node.node_id}", 'delete_volume'
   + job.submit "sta-handle.#{v.volume_device.storage_node.node_id}", 'delete_volume'
+ `dcmgr/lib/dcmgr/node_modules/natbox_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/resource_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/sta_collector.rb`
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
   + event.subscribe 'submit'
   + event.subscribe 'pop'
   + event.subscribe 'cancel'
   + event.subscribe 'find'
   + event.subscribe 'finish_success'
   + event.subscribe 'finish_fail'
   + event.subscribe 'find_job'

### rpc.start
