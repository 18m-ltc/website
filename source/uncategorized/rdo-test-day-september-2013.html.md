---
title: RDO Test Day September 2013
authors: acalinciuc, admiyo, ajeain, apevec, bdperkin, dneary, eglynn, hateya, ichavero,
  jlibosva, jpichon, jruzicka, mlessard, mrunge, ndipanov, nsantos, oblaut, otherwiseguy,
  pixelbeat, pmyers, rbowen, rcritten, red trela, rkukura, shardy, tgraf, vaneldik,
  whayutin, xqueralt, yrabl
wiki_title: RDO Test Day September 2013
wiki_revision_count: 74
wiki_last_updated: 2013-09-11
---

# RDO Test Day September 2013

We plan to hold a RDO **Havana** test day on September 10th and 11th, 2013. This will be coordinated through the #rdo channel on freenode, and through this wiki and RDO forums.

Results are being posted at [TestedSetups](TestedSetups)

## Who's Available

The following cast of characters will be available testing, workarounds, bug fixes, and general discussion. You can participate in the conversation on the #rdo IRC channel on the Freenode IRC network.

Development

*   pixelbeat (Pádraig Brady)
*   jruzicka (Jakub Ruzicka)
*   tgraf (Thomas Graf) [kernel, Open vSwitch, network namespaces]
*   eglynn (Eoghan Glynn)
*   jpichon (Julie Pichon)
*   mrunge (Matthias Runge)
*   xqueralt (Xavier Queralt)
*   ndipanov (Nikola Dipanov)
*   otherwiseguy (Terry Wilson) [Neutron/Packstack]
*   ayoung (Adam Young) [Keystone]
*   rkukura (Bob Kukura) [Neutron]
*   nsantos (Nuno Santos)

Testing

*   rbowen (Rich Bowen)
*   mosulica (Alin Calinciuc)
*   weshay ( Wes Hayutin)
*   dneary (Dave Neary)
*   pmyers (Perry Myers)
*   ichavero (Ivan Chavero)
*   oblaut (Ofer Blaut)
*   rvaknin (Rami Vaknin)
*   jlibosva (Jakub Libosvar)
*   rcritten (Rob Crittenden)
*   bdperkin (Brandon Perkins)
*   nmagnezi (Nir Magnezi)
*   ajeain (Ami Jeain)
*   ohochman (Omri Hochman)
*   dkorn (Daniel Korn)
*   jdennis (John Dennis)
*   yrabl (Yogev Rabl)
*   dron (Dafna Ron)
*   gifedente (Giulio Fidente)
*   mlessard (Michael Lessard)
*   vaneldik (Jan van Eldik)

## Prerequisite for Test Day

*   A newly¹ installed Fedora 19, Centos 6.4 or RHEL 6.4 VM² or baremetal system
*   With the updates-[testing repositories enabled](http://openstack.redhat.com/Repositories#Testing) and fully updated with: yum update

         yum-config-manager --enable updates-testing 
         yum update

*   If using a RHEL system please ensure the **optional** repo is enabled.
*   Hardware virtualization support (e.g. Intel VT or AMD-V).
*   Up to 10-20Gb free disk space. Guest images take up a lot of space.

¹ It's advisable to have the install images downloaded and the system prepared before the test day (without openstack packages installed)

² You can do basic testing of OpenStack in a virtual machine, which is auto detected by the installer

## How To Test

The things that should be tested are listed on the [Tested Setups](TestedSetups) page.

*   Pick an **RDO Havana** item from the list
*   Go through the scenario as though you were a beginner just following the instructions
*   KEEP GOOD NOTES

If you have problems with any of the tests, report a bug to [Bugzilla](https://bugzilla.redhat.com) usually for one of the [openstack-nova](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-nova), [openstack-glance](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-glance), [openstack-keystone](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-keystone), [openstack-cinder](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-cinder), [openstack-neutron](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-neutron),[openstack-swift](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-swift) or [openstack-heat](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-heat) or [openstack-ceilometer](https://bugzilla.redhat.com/enter_bug.cgi?product=RDO&version=18&component=openstack-ceilometer) components. If you are unsure about exactly how to file the report or what other information to include, just ask on IRC (#rdo, freenode.net) and we will help you.

Once you have completed the tests, add your results to the table on the [TestedSetups](TestedSetups) page, following the examples already there.

## Blogs, Screencasts

We strongly encourage you to write narrative blogs about your test experiences, whether this is about the test day itself, or a howto style post about a particular install/test scenario. Link to those posts here.

Also, if you'd like to do an audio interview for a podcast about this event, please contact Rich Bowen (rbowen at redhat dot com) to set something up.

Use something like RecordMyDesktop to record a screencast of your tests, and let me (rbowen) know if you have any video that you think would make useful HowTo videos.
