---
title: Networking
category: troubleshooting
authors: dneary, forrest, palmtown, rbowen
wiki_category: Troubleshooting
wiki_title: Networking
wiki_revision_count: 27
wiki_last_updated: 2013-12-19
---

# Networking

__NOTOC__

## Toolchain

A number of tools come in handy when troubleshooting Neutron/Quantum networking issues.

*   [Open vSwitch](//openvswitch.org/) ([documentation](http://openvswitch.org/support/))
    -   [ovs-vsctl](//openvswitch.org/cgi-bin/ovsman.cgi?page=utilities%2Fovs-vsctl.8) - tool for querying and configuring ovs-vswitchd
    -   [ovs-ofctl](//openvswitch.org/cgi-bin/ovsman.cgi?page=utilities%2Fovs-ofctl.8) - OpenFlow configuration tool
    -   [ovs-dpctl](//openvswitch.org/cgi-bin/ovsman.cgi?page=utilities%2Fovs-vsctl.8) - query and configure Open vSwitch datapaths
*   [iproute tools](//www.linuxfoundation.org/collaborate/workgroups/networking/iproute2)
    -   [iproute2 HOWTO](//www.policyrouting.org/iproute2.doc.html)
    -   [iproute2 examples](//www.linuxfoundation.org/collaborate/workgroups/networking/iproute2_examples)
*   [tcpdump](//www.tcpdump.org/) (see next section)
    -   [tcpdump documentation](//www.tcpdump.org/#documentation)

### tcpdump

tcpdump will be your best friend so it's best to learn and understand how to use it. When debugging routing issues with quantum, tcpdump can be used to investigate the ingress and egress of traffic. For example:

           tcpdump -n -i br-int  

The above command will capature all traffic on the internal bridge interface.

           tcpdump -n -i br-int  -w tcpdump.pcap

The above command will capture all traffic on the internal bridge interface and dump it to a file named tcpdump.pcap.

           tcpdump -r tcpdump.pcap

The above command will read in a previously created tcpdump file

           tcpdump -n -i any

The above command will capture all traffic on any interface. ...

## Common issues

*   I can create an instance, but cannot SSH or ping it
    -   Verify that traffic to port 22 and ICMP traffic of any type (-1:-1) is allowed in the default security group

In the dashboard, in the Project tab, under "Access and Security", check the rules which are active on the security group you are using with your instances (typically "default"). You should see a rule allowing traffic to port 22 over tcp from all hosts, and a port enabling icmp traffic of all types (-1). If you don't, create the necessary rules, and try again.

*   -   Verify that you can ping and SSH the host where the instance is running

From the host where you are attempting to connect to your instance, verify that network traffic is being correctly routed to the compute node in question.

*   -   Check that you can ping an instance from inside its network namespace. FIXME: Add a sample command.
    -   Ensure that the router is correctly created, that the internal subnet and external subnet are attached to it, and that it can route traffic from your IP to the instance IP
    -   Check the OVS routing table to ensure that it is correctly routing traffic from internal to external. FIXME: Add a sample command.
    -   Verify that br-ex is associated with the physical NIC, and that the virtual router can route traffic to the IP address of the host

<!-- -->

*   I cannot associate a floating IP with an instance
    -   If the error is that the external network is not visible from the subnet: Check that br-ex has its MAC address set correctly. Check for the error "Device or resource busy" in /var/log/messages - if it's present, you will need to bring down br-ex, set its MAC address to match that of the physical NIC, and bring it back up.
*   I can create an instance, however, it does not get a DHCP address
    -   See [network troubleshooting](http://docs.openstack.org/trunk/openstack-ops/content/network_troubleshooting.html) for information on sniffing the various steps of the allocation of an IP address by DHCP - verify that your DHCP agent is running, is receiving the DHCPDISCOVER request, and is replying to it - and verify that your host is receiving the DHCP reply.
    -   Make sure that IPv6 is enabled. Disabling IPv6 will give an error such as "Address family not supported".
    -   If you are using OpenvSwitch with VLAN, make sure that the network created includes VLAN information. You may need to restart the quantum-openvswitch-agent service and/or create a network using specific VLAN information.

<!-- -->

*   If you are using more than one node for OpenStack (i.e., not an all-in-one installation), then you must use VLANs.
*   If you are using a virtual machine as a node in OpenStack, you must use the virtio network driver when using VLANs. The default rt8139 driver seems to drop VLAN information.
*   You must have an external network set as the gateway to the router if you want to get network traffic out of the private instance network.

...

## Useful resources

*   [Quantum L3 workflow](//docs.openstack.org/trunk/openstack-network/admin/content/l3_workflow.html)
*   [Network troubleshooting](//docs.openstack.org/trunk/openstack-ops/content/network_troubleshooting.html)

...

<Category:Troubleshooting> <Category:Documentation> [Category:In progress](Category:In progress)
