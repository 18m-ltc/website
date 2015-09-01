---
title: Deploying RDO to a Virtual Machine Environment using RDO via Instack
authors: bnemec, ccrouch, gfidente, jomara, rbrady, slagle, sradvan
wiki_title: Deploying RDO to a Virtual Machine Environment using RDO via Instack
wiki_revision_count: 85
wiki_last_updated: 2014-10-31
---

# Deploying RDO to a Virtual Machine Environment using RDO via Instack

[ ← Deploying RDO using Instack](Deploying RDO using Instack)

If you are connecting to the virtual host remotely using ssh, you will need to use the -t flag to force pseudo-tty allocation or enable notty via a $USER.notty file.

Do not use the root user for executing any instack-undercloud scripts. Some programs of libguestfs-tools are not designed to work with the root user. All of the instack-undercloud scripts were developed and tested by using a normal user with sudo privileges.

## Minimum System Requirements

This setup creates five (5) virtual machines consisting of 2GB of memory and 30GB of disk space on each. If you do not plan to deploy Block Storage or Swift Storage nodes, you can delete those virtual machines and allocate less space accordingly. Most of the virtual machine disk files are thinly provisioned and will not take up the full 30GB. The undercloud is not thinly provisioned and is completely pre-allocated.

The minimum system requirements for the virtual host machine to follow this tutorial are:

*   Be running Fedora 20 x86_64
*   At least (1) quad core CPU
*   12GB free memory
*   200GB disk space [1]

If you want to deviate from the tutorial or increase the scaling of one or more overcloud nodes, you will need to ensure you have enough memory and disk space.

[1]: Note that the default Fedora partitioning scheme will most likely not provide enough space to the partition containing the default path for libvirt image storage (/var/lib/libvirt/images). The easiest fix is to customize the partition layout at the time of install to provide at least 200 GB of space for that path.

## Preparing the Host Machine

1. Make sure sshd service is installed and running.

2. The user performing all of the installation steps on the virt host needs to have password-less sudo enabled. **This step is NOT optional, you must create an additional user. Do not run the rest of the steps as root.**

       sudo useradd stack
       sudo passwd stack  # specify a password
       echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
       sudo chmod 0440 /etc/sudoers.d/stack

If you have previously used the host machine to run TripleO's devtest setup, then that could potentially conflict with the scripts installed from RDO packages. It is recommended to clean up from any previous devtest runs by deleting ~/.cache/tripleo and making sure there is no $TRIPLEO_ROOT defined in your environment.

### Virtual Host Setup

1. Make sure you are logged in as the user you created above.

2. The virtual host machine needs SELinux set to permissive mode. You can immediately set the mode and also update the configuration file to be persistent across reboots:

        # set selinux to permissive
        sudo setenforce 0
        # update the config file to survive reboots
        sudo sed -i "s/=enforcing/=permissive/" /etc/selinux/config

3. Add export of LIBVIRT_DEFAULT_URI to your bashrc file.

        echo 'export LIBVIRT_DEFAULT_URI="qemu:///system"' >> ~/.bashrc

4. Enable the RDO icehouse repository

`  sudo yum install -y `[`http://rdo.fedorapeople.org/openstack-icehouse/rdo-release-icehouse.rpm`](http://rdo.fedorapeople.org/openstack-icehouse/rdo-release-icehouse.rpm)

5. Install instack-undercloud package

       sudo yum install -y instack-undercloud

6. Run script to install required dependencies

       sudo yum install -y libguestfs-tools systemd
       source /usr/libexec/openstack-tripleo/devtest_variables.sh
       tripleo install-dependencies

**After running this command, you will need to log into a new shell for the changes to be applied**.

### Virtual Machine Creation

1. Make sure you are logged in, with a new shell, as the user you created above.

2. Run the script to setup your virtual environment.

       instack-virt-setup

Running "virsh list --all" will show you now have one virtual machine called "instack" and four related to "baremetal", all shut off. The "instack" vm runs a minimal install of Fedora 20 x86_64 and will be used to install the undercloud on. The vm contains a user "stack" that uses the password "stack" and is granted password-less sudo privileges. The root password is displayed in the standard output. The other vm's don't have an operating system on but will eventually become part of the "overcloud".

3. Get IP Address of VM for undercloud

You will need to start the instack virtual machine and obtain its IP address. Note that the second command will not return anything until the instance has finished booting.

      virsh start instack
      cat /var/lib/libvirt/dnsmasq/default.leases | grep $(tripleo get-vm-mac instack) | awk '{print $3;}'

4. Get MAC addresses of all other VMs

When setting up the undercloud on the instack virtual machine, you will need the MAC addresses of the bare metal node virtual machines. Use the following command to obtain the list of addresses. Later, you will need these MAC addresses to add to your deploy-overcloudrc file later.

       for i in $(seq 0 3); do echo -n $(tripleo get-vm-mac baremetal_$i) " "; done; echo

Next steps: [ Deploying an RDO Undercloud with Instack ](Deploying an RDO Undercloud with Instack)
