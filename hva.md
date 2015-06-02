# Hva

[TOC]

# Code Reading

## bin

+ `dcmgr/bin/hva`

## dcmgr/lib/dcmgr

### configurations

+ `dcmgr/lib/dcmgr/configurations.rb`
+ `dcmgr/lib/dcmgr/configurations/hva.rb`

### node_modules

+ Isono::NodeModules::NodeHeartbeat
+ `dcmgr/lib/dcmgr/node_modules/instance_monitor.rb`
   + check_instance
+ `dcmgr/lib/dcmgr/node_modules/alarm_config_updater.rb`
   + update_alarm
   + delete_alarm
   + delete_alarms
+ `dcmgr/lib/dcmgr/node_modules/monitor.rb`
   + update_alarm
   + update_alarms
   + delete_alarm
   + delete_alarms
   + get_alarm
   + get_alarms
+ `dcmgr/lib/dcmgr/node_modules/service_netfilter.rb`
   + event.subscribe 'hva.#{myinstance.node.manifest.node_instance_id}/vnic_created'
   + event.subscribe 'hva.#{myinstance.node.manifest.node_instance_id}/vnic_destroyed'
   + event.subscribe '#{vnic_id}/joined_group'
   + event.subscribe '#{vnic_id}/left_group'
   + event.subscribe '#{vnic_id}/joined_group'
   + event.subscribe '#{vnic_id}/left_group'
   + event.subscribe '#{ref_group_id}/vnic_joined'
   + event.subscribe '#{ref_group_id}/vnic_left'
   + event.subscribe '#{group_id}/referencer_added'
   + event.subscribe '#{group_id}/referencer_removed'
   + event.subscribe '#{group_id}/rules_updated'
   + event.subscribe '#{group_id}/vnic_joined'
   + event.subscribe '#{group_id}/vnic_left'
   + event.subscribe '#{group_id}/referencer_added'
   + event.subscribe '#{group_id}/referencer_removed'
   + event.subscribe '#{group_id}/rules_updated'

### rpc.start

+ `dcmgr/lib/dcmgr/rpc/hva_context.rb`
+ `dcmgr/lib/dcmgr/rpc/hva_handler.rb`
   + job :attach
   + job :attach_nic
   + job :cleanup
   + job :detach
   + job :detach_nic
   + job :poweroff
   + job :poweron
   + job :reboot
   + job :run_local_store
   + job :run_vol_store
   + job :soft_poweroff
   + job :stop
   + job :terminate
   + job :wait_volumes_available
+ `dcmgr/lib/dcmgr/rpc/local_store_handler.rb`
   + job :backup_image
   + job :backup_volume
   + job :delete_volume
   + job :deploy_volume_and_attach
   + job :run_local_store
+ `dcmgr/lib/dcmgr/rpc/migration_handler.rb`
   + job :run_vol_store
   + job :start_migration
+ `dcmgr/lib/dcmgr/rpc/windows_handler.rb`
   + job :launch_windows

### drivers/hypervisor

+ `dcmgr/lib/dcmgr/drivers/hypervisor.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/dummy_hypervisor.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/linux_hypervisor.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/linux_hypervisor/kvm.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/linux_hypervisor/linux_container.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/linux_hypervisor/linux_container/lxc.rb`
+ `dcmgr/lib/dcmgr/drivers/hypervisor/linux_hypervisor/linux_container/openvz.rb`
