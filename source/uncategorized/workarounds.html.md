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

See [Workaround_archive](Workaround_archive) for workarounds that we believe to be resolved. Please move them back here if they appear to still be necessary.

These are the current workarounds for the RDO Juno.

## Packstack fails when mariadb-server is installed

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1148578>
*   **Affects:** Fedora 20

#### symptoms

    192.168.122.48_mysql.pp:                          [ ERROR ]        
    Applying Puppet manifests                         [ ERROR ]

    ERROR : Error appeared during Puppet run: 192.168.122.48_mysql.pp
    Error: Execution of '/usr/bin/yum -d 0 -e 0 -y install mariadb-galera-server' returned 1: Error: mariadb-galera-server conflicts with 1:mariadb-server-5.5.39-1.fc20.x86_64
    You will find full trace in log /var/tmp/packstack/20141001-084831-5JyrbC/manifests/192.168.122.48_mysql.pp.log
    Please check log file /var/tmp/packstack/20141001-084831-5JyrbC/openstack-setup.log for more information

#### workaround

Remove the mariadb-server package: yum remove mariadb-server

And rerun packstack

## packstack --allinone fails on Centos7 with: ERROR : Cinder's volume group 'cinder-volumes' could not be created.

*   Bug: <https://bugzilla.redhat.com/show_bug.cgi?id=1148552>

#### symptoms

packstack --allinone fails on Centos7 with: ERROR : Cinder's volume group 'cinder-volumes' could not be created.

#### workaround

    dd if=/dev/zero of=cinder-volumes bs=1 count=0 seek=2G && losetup /dev/loop2 cinder-volumes && pvcreate /dev/loop2 && vgcreate cinder-volumes /dev/loop2

## rabbitmq-server does not start

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1148604>
*   **Affects:** Fedora 21

#### symptoms

Packstack fails with the following error

    Applying 192.168.1.127_mysql.pp
    192.168.1.127_amqp.pp:                            [ ERROR ]       
    Applying Puppet manifests                         [ ERROR ]

    ERROR : Error appeared during Puppet run: 192.168.1.127_amqp.pp
    Error: Could not start Service[rabbitmq-server]: Execution of '/sbin/service rabbitmq-server start' returned 1: Redirecting to /bin/systemctl start  rabbitmq-server.service
    You will find full trace in log /var/tmp/packstack/20141002-113627-IM6EYW/manifests/192.168.1.127_amqp.pp.log
    Please check log file /var/tmp/packstack/20141002-113627-IM6EYW/openstack-setup.log for more information

#### workaround

You can upgrade to a version of erlang-sd_notify >= 0.1-4 from F21 updates-testing: <https://admin.fedoraproject.org/updates/FEDORA-2014-11943/erlang-sd_notify-0.1-4.fc21>

    yum --enablerepo=updates-testing  install -y erlang-sd_notify-0.1-4

After making the above change, re-run packstack with:

     packstack --answer-file=<generated packstack file>

## mariadb fails to start

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1141458>
*   **Affects:** Fedora 21

#### symptoms

Packstack fails with the following error

    192.168.1.127_mysql.pp:                           [ ERROR ]       
    Applying Puppet manifests                         [ ERROR ]

    ERROR : Error appeared during Puppet run: 192.168.1.127_mysql.pp
    Error: Could not start Service[mysqld]: Execution of '/sbin/service mariadb start' returned 1: Redirecting to /bin/systemctl start  mariadb.service
    You will find full trace in log /var/tmp/packstack/20141002-123813-7E6EEK/manifests/192.168.1.127_mysql.pp.log
    Please check log file /var/tmp/packstack/20141002-123813-7E6EEK/openstack-setup.log for more information

#### workaround

For now comment out the "plugin-load-add=ha_connect.so" line in /etc/my.cnf.d/connect.cnf.

    sed -i s/plugin-load-add=ha_connect.so/#plugin-load-add=ha_connect.so/ /etc/my.cnf.d/connect.cnf

After making the above change, re-run packstack with:

     packstack --answer-file=<generated packstack file>

## nova boot: failure creating veth devices

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=1149043>
*   **Affects:** Fedora 21

#### symptoms

Booting an instance fails and says 'No valid host was found.' In nova-compute.log:

     [instance: d9ad23e8-ebf6-4d21-9004-991f893fd500] ProcessExecutionError: Unexpected error while running command.
     [instance: d9ad23e8-ebf6-4d21-9004-991f893fd500] Command: sudo nova-rootwrap /etc/nova/rootwrap.conf ip link add qvb55064258-0a type veth peer name qvo55064258-0a
     [instance: d9ad23e8-ebf6-4d21-9004-991f893fd500] Exit code: 255
     [instance: d9ad23e8-ebf6-4d21-9004-991f893fd500] Stdout: ''
     [instance: d9ad23e8-ebf6-4d21-9004-991f893fd500] Stderr: 'Error: argument "qvb55064258-0a" is wrong: Unknown device\n'

#### workaround

Patch the /usr/lib/python2.7/site-packages/nova/network/linux_net.py file to give arguments that /usr/sbin/ip will accept:

    patch -b -d/ -p0  << EOF
    --- /usr/lib/python2.7/site-packages/nova/network/linux_net.py.orig     2014-10-02 17:00:19.620007092 -0400
    +++ /usr/lib/python2.7/site-packages/nova/network/linux_net.py  2014-10-02 16:40:31.514361599 -0400
    @@ -1316,7 +1316,7 @@
         for dev in [dev1_name, dev2_name]:
             delete_net_dev(dev)

    -    utils.execute('ip', 'link', 'add', dev1_name, 'type', 'veth', 'peer',
    +    utils.execute('ip', 'link', 'add', 'name', dev1_name, 'type', 'veth', 'peer',
                       'name', dev2_name, run_as_root=True)
         for dev in [dev1_name, dev2_name]:
             utils.execute('ip', 'link', 'set', dev, 'up', run_as_root=True)
    EOF

After making the above change, restart openstack services:

     openstack-service restart

## Example Problem Description

*   **Bug:** <https://bugzilla.redhat.com/show_bug.cgi?id=12345>
*   **Affects:** Operating Systems Affected

#### symptoms

Describe symptoms here

#### workaround

Describe how to work around the problem.
