---
title: SELinux issues
category: documentation
authors: dneary, lon, vaneldik
wiki_category: Documentation
wiki_title: SELinux issues
wiki_revision_count: 7
wiki_last_updated: 2014-01-10
---

# SELinux issues

## Troubleshooting SELinux issues

### General tips

SELinux can sometimes be a source of issues with OpenStack. On a system with SELinux enabled (as it is by default on Fedora and RHEL), you can check for denial messages with the command:

    sudo cat /var/log/audit/audit.log | grep denied

or

    sudo sealert -a /var/log/audit/audit.log

You can check the SELinux enforcement status of your machine with the command "sestatus" and can temporarily place SELinux in enforcing (in which it blocks access violations) or permissive (logs access violations, but does not block) with the commands "setenforce 1" and "setenforce 0", respectively.

To make SELinux enforcement changes that persist between reboots, edit the file /etc/selinux/config.

To see what the current security context for a resource ls, run

    ls -Z

For more information on SELinux troubleshooting, see <http://fedoraproject.org/wiki/SELinux/Troubleshooting>.

### PackStack fails if SELinux is disabled

It has been reported that [/forum/discussion/46/install-on-centos-6-4-and-selinux-disabled/p1 PackStack will fail if SELinux is disabled].

The error observed is:

    ERROR : Error during puppet run : err: /Stage[main]//Exec[setenforce 0]/returns: 
    change from notrun to 0 failed: setenforce 0 returned 1 instead of one of [0] 
    at /var/tmp/packstack/51a57c45478b4091b2eb6a1bbd4c2303/manifests/my_public_ip_ring_swift.pp:56
    Please check log file /var/tmp/packstack/20130421-002212-fYYLUA/openstack-setup.log for more information

The solution is to enable SELinux in permissive mode (if there is a reason not to have it in enforcing mode). In

    /etc/selinux/config

set

    SELINUX=permissive

If you have previously disabled SELinux, you will need to re-label the filesystem, since when SELinux is disabled, this does not happen for new files, and failing to relabel will likely cause many false positive issues. The easiest way to do that is to do the following as root:

    touch /.autorelabel
    reboot

This issue is being tracked in [//bugzilla.redhat.com/show_bug.cgi?id=954188 Red Hat Bugzilla bug #954188

<Category:Documentation>
