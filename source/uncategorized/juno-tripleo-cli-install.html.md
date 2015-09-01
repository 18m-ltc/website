---
title: Juno-TripleO-CLI-install
authors: d0ugal
wiki_title: Juno-TripleO-CLI-install
wiki_revision_count: 7
wiki_last_updated: 2015-02-09
---

# Juno-Triple O-CLI-install

## Management System Installation

Instack is used to create the management system undercloud.

Questions: - How do we do instack of a hardware deployment?

## Infrastructure Setup

You will need to register your bare-metal hardware as nodes in Ironic. This can be done in one of two ways, the first uses the command line utility register-nodes to add them based on a JSON file.

Option 1: Use register-nodes from os-cloud-config to load the nodes JSON. The JSON file should match the following structure.

         {
           "nodes": [
             {
               "arch": "x86_64",
               "pm_user": "stack",
               "pm_addr": "192.168.122.1",
               "pm_password": "-----BEGIN RSA PRIVATE KEY-----$SNIP-----END RSA PRIVATE KEY-----",
               "pm_type": "pxe_ssh",
               "mac": [
                 "00:0b:d0:69:7e:59"
               ],
               "cpu": "1",
               "memory": "4096",
               "disk": "40"
             },
             {
               "arch": "x86_64",
               "pm_user": "stack",
               "pm_addr": "192.168.122.1",
               "pm_password": "-----BEGIN RSA PRIVATE KEY-----$SNIP-----END RSA PRIVATE KEY-----",
               "pm_type": "pxe_ssh",
               "mac": [
                 "00:0b:d0:69:7e:59"
               ],
               "cpu": "1",
               "memory": "4096",
               "disk": "40"
             },
         }

This can then be loaded like this:

         register-nodes --service-host undercloud --nodes <(jq '.nodes' $JSON_FILE)

Option 2: Use the ironic client and \`ironic node-create\` to add them one at a time.

         ironic node-create

## OpenStack Setup

## Deployment

## Monitoring

The ironic client can be used to view your infrastructure. To get an overview, use the following command which will list all registered nodes and the state of that node.

         ironic node-list

To view the detail for each individual node, use the following command with the ID in the output from above.

         ironic node-show $NODE_ID

## Post-Deployment

To scale a deployment, first you will need to update the deployment plan and then execute this plan with Heat. The following example shows how to scale the number of compute nodes to four.

         PLAN_ID=$( tuskar plan-show overcloud | awk '$2=="uuid" {print $4}' )

         tuskar plan-patch -A compute-1::count=4 $PLAN_ID

         tuskar plan-templates -O tuskar_templates $PLAN_ID

         heat stack-update -f -f "tuskar_templates/plan.yaml" \
             -e "tuskar_templates/environment.yaml" \
             overcloud

Note: At the moment only scaling up is supported.
