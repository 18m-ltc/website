---
title: Frequently Asked Questions
category: documentation
authors: amuller, beni, dneary, garrett, jasonbrooks, jruzicka, jwinn, kashyap, rbowen,
  sgordon
wiki_category: Documentation
wiki_title: Frequently Asked Questions
wiki_revision_count: 42
wiki_last_updated: 2014-10-07
---

# Frequently Asked Questions

## What is RDO?

RDO is a freely-available, community-supported distribution of OpenStack that runs on Red Hat Enterprise Linux, Fedora, and their derivatives. In addition to providing a set of software packages, RDO is a place for users of cloud computing platform on Red Hat Linux operating systems to get help and compare notes on running OpenStack.

## What does RDO stand for?

RDO has no expansion or longer name, officially. It is not an acronym or abbreviation for anything.

However, RDO does focus on building a distribution of OpenStack specific to Red Hat operating systems (and clones of Red Hat operating systems). So, in some sense you can think of RDO as being a project started by Red Hat to build a distribution of OpenStack.

The 3 letter meaningless acronym sort of comes from that line of thinking.

## What is OpenStack?

OpenStack is an open source project for building a private or public infrastructure-as-a-service (IaaS) cloud running on standard hardware. You can learn more about it by visiting [www.openstack org](http://www.openstack.org/).

## Why is RDO needed?

Just as a traditional operating system relies on the hardware beneath it, so too does the OpenStack cloud operating system rely on the foundation of a hypervisor and OS platform. RDO makes it easy to install and deploy the most up-to-date OpenStack components on the industry's most trusted Linux platform, Red Hat Enterprise Linux. and on RHEL derivatives like Fedora, CentOS, and Scientific Linux.

## Why should I use an OpenStack distribution from Red Hat?

The OpenStack project benefits from a broad group of providers and distributors, but none match Red Hat's combination of production experience, technical expertise, and commitment to the open source way of producing software. Some of the largest production clouds in the world run on and are supported by Red Hat, and Red Hat engineers contribute to every layer of the OpenStack platform. From the Linux kernel and the KVM hypervisor to the top level OpenStack project components, Red Hat is at or near the top of the list in terms of number of developers and of contributions.

## For which distributions does RDO provide packages?

RDO targets Red Hat Enterprise Linux, Fedora, and their derivatives. Specifically, RDO packages are available for RHEL 6.3 or later (and CentOS 6.3+, ScientificLinux 6.3+ and other similar derivatives), as well as Fedora 18 and later. Please note that el6.3 does not have [Open vSwitch](http://www.openvswitch.org) in its kernel - for Open vSwitch support in Neutron, you should install kernel version 2.6.32-343 or later.

Additionally note that the kernel included in el6.3 and el6.4 does not include support for network namespaces. Network namespaces are heavily relied upon by OpenStack Networking functionality. A kernel with network namespaces support is available in the RDO repositories. Run this command to install the RDO-supplied kernel once the RDO software repository has been enabled:

         # yum install kernel-2.6.32-*.openstack.el6.x86_64

Reboot the system to ensure that the newly installed kernel is running before proceeding with OpenStack Networking configuration and installation. Note that installing this kernel overrides/supersedes the officially supported RHEL kernel.

## How is RDO different from upstream?

The OpenStack project develops code, and does not handle packaging for specific platforms. As a distribution of OpenStack, RDO packages the upstream OpenStack components to run well together on Red Hat Enterprise Linux, Fedora, and their derivatives, and provides you with installation tools to make it easier for you to deploy OpenStack.

[Stable client branches](Clients) are maintained per release as part of RDO because client backward compatibility isn't guaranteed upstream.

## How can I participate?

Sign up to our [forum](http://openstack.redhat.com/forum) and help others with their RDO questions. Add your case study to our case studies section of the wiki. Take the best answer from a forum thread and turn it into a new page in the knowledge base. Feel free to contribute any packaging and integration patches via our developer mailing lists, or propose improvements to OpenStack on the upstream Launchpad page. For more information, see [ getting involved](get involved).

## Is RDO a fork of OpenStack?

RDO is not a fork of OpenStack, but a community focused on packaging and integrating code from the upstream OpenStack project on Red Hat Enterprise Linux and Fedora based platforms. Red Hat continues to participate in the development of the core OpenStack projects upstream, and all relevant patches and bug reports are routed directly to the OpenStack community codebase.

## What does RDO mean for OpenStack for EPEL?

RDO replaces OpenStack for EPEL ([Extra Packages for Enterprise Linux](http://fedoraproject.org/wiki/EPEL)). The current OpenStack release in EPEL, based on OpenStack Folsom, will continue to work in EPEL. Users who wish to upgrade to Grizzly should move to RDO.

## What does RDO mean for OpenStack on Fedora?

Development of OpenStack for Fedora will continue unchanged. Fedora will continue to ship whatever the latest OpenStack release is, at the time when each Fedora release cycle hits its development freeze date. For Fedora users who want to run a more recent version of OpenStack than that which is available in the Fedora repository, we provide RPMs through RDO. For example, if you are running Fedora 18, and you would like to try Grizzly, then the RDO packages are for you. If you would like to run Grizzly on Fedora 19, then you should install OpenStack packages from the Fedora repository.

Users of OpenStack on Fedora are welcome to participate in the Red Hat OpenStack community forums on openstack.redhat.com and in the Fedora Cloud SIG. There is more information on OpenStack in Fedora [in the Fedora project wiki](http://fedoraproject.org/wiki/OpenStack).

## How do I deploy RDO?

Packstack, an installation utility which uses Puppet modules to deploy OpenStack, is the primary tool for deploying RDO. Instructions on enabling the RDO Yum repository and installing RDO with Packstack are available on the [ quick start](Quickstart) page.

## Where can I find help with RDO?

You can find documentation and get help through the [/forum forums], IRC, or mailing lists and from others in the RDO community. And don't hesitate to answer someone else's question if you know the answer. You can find all of the ways you can get involved in the RDO community at [ getting involved](get involved).

## Can I buy commercial support for RDO?

No commercial support for RDO will be available from Red Hat. Red Hat offers [the enterprise-hardened and supported product Red Hat OpenStack Early Adopter Edition](http://redhat.com/openstack) including a Partner Certification Program and Red Hat's award-winning support offering.

## What is the errata or updates policy for RDO?

RDO updates when the OpenStack project provides updates. RDO provides no lifecycle guarantees beyond what the upstream project provides. If you require additional guarantees see [Red Hat OpenStack](//redhat.com/openstack).

## Can I upgrade between versions of RDO?

We plan to enable RDO users to upgrade between consecutive OpenStack versions (from Grizzly to Havana, for instance). The RDO project will strive to release updated OpenStack versions as soon as possible following upstream releases, on the order of hours to a few days.

## How often are bugfix updates of RDO made available?

RDO bugfix updates will be available every 8 weeks or so, in line with the release schedule of the upstream OpenStack project.

## Who is RDO for?

RDO aims to be the natural option for anyone that wants to run the most recently released version of OpenStack on RHEL, Fedora or their derivatives. Whether you are interested in running OpenStack on CentOS in a production environment, or doing a proof-of-concept deployment on RHEL, RDO is for you.

## Which OpenStack components does RDO include?

RDO includes all "integrated" OpenStack components (Nova, Glance, Keystone, Cinder, Neutron and Horizon), OpenStack client libraries and CLIs, as well as the PackStack installer and assoicated puppet modules. In addition, RDO includes those projects which are Incubating in Grizzly, i.e. Heat and Ceilometer. You can find out more about the OpenStack components included, and about RDO, at [about RDO](about RDO).

## Where is RDO built?

RDO is built using Koji infrastructure, similar to how Fedora packages are built. The build system is accessible at [Fedora Buildsystem](http://koji.fedoraproject.org/koji/).

## What is the relationship between RDO and Red Hat's commercial OpenStack product?

RDO is a community-supported OpenStack distribution that tracks the latest version of OpenStack upstream, beginning with OpenStack Grizzly. Red Hat Enterprise Linux OpenStack Platform is an enterprise-ready commercially-supported product from Red Hat.

<Category:Documentation>
