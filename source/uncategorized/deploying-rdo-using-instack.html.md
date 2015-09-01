---
title: Deploying RDO using Instack
authors: gfidente, jcoufal, rbrady, rlandy, slagle, sradvan
wiki_title: Deploying RDO using Instack
wiki_revision_count: 31
wiki_last_updated: 2015-05-05
---

# Deploying RDO using Instack

[ ← Installation and Configuration](Install)

This tutorial covers how to deploy a [TripleO](https://wiki.openstack.org/wiki/TripleO) [Undercloud](http://docs.openstack.org/developer/tripleo-incubator/devtest_undercloud.html) and [Overcloud](http://docs.openstack.org/developer/tripleo-incubator/devtest_overcloud.html) using RDO and Instack on an all baremetal or an all virtual environment.. TripleO is a program aimed at installing, upgrading and operating OpenStack clouds using OpenStack's own cloud facilities as the foundations - building on nova, neutron and heat to automate fleet management at datacenter scale (and scaling down to as few as 2 machines).

Instack executes [diskimage-builder](https://github.com/openstack/diskimage-builder) style elements on the current system. This enables a current running system to have an element applied in the same way that diskimage-builder applies the element to an image build. Using instack, you can quickly build an Undercloud to deploy your Overcloud.

## 1. Deploying an Undercloud

The following sections describe the steps for deploying an Undercloud on all baremetal or all virtual machine environments using RDO packages and Instack.

### Baremetal Setup

[ Deploying RDO on a Baremetal Environment using Instack](Deploying RDO on a Baremetal Environment using Instack)

### Virtual Machine Setup

[ Deploying an Undercloud to a Virtual Machine Environment using RDO via Instack](Deploying RDO to a Virtual Machine Environment using RDO via Instack)

## Deploying an Overcloud

[ Deploying an RDO Overcloud with Instack ](Deploying an RDO Overcloud with Instack)

## Testing the Overcloud

[ Testing an RDO Overcloud with Instack ](Testing an RDO Overcloud with Instack)
