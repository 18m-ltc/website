---
title: HA
authors: beekhof, radez, rbowen
wiki_title: HA
wiki_revision_count: 19
wiki_last_updated: 2015-03-09
---

# HA

## Purpose

This document will outline the process to set up multiple control nodes that have services highly available and load balanced across the nodes. For demonstration purposes each node will all of the api services, mysql and qpid installed and running as appropriate. The concepts in this document could be applied to break the services on each node further into smaller collections of services.

## Overview

Start by setting each control node up as you would a non-HA/LB control node. Once each control node is installed without being aware of each other the nodes will be clustered together using Pacemaker. This involves clustering the database and messaging as pacemaker resources. MySQL wil be clustered in an active/passive fail over using shared storage only mounted on one node at a time. Qpid will be also be configured in an active/passive fail over configuration, this document will be updated in the future to demonstrate how to deploy qpid in a clustered configuration. Most of the other control services are stateless and, therefore, can be load balanced without further configuration. Exceptions to this include the nova-consoleauth service and the neutron L2 agent. The will be added as pacemaker resources and only run on a single host at a time.

## Openstack Installation

[ Installing Multinode Havana w/GRE](GettingStartedHavana_w_GRE) will walk through a multinode installation using GRE. This is the installation needed to proceed with HA and LB configuration. Use that document to install each of your control nodes. The number of compute nodes is up to you. You can specify the same or different compute node(s) for each install. You will update their settings later to make sure they're talking to the control nodes properly. The important piece in this installation is to separate your control from your compute nodes, that you do multiple control node installations (run that doc once for each control node) and that GRE is setup.

Be sure to provision your nodes with RHEL 6.5+
The pacemaker features in this document require the version of pacemaker in 6.5.

## High Availability Configuration

Now that the control nodes are installed, the next step is to cluster them. This is accomplished by having them share a database store and ensuring that messaging is highly available.

[ Highly Available MySQL server for OpenStack ](Highly_Available_MySQL_server_for_OpenStack) will get pacemaker installed and configured so that the nodes are highly available and MySQL is HA.

[ Highly Available Qpid for OpenStack](Highly_Available_Qpid_for_OpenStack) will get qpid clustered and/or added to pacemaker.

nova-consoleauth also needs to be added to pacemaker, this will ensure that it only runs on one node. Your remote console will not work if consoleauth is running in more than one place.

    node1$ pcs resource create consoleauth lsb:openstack-nova-consoleauth --group test-group

## Load Balancing

HA Proxy is used to load balance the services. It should be installed on each of the control nodes.

    # yum install -y haproxy

Next use the configuration from [ HA Proxy Configuration](Load_Balance_OpenStack_API#HAProxy) to configure HA Proxy.
* Keepalived will not be used, pacemaker will handle keeping HA Proxy highly available.
* The configuration on that wiki doc refers to quantum. The ports are the same in neutron, replace quantum with neutron if you want to.
 Once HA Proxy is configured it can be added to pacemaker as a resource to ensure that it's kept highly available.

    node1$ pcs resource create qpidd lsb:qpidd --group test-group

In this example, HA Proxy is added to the same test-group as before. That way it will run on the same host as the floating ip.

## Securing Services

Use [ Securing Services](Securing_Services) to secure your MySQL, qpid and Apache services

## Updating OpenStack

At this point all the services are being managed by either pacemaker or HAProxy. The final step is to point all the config files for the OpenStack cluster to use the floating ip address to connect to the API endpoints. Do this both in the config files and in the keystone endpoints. There are quite a few config files this should be updated. An update to a keystone endpoint is a delete then re-add, There is not an actual update method.
