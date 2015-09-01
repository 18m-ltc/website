---
title: TestedSetups 2014 01
authors: amuller, bcrochet, cgirda, dron, edu, flaper87, gfidente, ihrachys, jary,
  jlibosva, jruzicka, krwhitney, mangelajo, marun, mbourvin, mpavlase, mrhodes, ndipanov,
  nmagnezi, oblaut, ohochman, panda, pixelbeat, rbowen, rlandy, thaha, vaneldik, whayutin,
  yrabl, zaitcev
wiki_title: TestedSetups 2014 01
wiki_revision_count: 132
wiki_last_updated: 2014-02-21
---

# TestedSetups 2014 01

Tested Setups for [RDO_test_day_January_2014](RDO_test_day_January_2014)

*See [TestedSetups](TestedSetups) for the canonical list, including history and filed bugs.*

## Example Entry

| Config Name                                                    | Release          | BaseOS   | Status                                       | HOWTO                                               | Who    | Date       | BZ/LP | Notes Page |
|----------------------------------------------------------------|------------------|----------|----------------------------------------------|-----------------------------------------------------|--------|------------|-------|------------|
| All-in-One w/ Quantum OVS (no tunnels, fake bridge) Networking | Grizzly 2013.1.3 | RHEL 6.4 | <span style="background:#00ff00">Good</span> | [Neutron-Quickstart](Neutron-Quickstart) | pmyers | 2013-09-08 | None  | None       |

## Packstack Based Installation (Neutron Networking)

| Config Name                                                           | Release | BaseOS     | Status | HOWTO                                                                                                                                           | Who               | Date          | BZ/LP | Notes Page |
|-----------------------------------------------------------------------|---------|------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|---------------|-------|------------|
| All-in-One                                                            |         | RHEL 6.5   | ??     | [Quickstart](Quickstart)                                                                                                             | nmagnezi/ukalifon | 01/07-08/2014 | None  | None       |
|                                                                       |         | CentOS 6.5 | ??     | [Quickstart](Quickstart)                                                                                                             | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 19  | ??     | [Quickstart](Quickstart)                                                                                                             | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 20  | ??     | [Quickstart](Quickstart)                                                                                                             | ??                | ??            | None  | None       |
| Distributed w/ Neutron OVS (no tunnels, real provider net) Networking |         | RHEL 6.5   | ??     | [Quickstart](Quickstart)                                                                                                             | nmagnezi          | 01/07-08/2014 | None  | None       |
| All-in-One w/ Neutron OVS (no tunnels, real provider net) Networking  |         | CentOS 6.5 | ??     | [Neutron-Quickstart w/ External Accessibility](http://allthingsopen.com/2013/08/23/openstack-packstack-installation-with-external-connectivity) | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 19  | ??     | [Neutron-Quickstart w/ External Accessibility](http://allthingsopen.com/2013/08/23/openstack-packstack-installation-with-external-connectivity) | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 20  | ??     | [Neutron-Quickstart w/ External Accessibility](http://allthingsopen.com/2013/08/23/openstack-packstack-installation-with-external-connectivity) | ??                | ??            | None  | None       |
|                                                                       |         | RHEL 6.5   | ??     | [Neutron-Quickstart w/ External Accessibility](http://allthingsopen.com/2013/08/23/openstack-packstack-installation-with-external-connectivity) | ??                | ??            | None  | None       |
| Multi-host w/ Neutron ML2 Networking                                  |         | Fedora 19  | ??     | [Modular Layer 2 (ML2) Plugin](Modular Layer 2 (ML2) Plugin)                                                                         | ??                | ??            | None  | None       |
| Distributed w/ Neutron ML2 Networking                                 |         | RHEL 6.5   | ??     | [Modular Layer 2 (ML2) Plugin](Modular Layer 2 (ML2) Plugin)                                                                         | ??                | ??            | None  | None       |
| Distributed w/ Neutron OVS (GRE) Networking                           |         | CentOS 6.5 | ??     | [Using_GRE_Tenant_Networks](Using_GRE_Tenant_Networks)                                                                            | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 20  | ??     | [Using_GRE_Tenant_Networks](Using_GRE_Tenant_Networks)                                                                            | ??                | ??            | None  | None       |
|                                                                       |         | RHEL 6.5   | ??     | [Using_GRE_Tenant_Networks](Using_GRE_Tenant_Networks)                                                                            | jlibosva          | 01/07/2014    | None  | None       |
| Distributed w/ Neutron OVS (VXLAN) Networking                         |         | RHEL 6.5   | ??     | [Using VXLAN Tenant Networks](Using VXLAN Tenant Networks)                                                                           | amuller           | 01/07/2014    | None  | None       |
|                                                                       |         | Fedora 19  | ??     | [Using VXLAN Tenant Networks](Using VXLAN Tenant Networks)                                                                           | ??                | ??            | None  | None       |
| Neutron VPNaaS Networking                                             |         | RHEL 6.5   | ??     |                                                                                                                                                 | ??                | ??            | None  | None       |
|                                                                       |         | Fedora 20  | ??     |                                                                                                                                                 | ??                | ??            | None  | Non        |
| NeutronLBaaS Networking                                               |         | Fedora 19  | ??     |                                                                                                                                                 | ??                | ??            | None  | None       |
|                                                                       |         | RHEL 6.5   | ??     |                                                                                                                                                 | ??                | ??            | None  | None       |

## Packstack Based Installation (Storage Components) - work in progress

For "Distributed" deployments add the `--cinder-host` or `--glance-host` arguments to Packstack as needed to distribute the installation of the storage component on a separate node.

| Config Name                                          | Release | BaseOS      | Status | HOWTO                                                                                                                                     | Who | Date | BZ/LP | Notes Page |
|------------------------------------------------------|---------|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|-----|------|-------|------------|
| Cinder w/ non-lvm driver (All-In-One)                |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
| Cinder w/ GlusterFS driver (Distributed)             |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Cinder with RDO Havana](Using GlusterFS for Cinder with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Cinder with RDO Havana](Using GlusterFS for Cinder with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Cinder with RDO Havana](Using GlusterFS for Cinder with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Cinder with RDO Havana](Using GlusterFS for Cinder with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Cinder with RDO Havana](Using GlusterFS for Cinder with RDO Havana) | ??  | ??   | None  | None       |
| Glance w/ non-localfs driver (All-In-One)            |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
| Glance w/ mounted GlusterFS (Distributed)            |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Glance with RDO Havana](Using GlusterFS for Glance with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Glance with RDO Havana](Using GlusterFS for Glance with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Glance with RDO Havana](Using GlusterFS for Glance with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Glance with RDO Havana](Using GlusterFS for Glance with RDO Havana) | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + [Using GlusterFS for Glance with RDO Havana](Using GlusterFS for Glance with RDO Havana) | ??  | ??   | None  | None       |
| **Optionals**                                        |
| Glance and Cinder w/ non-local drivers (Distributed) |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + DIY                                                                                                 | ??  | ??   | None  | None       |
| Cinder w/ ThinLVM driver (All-In-One or Distributed) |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + [Using ThinLVM for Cinder with RDO Havana](Using ThinLVM for Cinder with RDO Havana)     | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + [Using ThinLVM for Cinder with RDO Havana](Using ThinLVM for Cinder with RDO Havana)     | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + [Using ThinLVM for Cinder with RDO Havana](Using ThinLVM for Cinder with RDO Havana)     | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + [Using ThinLVM for Cinder with RDO Havana](Using ThinLVM for Cinder with RDO Havana)     | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + [Using ThinLVM for Cinder with RDO Havana](Using ThinLVM for Cinder with RDO Havana)     | ??  | ??   | None  | None       |
| Glance w/ Swift driver (All-In-One or Distributed)   |         | CentOS 6.5  | ??     | [Quickstart](Quickstart) + [Using Swift for Glance with RDO Havana](Using Swift for Glance with RDO Havana)         | ??  | ??   | None  | None       |
|                                                      |         | Fedora 19   | ??     | [Quickstart](Quickstart) + [Using Swift for Glance with RDO Havana](Using Swift for Glance with RDO Havana)         | ??  | ??   | None  | None       |
|                                                      |         | Fedora 20   | ??     | [Quickstart](Quickstart) + [Using Swift for Glance with RDO Havana](Using Swift for Glance with RDO Havana)         | ??  | ??   | None  | None       |
|                                                      |         | RHEL 6.5    | ??     | [Quickstart](Quickstart) + [Using Swift for Glance with RDO Havana](Using Swift for Glance with RDO Havana)         | ??  | ??   | None  | None       |
|                                                      |         | RHEL 7 Beta | ??     | [Quickstart](Quickstart) + [Using Swift for Glance with RDO Havana](Using Swift for Glance with RDO Havana)         | ??  | ??   | None  | None       |

## Advanced Installs (Foreman Based) -- Work in Progress

Please see [Deploying RDO Using Foreman](Deploying RDO Using Foreman) for directions on setting up compute and controller nodes using foreman (only RHEL (derivatives) for now)

| Config Name                          | Release    | BaseOS     | Status | HOWTO                                                                                                               | Who      | Date          | BZ/LP | Notes Page |
|--------------------------------------|------------|------------|--------|---------------------------------------------------------------------------------------------------------------------|----------|---------------|-------|------------|
| 2 Node install with Nova Networking  | Havana H-3 | RHEL 6.5   | ??     | [ 2 Node install with Nova Networking](Deploying_RDO_Using_Foreman #2_Node_install_with_Nova_Networking) | ohochman | 01/07-08/2013 | None  | None       |
|                                      |            | CentOS 6.5 | ??     | [ 2 Node install with Nova Networking](Deploying_RDO_Using_Foreman#2_Node_install_with_Nova_Networking)  | ??       | ??            | None  | None       |
| 3 Node install with Neutron          | Havana H-3 | RHEL 6.5   | ??     | [ 3 Node install with Neutron](Deploying_RDO_Using_Foreman#Neutron_with_Networker_Node)                  | ohochman | 01/07-08/2013 | None  | None       |
|                                      |            | CentOS 6.5 | ??     | [ 3 Node install with Neutron](Deploying_RDO_Using_Foreman#Neutron_with_Networker_Node)                  | majopela | 01/08/2013    | None  | None       |
| Multi-host w/ Load Balanced Services | Havana H-3 | RHEL 6.5   | ??     | [ Load Balanced API](Load_Balance_OpenStack_API)                                                         | ??       | ??            | None  | None       |
|                                      |            | CentOS 6.5 | ??     | [ Load Balanced API](Load_Balance_OpenStack_API)                                                         | ??       | ??            | None  | None       |
| HA MySql                             | Havana H-3 | RHEL 6.5   | ??     | [ HA MySql](Deploying_RDO_Using_Foreman#HA_Database_Cluster)                                             | ??       | ??            | None  | None       |
|                                      |            | CentOS 6.5 | ??     | [ HA MySql](Deploying_RDO_Using_Foreman#HA_Database_Cluster)                                             | ??       | ??            | None  | None       |
