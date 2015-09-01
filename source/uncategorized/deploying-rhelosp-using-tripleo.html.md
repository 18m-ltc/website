---
title: Deploying RHELOSP using TripleO
authors: slagle, tzumainn
wiki_title: Deploying RHELOSP using TripleO
wiki_revision_count: 14
wiki_last_updated: 2014-12-19
---

# Deploying RHELOSP using TripleO

Red Hat Enterprise Linux OpenStack Platform version 6.0 can be deployed using TripleO. The TripleO tooling includes Tuskar, a UI driven tool for the deployment of OpenStack.

The process is largely the same as the process for RDO, so be sure to be familiar with the steps at [Deploying_RDO_using_Instack](Deploying_RDO_using_Instack).

This page will document the differences needed to the RDO instructions in order to use RHELOSP instead of RDO.

## Use RHEL 7 instead of Fedora 20

All systems must be RHEL 7 instead of Fedora 20. This includes the virtual host if using a virtual machine environment and the baremetal Undercloud system if using all baremetal.

## Use RHELOSP instead of RDO

Enable the RHELOSP repositories instead of the RDO and EPEL repositories. This must be done on both the virtual host if using a virtual machine environment and the Undercloud system for both virtual and baremetal environments.. Register to a Satellite Server that configures the RHELOSP 6.0 repositories. Access to the Optional, Extras and HA repositories is also required.

If not using a Satellite Server, a manually crafted yum repo file that configures the required repositories can be installed under /etc/yum.repos.d.

## Prerequisites for `instack-virt-seutp`

Prior to running `instack-virt-setup`, download the RHEL 7 guest image from the Red Hat Customer Portal [<https://access.redhat.com/downloads/content/69/ver=/rhel>---7/7.0/x86_64/product-downloads]. The guest image must then be hosted on an HTTP(S) server that does not require authentication.

If using a Satellite Server:

      export RHOS=1
      export DIB_CLOUD_IMAGES="`[`http://`](http://)<server>`/path/to/directory/containing/guest/image"
      # The name of the guest image downloaded from the Red Hat Customer Portal
      export BASE_IMAGE_FILE="rhel-guest-image-7.0-20140930.0.x86_64.qcow2"
      export REG_METHOD=satellite
`export REG_SAT_URL="`[`http://`](http://)<satellite-server"
 # Find this with `subscription-manager list --available`
 export REG_POOL_ID="<pool-id>`"`
      export REG_USER="`&lt;username&gt;`"
      export REG_PASSWORD="`<password>`"
`export REG_REPOS="`<list of space separated repositories"

If using a manual yum repo configuration file:

 export RHOS=1
 export DIB_YUM_REPO_CONF=/etc/yum.repos.d/<rhelosp-repo-file>`.repo`
      export DIB_CLOUD_IMAGES="`[`http://`](http://)<server>`/path/to/directory/containing/guest/image"
      # The name of the guest image downloaded from the Red Hat Customer Portal
      export BASE_IMAGE_FILE="rhel-guest-image-7.0-20140930.0.x86_64.qcow2"
      export REG_METHOD=disable

Finally, execute `instack-virt-setup`.

## Prerequisites for `instack-install-undercloud`

Prior to installing the `instack-undercloud` rpm, perform the same steps on the Undercloud as in the above section for [Deploying_RHELOSP_using_TripleO#Prerequisites_for_instack-virt-seutp](Deploying_RHELOSP_using_TripleO#Prerequisites_for_instack-virt-seutp). This must be done on both virtual and baremetal Underclouds.

Then, install the <instack-undercloud> package.

Next, Overcloud images must be built on the Undercloud. They will be created in the current directory. The environment variables defined in the previous section must still be set in the current environment.

      instack-build-images
