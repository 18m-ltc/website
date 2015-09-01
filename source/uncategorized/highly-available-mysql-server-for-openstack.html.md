---
title: Highly Available MySQL server for OpenStack
authors: dneary, radez
wiki_title: Highly Available MySQL server for OpenStack
wiki_revision_count: 40
wiki_last_updated: 2013-10-07
---

# Highly Available MySQL with RDO

When running OpenStack API services with MySQL on a single node, the database is a single point of failure. This guide will show how to manually deploy pacemaker and and use it to manage your MySQL cluster across multiple nodes.

### Prerequisites

This guide assumes that OpenStack has been deployed with a single database node and that a second node has an unused mysql server install on it. An all in one install will be fine for demonstration purposes. See the RDO QuickStart guide to get OpenStack installed.

### Overview

MySQL will be configured in an active/passive configuration. Pacemaker will manage a floating ip address across the two MySQL nodes. The ip address will live on the master node and the slave node will be configured to replicate all the activity on the master to the slave. In the event of a failure, Pacemaker will move the ip to the slave node. Once the slave starts to receive writes the master will be out of sync from the slave and will require a resync before replication can be reestablished.
