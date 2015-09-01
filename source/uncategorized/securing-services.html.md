---
title: Securing services
authors: jayg, rbowen, rcritten
wiki_title: Securing services
wiki_revision_count: 5
wiki_last_updated: 2013-12-02
---

# Securing services

__NOTOC__

## Overview

The default OpenStack configuration doesn't include securing internal services with SSL. This document provides manual instructions for securing some of the services.

## Preparation

You'll need a CA to issue server certificates, along with the CA certificate itself. Instructions for issuing certificates is beyond the scope of this document, but the subject should include CN=<fqdn> for SSL to work.

Depending on your host configuration, some services may be running on separate machines. Keep this in mind when restarting services.

The location of the certificates is not mandatory, but be aware of SELinux issues if you decide to locate them elsewhere.

It is bad practice to share the same server certificate between services.

## MySQL

*   Obtain a server certificate and private key, in PEM format with no password on the private key.

<!-- -->

    # openssl req -out /root/example.csr -new -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/<fqdn>-mysql.key
    # chown mysql  /etc/pki/tls/private/<fqdn>-mysql.key
    # restorecon  /etc/pki/tls/private/<fqdn>-mysql.key

*   Place the certificate file in /etc/pki/tls/certs/<fqdn>-mysql.crt, fix permissions

<!-- -->

    # chown mysql  /etc/pki/tls/certs/<fqdn>-mysql.crt
    # restorecon  /etc/pki/tls/certs/<fqdn>-mysql.crt

*   Place the private key in /etc/pki/tls/private/<fqdn>-mysql.key (if you didn't create it using the step above)
*   Place the CA certificate in /etc/pki/tls/CA/certs/ca.crt
*   Edit /etc/my.conf and point mysqld to the new cert and key:

<!-- -->

    [client]
    ssl_ca=/etc/pki/tls/CA/certs/ca.crt
    ...
    [mysqld]
    ssl_ca=/etc/pki/tls/CA/certs/ca.crt
    ssl_cert=/etc/pki/tls/certs/<fqdn>-mysql.crt
    ssl-key=/etc/pki/tls/private/<fqdn>-mysql.key

*   Restart mysqld:

<!-- -->

    [root@mysql ~]# service mysqld restart

*   Test that SSL is working:

<!-- -->

    [root@mysql ~]# mysql -e "\s"
    --------------
    mysql  Ver 14.14 Distrib 5.5.31, for Linux (x86_64) using readline 5.1

    Connection id:          487
    Current database:
    Current user:           root@localhost
    SSL:                    Cipher in use is DHE-RSA-AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.5.31 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             Localhost via UNIX socket
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    UNIX socket:            /var/lib/mysql/mysql.sock
    Uptime:                 2 sec

    Threads: 10  Questions: 791702  Slow queries: 0  Opens: 91  Flush tables: 1  Open tables: 84  Queries per second avg: 4.800

*   Configure the OpenStack services to use SSL. Set the SQL connection (either connection or sql_connection, depending on the service) . **Note**: that this also changes from addressing the host by IP address to FQDN:

<!-- -->

    connection = mysql://keystone_admin:a3a9e1219b81473e@mysql.example.com/keystone?ssl_ca=/etc/ipa/ca.crt

*   To these files (which may span several hosts):
    -   /etc/glance/glance-api.conf
    -   /etc/glance/glance-registry.conf
    -   /etc/cinder/cinder.conf
    -   /etc/nova/nova.conf
    -   /etc/keystone/keystone.conf

<!-- -->

*   Restart the affected OpenStack services

## qpid

Configuring qpidd does not currently work without making some manual changes, depending on the version of OpenStack you are running. nova and glance do not properly allow SSL configuration due to launchpad bug <https://bugs.launchpad.net/oslo/+bug/1158807> . It is a one-line change to make in the nova and cinder python files.

*   Install the qpid-cpp-server-ssl package

      # yum install qpid-cpp-server-ssl

*   Create a directory to store the NSS certificate database for qpidd

<!-- -->

    # mkdir /etc/pki/qpid
    # certutil -N -d /etc/pki/qpid (note the password you use)

*   Create a password file containing the password you set on the NSS database

<!-- -->

    # echo password > /etc/pki/qpid/password.conf

*   Fix permissions and ownership of files

<!-- -->

    # chown -R qpidd /etc/pki/qpid
    # chmod 600 /etc/pki/qpid/password.conf
    # restorecon /etc/pki/qpid/*

*   Request a certificate from this NSS database. The subject will depend on your PKI infrastructure. The issuing CA may change it.

<!-- -->

    # certutil -R -d /etc/pki/qpid -s "cn=<fqdn>,O=example.com -a

*   Provide this CSR to your CA. You'll get back a certificate, probably in PEM form.
*   Add the server certificate and CA certificate to your NSS database. The nickname(s) you use here are for your purposes. You can use something more descriptive as needed.

<!-- -->

    # certutil -A -n broker -d /etc/pki/qpid -t u,u,u -a -i /path/to/server.crt
    # certutil -A -n CA -d /etc/pki/qpid -t CT,, -a -i /etc/pki/tls/CA/certs/ca.crt
    * Add configuration options to /etc/qpidd.conf
    <pre>
    require-encryption=yes
    ssl-require-client-authentication=no
    ssl-cert-db=/etc/pki/qpid
    ssl-cert-password-file=/etc/pki/qpid/password.conf
    ssl-cert-name=broker
    ssl-port=5671

*   Restart qpid

<!-- -->

    # service qpid restart

*   Configure nova to use ssl in /etc/nova/nova.conf

      qpid_protocol=ssl
      qpid_port=5671

*   Configure cinder to use ssl in /etc/cinder/cinder.conf

      qpid_protocol=ssl
      qpid_port=5671

*   Restart the nova and cinder services
