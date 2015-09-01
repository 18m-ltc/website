---
title: Deploying Multi Node Overcloud Using TripleO And Tuskar
authors: rlandy
wiki_title: Deploying Multi Node Overcloud Using TripleO And Tuskar
wiki_revision_count: 20
wiki_last_updated: 2014-03-18
---

# Deploying Multi Node Overcloud Using TripleO And Tuskar

## Background

This task follows the steps to install RDO using the [Tuskar](//wiki.openstack.org/wiki/TripleO/Tuskar) and [TripleO](//wiki.openstack.org/wiki/TripleO) projects. The install is done via packages. Note that any pages linked and any information in the linked pages are part of a rapidly changing project and are likely to be modified.

The goal of the task is to:

      - install the Undercloud
      - download images needed for the Overcloud
      - deploy an Overcloud with one Control node, Two Compute nodes and Two Block Storage nodes using Heat
      - test that this Overcloud is functional by deploying a test instance within it
      - tear down the Overcloud
      - re-deploy the same Overcloud using Tuskar
      - test that Overcloud

## Undercloud Setup

The steps to install the undercloud and configure Tuskar are listed in: <https://github.com/agroup/instack-undercloud/blob/master/README-packages.md>. These steps should be executed on Fedora 20.

Note that this task's install uses the instack-baremetal.answers.sample answers file.

## Setup for Overcloud

Download the images needed for the Overcloud and load those images into Glance on the Undercloud following the steps listed here: <https://github.com/agroup/instack-undercloud/blob/master/scripts/instack-prepare-for-overcloud>

Once the images are available in Glance, to deploy the overcloud using Heat, you can either:

      - run "deploy-overcloud' script 
      - manually follow the steps in /usr/share/deploy-overcloud

Note:

      - you need to set values that are appropriate to your environment for the environment variables used deploying the Overcloud:  - $TRIPLEO_ROOT, $CPU, $MEM, $DISK, $ARCH, "$MACS", $PM_IPS",  "$PM_USERS",  "$PM_PASSWORDS", $NeutronPublicInterface, $OVERCLOUD_LIBVIRT_TYPE
      - to deploy an Overcloud with Block Storage, you will need to include /usr/share/tripleo-heat-templates/block-storage.yaml when building the overcloud.yaml file
      - to deploy an Overcloud with multiple Compute or Block Storage nodes, you need to modify the --scale (or COMPUTESCALE) argument.

## Deploying an Overcloud

## Overcloud Steps

## Deploy the Overcloud using Tuskar
