---
title: Fedora 19
authors: rbowen
wiki_title: Fedora 19
wiki_revision_count: 9
wiki_last_updated: 2013-08-19
---

# Fedora 19

## mysql vs mariadb

Because community-mysql requires mysql-libs not community-mysql-libs (I didn't check the SPEC of community-mysql), yum installs mariadb-libs instead of mysql-libs on F19. It's better to replace mysql with mariadb in /usr/lib/python2.7/site-packages/packstack/puppet/modules/mysql/manifests/params.pp as follows.

    $client_package_name   = 'mariadb'
    $server_package_name   = 'mariadb-server'
