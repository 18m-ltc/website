---
title: Workarounds
authors: apevec, arifali, avladu, dustymabe, eglynn, ekuris, eyepv6, fbayhan, flaper87,
  hma, iovadia, jpichon, jruzicka, kashyap, larsks, mmagr, pixelbeat, rbowen, rdo,
  rohara, rwmjones, sasha, shardy, sschinna, stoner, stzilli, tshefi, vaneldik, weshayutin,
  whayutin, xqueralt
wiki_title: Workarounds
wiki_revision_count: 100
wiki_last_updated: 2015-05-07
---

# Workarounds

## Active

### Comparison of string with 7 failed

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1117035>
*   **Affects:** CentOS 7

##### symptoms

prescript.pp, fails with the following error message:

         Comparison of String with 7 failed

##### workaround

Edit files in /usr/lib/python2.7/site-packages/packstack/puppet/templates/ where operatingsystemrelease is compared to 7. Change operatingsystemrelease to operatingsystemmajrelease. Run packstack again with the same answerfile.

### Could not enable mysqld

*   bug: <https://bugzilla.redhat.com/show_bug.cgi?id=1138701>
*   **Affects:** CentOS 7

##### symptoms

         ERROR : Error appeared during Puppet run: 127.0.0.1_mysql.pp
         Error: Could not enable mysqld: 

##### workaround

         # rm /usr/lib/systemd/system/mysqld.service 
         # cp /usr/lib/systemd/system/mariadb.service /usr/lib/systemd/system/mysqld.service
         # systemctl stop mariadb
         # pkill mysql
         # rm -f /var/lib/mysql/mysql.sock

Then run packstack again with the generated answer file from the last time.

### Could not find command 'restorecon'

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1109079>
*   **Affects:** CentOS 6.5

##### symptoms

ERROR : Error appeared during Puppet run: 10.16.67.27_swift.pp

         Error:
        /Stage[main]//Swift::Storage::Loopback[swift_loopback]/Swift::Storage::Ext4
         [swift_loopback]/Swift::Storage::Mount[swift_loopback]/Exec[restorecon_mount
         _swift_loopback]: Failed to call refresh: Could not find command 'restorecon'

##### workaround

Change line in /usr/share/openstack-puppet/modules/swift/manifests/storage/mount.pp:68

           path        => ['/usr/sbin', '/bin'],

to

           path        => ['/usr/sbin', '/sbin'],

### /usr/sbin/tuned-adm profile virtual-host returned 2 instead of one of [0]

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1041350>
*   **Upstream Bug:** <https://review.openstack.org/#/c/68371/>
*   **Affects:** Fedora 19

##### symptoms

ERROR : Error during puppet run : Error: /usr/sbin/tuned-adm profile virtual-host returned 2

##### workaround

Your mileage may vary, but I found this issue disappeared after I started doing a full update **before** installing openstack, packstack etc.

       sudo yum --enablerepo=updates-testing clean all
       sudo yum update -y

jruzicka: I always fully \`yum update\` my VM as part of provisioning so this is definitely **not working** for me.

xqueralt: installing and enabling the tuned service before running packstack solved the issue for me.

##### more info

      # service tuned status
       tuned.service - Dynamic System Tuning Daemon
         Loaded: loaded (/usr/lib/systemd/system/tuned.service; disabled)
         Active: active (running) since Út 2013-09-10 13:35:22 EDT; 1min 22s ago
       Main PID: 16101 (tuned)
         CGroup: name=systemd:/system/tuned.service
                 └─16101 /usr/bin/python -Es /usr/sbin/tuned -l -P
      zář 10 13:35:22 rdo-havana systemd[1]: Starting Dynamic System Tuning Daemon...
      zář 10 13:35:22 rdo-havana systemd[1]: Started Dynamic System Tuning Daemon.

      # /usr/sbin/tuned-adm profile virtual-host
      2013-09-10 13:44:22,474 ERROR    dbus.proxies: Introspect error on :1.245:/Tuned: dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NoReply: Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.
      ERROR:dbus.proxies:Introspect error on :1.245:/Tuned: dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NoReply: Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.
      DBus call to Tuned daemon failed (org.freedesktop.DBus.Error.NoReply: Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.).

*   setenforce 0 doesn't help.
*   dbus is running

hma: I have the exact same problem. My tuned is up and I did "yum update -y". but, the problem is still persistent. I am following RDO quick start to install OpenStack (Grizzly) on Fedora 19 which is on VMware (VMware player). I also changed IP address in /etc/libvirt/qemu/networks/default.xml as other post suggests, But, it does not help.

mmagr: don't forget to also restart tuned.service, After restart it always solved this issue for me.

### heat.conf auth_uri is incorrectly set leading to api-cfn authentication errors

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1106394> (FIX PROPOSED)
*   **Affects:** RHEL OSP & RDO (Icehouse) deployed with Packstack

##### symptoms

The auth_uri option within heat.conf defaults to auth_uri=<http://127.0.0.1:5000/v2.0/ec2tokens> in packstack deployed environments. This leads to the following authentication errors in api-cfn.log :

[Rdo](User:Rdo) ([talk](User talk:Rdo)) 2014-06-04 03:52:12.346 1178 INFO heat.api.aws.ec2token [-] Checking AWS credentials.. 2014-06-04 03:52:12.347 1178 INFO heat.api.aws.ec2token [-] AWS credentials found, checking against keystone. 2014-06-04 03:52:12.348 1178 INFO heat.api.aws.ec2token [-] Authenticating with <http://127.0.0.1:5000/v2.0/ec2tokens/ec2tokens> 2014-06-04 03:52:12.350 1178 INFO urllib3.connectionpool [-] Starting new HTTP connection (1): 127.0.0.1 2014-06-04 03:52:12.356 1178 INFO heat.api.aws.ec2token [-] AWS authentication failure. [Rdo](User:Rdo) ([talk](User talk:Rdo))

This error then leads to no autoscaling taking place.

##### workaround

Switching this to auth_uri=<http://127.0.0.1:5000/v2.0> removes these errors and allows stacks to autoscale.

Note: IP address needs to be set to the machine running the Keystone API, not necessarily 127.0.0.1

### heat-engine fail to start

*   **Bug:** <https://bugzilla.redhat.com/1006911> (FIXED)
*   **Bug:** <https://bugzilla.redhat.com/1007497>
*   **Affects:** all (Havana)

##### symptoms

*   Starting heat-engine: [FAILED]
*   2013-09-11 15:02:04.115 14992 ERROR heat.openstack.common.threadgroup [-] (ProgrammingError) (1146, "Table 'heat.stack' doesn't exist") 'SELECT stack.created_at AS stack_created_at, stack.updated_at AS stack_updated_at, stack.deleted_at AS stack_deleted_at, stack.id AS stack_id, stack.name AS stack_name, stack.raw_template_id AS stack_raw_template_id, stack.username AS stack_username, stack.tenant AS stack_tenant, stack.action AS stack_action, stack.status AS stack_status, stack.status_reason AS stack_status_reason, stack.parameters AS stack_parameters, stack.user_creds_id AS stack_user_creds_id, stack.owner_id AS stack_owner_id, stack.timeout AS stack_timeout, stack.disable_rollback AS stack_disable_rollback \\nFROM stack \\nWHERE stack.deleted_at IS NULL AND stack.owner_id IS NULL' ()

##### workaround

packstack does not sync the DB in install, you can manually work around via:

1.  heat-manage db_sync
2.  systemctl start openstack-heat-engine.service

### /usr/sbin/service mongod start returned 2 instead of one of [0]

*   **Bug:** related to <https://bugzilla.redhat.com/824405#c2>,

` `[`https://bugzilla.redhat.com/967284#c3`](https://bugzilla.redhat.com/967284#c3)

*   **Affects:** Fedora 19 (on resource-constrained VMs)

##### symptoms

The eager mongodb journal file pre-allocation takes longer than the default 90 second timeout imposed by systemd.

##### workaround

Pre-install mongod before running packstack, editing the systemd config for mongod to override the default timeout.

         sudo yum install -y mongodb-server mongodb

         # add the line TimeoutStartSec=360 to the [Service] section:
         sudo vi /usr/lib/systemd/system/mongod.service

         sudo service mongod start
         sudo service mongod status
         sudo service mongod stop

### cinder: volume lifecycle not metered

*   **Affects:** All

##### symptoms

Newly created volumes are not metered:

       cinder create 1 ; ceilometer meter-list | grep volume 

##### workaround

The root cause of the problem is that cinder does not override the control_exchange config option from the oslo standard "openstack" setting. This config setting (normally set directly in the code) can be specified as follows:

       sudo openstack-config --set /etc/cinder/cinder.conf DEFAULT control_exchange cinder
       sudo service openstack-cinder-volume restart

## Resolved

### openvswitch fails to start

*   **Bug:** [1006412](https://bugzilla.redhat.com/show_bug.cgi?id=1006412)
*   **Affects:** Fedora 19
*   **Status:** **CLOSED ERRATA**

##### symptoms

      A dependency job for openvswitch.service failed. See 'journalctl -xn' for details.
      Dependency failed for Open vSwitch Unit.

##### workaround

      mkdir /var/lock/subsys

### Could not start Service[ovs-cleanup-service]

*   **Bug:** [1006342](https://bugzilla.redhat.com/show_bug.cgi?id=1006342) (should be solved)
*   **Affects:** Fedora 19

##### symptoms

      ERROR : Error during puppet run : Error: Could not start Service[ovs-cleanup-service]: Execution of '/sbin/service neutron-ovs-cleanup start' returned 1:

##### workaround

      yum install -y python-pbr

### Could not prefetch glance_image provider 'glance'

*   **Bug:** [1017421](https://bugzilla.redhat.com/show_bug.cgi?id=1017421)
*   **Affects:** Fedora 19, RHEL 6.4, RHEL7
*   **Status:** **CLOSED WORKSFORME**

##### symptoms

      ERROR : Error during puppet run : Error: Could not prefetch glance_image provider 'glance': Execution of '/usr/bin/glance -T services -I glance -K da3cb42f56224b0

##### workaround

Just run packstack once again on the generated answer file, for example:

      packstack --answer-file packstack-answers-20130910-113355.txt

##### more info

<dneary>`  Seems like it's a time out on testing Puppet module application which is funky.`

<rbowen>` The workaround didn't work for me - I get that same failure repeatedly on subsequent runs.`

<tshefi> My workaround openstack-status -> openstack-glance-api: failed, started Glance API service and reran packstack.

### glance: Error communicating with <http://192.168.8.96:9292> timed out

*   **Bug:** <https://bugzilla.redhat.com/1006484>
*   **Affects:** Fedora 19
*   **Status:** **CLOSED ERRATA**

##### symptoms

      ERROR : Error during puppet run : Error: Execution of '/usr/bin/glance -T services -I glance -K 8ecff593f85544c1 -N `[`http://192.168.8.96:35357/v2.0/`](http://192.168.8.96:35357/v2.0/)` add name=cirros is_public=Yes container_format=bare disk_format=qcow2 copy_from=`[`http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-disk.img`](http://download.cirros-cloud.net/0.3.1/cirros-0.3.1-x86_64-disk.img)`' returned 1: Error communicating with `[`http://192.168.8.96:9292`](http://192.168.8.96:9292)` timed out

Check if glance is hitting AVCs on connecting to qpid:

      # grep 'WARNING qpid.messaging' /var/log/glance/api.log
      2013-09-10 11:56:30.573 28749 WARNING qpid.messaging [-] recoverable error[attempt 1]: [Errno 13] EACCES
      2013-09-10 11:56:30.574 28749 WARNING qpid.messaging [-] sleeping 1 seconds
      2013-09-10 11:56:31.574 28749 WARNING qpid.messaging [-] trying: localhost:5672
      2013-09-10 11:56:31.577 28749 WARNING qpid.messaging [-] recoverable error[attempt 2]: [Errno 13] EACCES
      2013-09-10 11:56:31.577 28749 WARNING qpid.messaging [-] sleeping 2 seconds
      2013-09-10 11:56:33.577 28749 WARNING qpid.messaging [-] trying: localhost:5672
      2013-09-10 11:56:33.579 28749 WARNING qpid.messaging [-] recoverable error[attempt 3]: [Errno 13] EACCES
      2013-09-10 11:56:33.580 28749 WARNING qpid.messaging [-] sleeping 4 seconds
      2013-09-10 11:56:37.583 28749 WARNING qpid.messaging [-] trying: localhost:5672
      ...

##### workaround

Disabling glance notifications:

      sudo sed -i 's/notifier_strategy = qpid/notifier_strategy = noop/' /etc/glance/glance-api.conf
      sudo service openstack-glance-api restart

### heat-api and heat-engine fail to start

*   **Bug:** <https://bugzilla.redhat.com/1006868>
*   **Affects:** RHEL/SLC 6.4
*   **Status:** **CLOSED ERRATA**

##### symptoms

*   ERROR : Error during puppet run : Error: Could not start Service[heat-api]: Execution of '/sbin/service openstack-heat-api start' returned 6
*   ERROR : Error during puppet run : Error: Could not start Service[heat-engine]: Execution of '/sbin/service openstack-heat-engine start' returned 6:

##### workaround

init scripts still expect heat-api.conf and heat-engine.conf to be present.

      # cd /etc/heat
      # ln -s heat.conf heat-api.conf
      # ln -s heat.conf heat-engine.conf

### horizon: no image format

*   **Bug:** [988316](https://bugzilla.redhat.com/988316) (ON_QA),

       `[`1006483`](https://bugzilla.redhat.com/1006483)` (CLOSED ERRATA)

*   **Affects:** all (Havana)

##### symptoms

The dropdown list in the 'Create An Image" is empty and no image can be created.

##### workaround

Edit /etc/openstack-dashboard/local_settings and add the following in the end:

      OPENSTACK_IMAGE_BACKEND = {
         'image_formats': [
             ('', ''),
             ('aki', _('AKI - Amazon Kernel Image')),
             ('ami', _('AMI - Amazon Machine Image')),
             ('ari', _('ARI - Amazon Ramdisk Image')),
             ('iso', _('ISO - Optical Disk Image')),
             ('qcow2', _('QCOW2 - QEMU Emulator')),
             ('raw', _('Raw')),
             ('vdi', _('VDI')),
             ('vhd', _('VHD')),
             ('vmdk', _('VMDK'))
         ]
      }

### horizon: internal error accessing the login page

*   **Bug:** [988316](https://bugzilla.redhat.com/988316) (ON_QA)
*   **Affects:** Fedora 19

##### symptoms

Just after installing with packstack it's impossible to access the login page of the dashboard which shows a generic error page.

##### workaround

Edit /etc/openstack-dashboard/local_settings and add one of the following in the end:

      ALLOWED_HOSTS = ['*'] 

or

      ALLOWED_HOSTS = ['`<host ip>`']

### horizon: logs are empty even in case of internal server error

*   **Bug:** [LP#1236423](https://bugs.launchpad.net/horizon/+bug/1236423). This should be fixed in packstack when [this review](https://review.openstack.org/#/c/51222/) is merged.
*   **Status:** **Fix Released**

##### symptoms

The logs in /var/log/httpd and /var/log/horizon do not contain any useful information or traceback, even when the UI fails with an Error 500 / Internal Server Error.

##### workaround

Edit /etc/openstack-dashboard/local_settings and add the 'django' logger to the LOGGING dictionary, after 'nose.plugins.managers':

             'nose.plugins.manager': {
                 'handlers': ['file'],
                 'propagate': False,
             },
             'django': {
                 'handlers': ['file'], # This should match the handler name used in the other 'loggers'
                 'level': 'DEBUG',
                 'propagate': False,
             },

You should then restart httpd.

Here's an example of a [working logging configuration](https://github.com/openstack/horizon/blob/3ccd927251a69905b2c3f1ee496c174eaeb1f8eb/openstack_dashboard/local/local_settings.py.example#L241).

### nova: instances started in error state

*   '''Bug: <https://bugzilla.redhat.com/show_bug.cgi?id=1090605> ''
*   **Affects:** all distros

##### workaround

Edit /etc/nova/nova.conf

      vif_plugging_is_fatal:  false
      vif_plugging_timeout: 0

##### symptoms

Instances launched in error state
