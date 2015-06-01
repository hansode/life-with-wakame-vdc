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
   + `hva/instance_terminated`
   + `hva/instance_terminated`
   + `hva/instance_turnedoff`
   + `hva/instance_startea`d
   + `hva/instance_turnedon`
+ `dcmgr/lib/dcmgr/node_modules/hva_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/natbox_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/resource_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/sta_collector.rb`
+ `dcmgr/lib/dcmgr/node_modules/scheduler.rb`
   + `schedule_instance`
   + `schedule_start_instance`
   + `schedule_volume`
+ `dcmgr/lib/dcmgr/node_modules/maintenance.rb`
   + `expire_ip_handles`
+ `dcmgr/lib/dcmgr/node_modules/event_hook.rb`
   + `unregister_instance`
   + `update_load_balancer`
+ `dcmgr/lib/dcmgr/node_modules/job_queue_proxy.rb`
   + `submit`
   + `pop`
   + `cancel`
   + `find`
   + `finish_success`
   + `finish_fail`
   + `find_job`
