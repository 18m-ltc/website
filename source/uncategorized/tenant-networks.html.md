---
title: Tenant Networks
authors: rkukura
wiki_title: Tenant Networks
wiki_revision_count: 6
wiki_last_updated: 2013-07-30
---

# Tenant Networks

Work in progress!

Tenant networks are isolated virtual layer 2 networks typically created by or for, and used by, individual tenants. Creating a tenant network requires no special administrative privilege. The details on how the virtual network is implemented (VLAN, tunnel, ...) are not controlled by or visible to the tenant.

The openvswitch, linuxbridge, and ml2 plugins each support several tenant network types:

*   local - Provide connectivity between instances and services on the same node, but no remote connectivity. Requires no network interface or switch configuration. Applicable only in all-in-one deployments.
*   vlan - Provide connectivity between nodes using IEEE 802.1Q VLAN tags to segment multiple virtual networks within the same physical network link. Requires VLAN compatible network interfaces and switches configured to trunk ranges of VLANs dedicated to tenant networks.
*   gre - Provide connectivity between nodes using GRE tunnels. Requires L3 connectivity between nodes, but no special interface or switch configuration. Supported by openvswitch and ml2 plugins, but not available on RHEL 6.4 without installing unsupported OVS kernel modules.
*   vxlan - Similar to GRE, but uses VXLAN.

Given that local networks do not provide connectivity between nodes, and that gre and vxlan networks are not available on RHEL 6.4, vlan tenant networks are the only current workable option in multi-node deployments.

In order to use vlan tenant networks, one or more physical networks configured to trunk ranges of VLANs must connect all compute and network nodes. Each distinct physical network is identified by a physical_network name in plugin and L2 agent configurations, and in the provider networking API extension. The same VLAN tag can be reused on different physical networks for different virtual networks.

Utilizing vlan tenant networks requires [plugin configuration](Plugin_Configuration) to select vlan as the tenant network type and to specify the range(s) of VLAN tags available for tenant networks, and [L2 agent configuration](L2_Agent_Configuration) to map the physical networks carrying the tenant networks.
