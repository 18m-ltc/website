---
title: OpenDaylight integration
authors: hshen, mavenugo, nmaadara, rbowen, shague, snowzach
wiki_title: OpenDaylight integration
wiki_revision_count: 22
wiki_last_updated: 2015-01-22
---

# OpenDaylight integration

OpenDaylight is an open platform for network programmability to enable Software-Defined Networking (SDN). OpenDaylight has driver for Neutron ML2 (Modular Layer 2) plugin to enable communication between Neutron and OpenDaylight. On the SDN controller side, OpenDaylight has northbound APIs to interact with Neutron and use OVSDB (Open vSwitch Database Management Protocol) for southbound configuration of vSwitches on compute nodes. Thus OpenDaylight can manage network connectivity and initiate GRE or VXLAN tunnels for compute nodes. This page will provide step-by-step detail for neutron integration with OpenDaylight.

## Run OpenDaylight Controller

Get OpenDaylight virtualization edition from [OpenDaylight Release Download](http://www.opendaylight.org/software/downloads). Follow the [Installation Guide](https://wiki.opendaylight.org/view/Release/Hydrogen/Virtualization/Installation_Guide) to install. OpenDaylight can either run on same host of OpenStack control node or on separate Host. The host must have at least 3GB memory if running RDO computer node and ODL controller.

Both OpenStack Swift and OpenDaylight use port 8080 for webpage GUI. You can either disable OpenStack swift or move OpenDaylight webserver to different port. To move ODL webserver to different port, edit *Connector port* value in configuration/tomcat-server.xml under opendaylight directory:

` `<Service name="Catalina">
`     `<Connector port="8081" protocol="HTTP/1.1"
                     connectionTimeout="20000"
                                    redirectPort="8443" />

Make sure it has following line for OpenFlow 1.3 config in the configuration/config.ini file. If not, add this line to config.ini file (or uncomment it if it is commented):

      ovsdb.of.version=1.3

Start the controller with OVSDB and OpenFlow 1.3 option

      ./run.sh -virt ovsdb -of13

## Configure ovsdb to connect to OpenDaylight and provide network information

The odl.sh script below accomplishes a few things:

1.  The set-manager instructs ovsdb to connect to OpenDaylight as a manager.
2.  Configuration values are added to the other_config field in the Open_vSwitch table to inform OpenDaylight of information it needs to provision the bridges:
    1.  bridge_mappings to detail how the physical networks are mapped to the provider networks. This is only needed for vlan isolation.
    2.  local_ip to indicate the tunnel endpoint address which is needed to build the tunnels.

Copy the odl.sh script and run it as:

      sudo ./odl.sh --local_ip 192.168.120.31 --bridge_mappings physnet1:eth1,physnet3:eth3 --odl_ip 192.168.120.1

## Start RDO

Start RDO as suggested in [ RDO Quickstart](Quickstart). After OpenStack is up and running, you can [ add compute nodes](Adding_a_compute_node). Then follow steps in [ ML2 plugin](ML2_plugin) to configure Neutron ML2 plugin.

## Enable ML2 plugin with OpenDaylight controller

Stop neutron server

      service neutron-server stop

Stop openvswitch agent on all aompute nodes

      service neutron-openvswitch-agent stop

Download the [OpenDaylight driver for ML2 plugin](https://raw.github.com/CiscoSystems/neutron/odl_ml2/neutron/plugins/ml2/drivers/mechanism_odl.py) and put the driver file under /usr/lib/python2.7/site-packages/neutron/plugins/ml2/drivers/ or /usr/lib/python2.6/site-packages/neutron/plugins/ml2/drivers/ if using RHEL 6.5.

------------------------------------------------------------------------

''If using OpenStack Havana, you need to change neutron constants import and egg-info file. Skip these two steps if you are using OpenStack Icehouse.

1. remove following line in downloaded OpenDaylight driver:

      from neutron.plugins.openvswitch.common import constants

And add this line in OpenDaylight driver to change constants import :

      from neutron.openstack.common import constants

2. Modification in egg-info is also required for using OpenStack Havana, Add entry point in /usr/lib/python2.6/site-packages/neutron-2013.2.1-py2.6.egg-info/entry_points.txt

      [neutron.ml2.mechanism_drivers]
      ...
      opendaylight = neutron.plugins.ml2.drivers.mechanism_odl:OpenDaylightMechanismDriver

''

------------------------------------------------------------------------

Modify /etc/neutron/plugins/ml2/ml2_conf.ini in Openstack control node:

      crudini --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 mechanism_drivers opendaylight 
`crudini --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 tenant_network_types `<gre or vxlan>

Configure the odl section in /etc/neutron/plugins/ml2/ml2_conf.ini in Openstack control node

      [odl]
      nodes = 
`network_vlan_ranges = `<vlan_min>`:`<vlan_max>
`tunnel_id_ranges = `<tunnel_id_min>`:`<tunnel_id_max>
      tun_peer_patch_port = patch-int
      int_peer_patch_port = patch-tun
      tenant_network_type = vlan
      tunnel_bridge = br-tun
      integration_bridge = br-int
      controllers = `<ODL_controller_ip>`:`<ODL_controller_port>`:admin:admin

Configure the ml2_odl section in /etc/neutron/plugins/ml2_conf.ini in Openstack control node

      [ml2_odl]
      password = admin
      username = admin
      url = `[`http://`](http://)<ODL_controller_ip>`:`<ODL_controller_port>`/controller/nb/v2/neutron

There is no change required at Openstack compute node. Example of ml2_conf.ini file including above changes can also be find [here](http://paste.openstack.org/show/61851/).

Once everything is properly configured, but before starting neutron-server, recreate the ML2 database:

      mysql -e "drop database if exists neutron_ml2;"
      mysql -e "create database neutron_ml2 character set utf8;"
      mysql -e "grant all on neutron_ml2.* to 'neutron'@'%';"
      neutron-db-manage --config-file /usr/share/neutron/neutron-dist.conf --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugin.ini upgrade head

Start neutron server

      service neutron-server start

## Create network and launch VM instances

Create network and attach subnet by neutron commands

`neutron net-create `<network name>` --provider:network_type `<gre or vxlan>` --provider:segmentation_id `<id>
      neutron  subnet-create `<network name>`10.100.2.0/24 --name `<subnet name>` 

Add VM instance to the subnet by [ running instances](Running_an_instance). VM on different compute nodes should be able to ping each other through GRE or VxLAN tunnels provisioned by ODL controller.

## odl.sh script

      #!/bin/bash

      local_ip=""
      bridge_mappings=""
      odl_ip=""

      function usage {
          local rc=$1
          local outstr=$2

          if [ "$outstr" != "" ]; then
              echo "$outstr"
              echo
          fi

      `     echo "Usage: `basename $0` [OPTION...]" `
          echo
          echo "Script options:"
          echo "  --local_ip IP               IP address of the node, will be used as tunnel endpoint"
          echo "  --bridge_mappings MAPPINGS  physical provider mappings, i.e physnet1:eth1,physnet2:eth2"
          echo "  --odl_ip IP                 IP address of OpenDaylight controller"
          echo
          echo "Help options:"
          echo "  -?, -h, --h, --help  Display this help and exit"
          echo

          exit $rc
      }

      function parse_options {
          while true ; do
              case "$1" in
              --local_ip)
                  shift; local_ip="$1"; shift
                  ;;

               --bridge_mappings)
                  shift; bridge_mappings="$1"; shift
                  ;;

               --odl_ip)
                  shift; odl_ip="$1"; shift
                  ;;

            -? | -h | --h | --help)
                  usage 0
                  ;;
              "")
                  break
                  ;;
              *)
                  echo "Ignoring unknown option: $1"; shift;
              esac
          done
      }

      parse_options "$@"

      ` if [ `whoami` != "root" ]; then `
          usage 1 "Please execute this script as superuser or with sudo previleges."
      fi

      if [ -n "$odl_ip" ]; then
          echo "setting odl_ip=$odl_ip"
          ovs-vsctl set-manager tcp:$odl_ip:6640
      fi

      read ovstbl <<< $(ovs-vsctl get Open_vSwitch . _uuid)

      if [ -n "$bridge_mappings" ]; then
          sudo ovs-vsctl set Open_vSwitch $ovstbl other_config:bridge_mappings=$bridge_mappings
      fi

      if [ -n "$local_ip" ]; then
          sudo ovs-vsctl set Open_vSwitch $ovstbl other_config:local_ip=$local_ip
      fi

      ovs-vsctl list Manager
      echo
      ovs-vsctl list Open_vSwitch .

      exit 0
