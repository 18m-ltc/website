---
title: Deploy Heat and launch your first Application
authors: bluesman, dneary, gfidente, rbowen, sbaker, sdake, shardy, vaneldik, zaneb
wiki_title: Deploy Heat and launch your first Application
wiki_revision_count: 47
wiki_last_updated: 2013-10-01
---

# Deploy Heat and launch your first Application

__NOTOC__

## Deploy OpenStack Heat on RHEL (and derivatives)

[Heat](http://wiki.openstack.org/wiki/Heat) provides orchestration of composite cloud applications using the CloudFormation API and templates; it is an incubated project of OpenStack. Its development cycle is to be Integrated in Havana and follow the full OpenStack release process.

> Heat is a service to orchestrate multiple composite cloud applications using the AWS CloudFormation template format, through both an OpenStack-native ReST API and a CloudFormation-compatible Query API.

This guide deploys a composite application (made up of more than a single instance) on the cloud infrastructure. This also involves launch-time customization of the VMs. This guide makes some assumptions:

*   OpenStack has already been configured via PackStack as described in the QuickStart guide
*   OpenStack is deployed on RHEL 6.4

### Installation

Start by installing the required packages for Heat to work:

      $ sudo yum install "openstack-heat-*" python-heatclient

You'll get four new services installed: an engine, a native api, a cloudformation compatible api, a cloudwatch compatible api. You don't have to deploy them all on a single host but for the purpose of this guide it will be fine to do so.

python-heatclient installs a python CLI tool "heat", which is used to interact with the heat native API

### Configuration

Heat comes with a script which creates (and populates) the needed database for it to work but you need to know your MySQL's `root` account password. If you've used PackStack, than that is saved as `CONFIG_MYSQL_PW` in the answers file (`/root/packstack-answers*` by default). Now run the prepare script:

      $ sudo heat-db-setup rpm -y -r ${MYSQL_ROOT_PASSWORD} -p ${HEAT_DB_PASSWORD_OF_CHOICE}

Check in `/etc/heat/heat-engine.conf` that your database connection string is correct::

      sql_connection = mysql://heat:${HEAT_DB_PASSWORD}@localhost/heat

`/etc/heat/heat-engine.conf` contains a placeholder value for `auth_encryption_key` which needs to be replaced with a randomly generated key::

      ` sed -i "s/%ENCRYPTION_KEY%/`hexdump -n 16 -v -e '/1 "%02x"' /dev/random`/" /etc/heat/heat-engine.conf `

Now go through the usual steps needed to create a new user, service and endpoint with Keystone and don't forget to source the admin credentials before starting (which are in `/root/keystonerc_admin` if you've used PackStack)::

      $ keystone user-create --name heat --pass ${HEAT_USER_PASSWORD_OF_CHOICE} --tenant-id ${SERVICES_TENANT_ID}
      $ keystone user-role-add --user heat --role admin --tenant ${SERVICES_TENANT_NAME}
      $ keystone service-create --name heat --type orchestration
      $ keystone service-create --name heat-cfn --type cloudformation
      $ keystone endpoint-create --region RegionOne --service-id ${HEAT_CFN_SERVICE_ID} --publicurl "`[`http://`](http://)`${HEAT_CFN_HOSTNAME}:8000/v1" --adminurl "`[`http://`](http://)`${HEAT_CFN_HOSTNAME}:8000/v1" --internalurl "`[`http://`](http://)`${HEAT_CFN_HOSTNAME}:8000/v1"
      $ keystone endpoint-create --region RegionOne --service-id ${HEAT_SERVICE_ID} --publicurl "`[`http://`](http://)`${HEAT_HOSTNAME}:8004/v1/%(tenant_id)s" --adminurl "`[`http://`](http://)`${HEAT_HOSTNAME}:8004/v1/%(tenant_id)s" --internalurl "`[`http://`](http://)`${HEAT_HOSTNAME}:8004/v1/%(tenant_id)s"

Note: `${HEAT_HOSTNAME}` should be replaced by the hostname or IP address of your Heat host, while `%(tenant_id)` should remain literally as is in these commands. The various service IDs may be obtained by running the `keystone service-list` command.

For Grizzly, update the paste files at `/etc/heat/heat-api{,-cfn,-cloudwatch}-paste.ini` with the credentials just created::

      admin_tenant_name = ${SERVICES_TENANT_NAME}
      admin_user = heat
      admin_password = ${HEAT_USER_PASSWORD}

For Havana, update the config files at `/etc/heat/heat-api{,-cfn,-cloudwatch}.conf` with the credentials just created::

      admin_tenant_name = ${SERVICES_TENANT_NAME}
      admin_user = heat
      admin_password = ${HEAT_USER_PASSWORD}

**Note**: Check your value for ${SERVICES_TENANT_NAME} carefully, the packstack default tenant is "**services**", whereas some other install methods (which use /usr/share/openstack-keystone/sample_data.sh) name it "**service**"

In there you also need to make sure that the following variables are pointing to your Keystone host (127.0.0.1 should just work if you've used PackStack as Keystone is probably installed on the same host)::

      service_host = ${KEYSTONE_HOSTNAME}
      auth_host = ${KEYSTONE_HOSTNAME}
      auth_uri = `[`http://`](http://)`${KEYSTONE_HOSTNAME}:35357/v2.0
      keystone_ec2_uri = `[`http://`](http://)`${KEYSTONE_HOSTNAME}:5000/v2.0/ec2tokens

In `/etc/heat/heat-engine.conf` you've to make instead sure that the following variables \*\*do not\*\* point to 127.0.0.1 even though the services are actually hosted on the same system because URLs will be passed over to the VMs, which don't have them available locally::

      heat_metadata_server_url = `[`http://`](http://)`${HEAT_CFN_HOSTNAME}:8000
      heat_waitcondition_server_url = `[`http://`](http://)`${HEAT_CFN_HOSTNAME}:8000/v1/waitcondition
      heat_watch_server_url = `[`http://`](http://)`${HEAT_CLOUDWATCH_HOSTNAME}:8003

The application templates can use wait conditions and signaling for the orchestration, Heat needs to create special users to receive the progress data and these users are, by default, given the role of `heat_stack_user`. You can configure the role name in `heat-engine.conf` or just create a so called role::

      $ keystone role-create --name heat_stack_user

The configuration should now be complete and the services can be started::

      $ sudo -c "cd /etc/init.d && for s in $(ls openstack-heat-*); do chkconfig $s on && service $s start; done"

Make sure by checking the logs that everything was started successfully. Specifically, in case the engine service reports `ImportError: cannot import name Random` then you're probably using an old version of `pycrypto`. A fix has been merged upstream to workaround the issue. It's [a trivial change](https://review.openstack.org/#/c/26759/) which you can apply manually to `heat/common/crypt.py`.

### Get the demo files

It is time now to launch your first multi-instance cloud application! There are a number of sample templates available in the [github repo](https://github.com/openstack/heat), download the composed Wordpress example with::

`$ wget `[`https://raw.github.com/openstack/heat-templates/master/cfn/WordPress_Composed_Instances.template`](https://raw.github.com/openstack/heat-templates/master/cfn/WordPress_Composed_Instances.template)

Every template also provides you with a list of usable distros and map these into an AMI string, for each arch. You will have to populate Glance with an image matching the AMI string that the template file is expecting to find.

Now you would need to create JEOS image and you can do it through the `heat_jeos.sh` script from : <https://github.com/openstack/heat-templates> . This can be used to create the JEOS images and upload them to Glance but there is also a collection of prebuilt images at: <http://fedorapeople.org/groups/heat/prebuilt-jeos-images/> so I suggest you to just download one from `F17-x86_64-cfntools.qcow2` or `U10-x86_64-cfntools.qcow2` (which are referred by many if not all the templates available in the Heat's repo). To upload the F17 x86_64 image in Glance::

`$ glance image-create --name F17-x86_64-cfntools --disk-format qcow2 --container-format bare --is-public True --copy-from `[`http://fedorapeople.org/groups/heat/prebuilt-jeos-images/F17-x86_64-cfntools.qcow2`](http://fedorapeople.org/groups/heat/prebuilt-jeos-images/F17-x86_64-cfntools.qcow2)

While that is downloading, create a new keypair or upload your public key in nova to make sure you'll be able to login on the VMs using SSH::

      $ nova keypair-add --pub_key ~/.ssh/id_rsa.pub userkey

Some templates require the instances to be able to connect to the heat CFN API, so it would be a good idea to add a chain to iptables to accept connection to 8000 and 8003 ports so that the guests can communicate with the heat-api-cfn server and heat-api-cloudwatch server. You can append to your `/etc/sysconfig/iptables` ::

      $ -A INPUT -i br100 -p tcp --dport 8000 -j ACCEPT
      $ -A INPUT -i br100 -p tcp --dport 8003 -j ACCEPT

where `br100` is the interface of the bridge that your instances are using.

### Launch!

It is time for the real fun now, launch your first composed application with::

      $ heat stack-create wordpress --template-file=WordPress_Composed_Instances.template --parameters="DBUsername=wp;DBPassword=wp;KeyName=userkey;LinuxDistribution=F17"

More parameters could have passed, note for instance the LinuxDistribution parameter discussed above. Now the interesting stuff:

      $ heat stack-list
      $ heat event-list wordpress

After the VMs are launched, the mysql/httpd/wordpress installation and configuration begins, the process is driven by the `cfntools`, installed in the VMs images. It will take quite some time, despite the `event-list` reporting completion for the WordPress install too early (there is signaling, via `cfn-signal`, only in the MySQL template). You can login on the instances and check the logs or just use `ps` to see how things are going. After some minutes the setup should be finished:

      $ heat stack-show wordpress
      $ wget ${WebsiteURL} // that is an URL from the previous command!

If anything goes wrong, check the logs at `/var/log/heat/engine.log` or look at the scripts passed as `UserData` to the instances, these should be found in `/var/lib/cloud/data/`. Time to hack your very own template and delete the test deployment! :)
