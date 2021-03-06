# rear-automated-testing
Relax-and-Recover (ReaR) Automated Testing is using Vagrant and standard GNU/Linux boxes to deploy one server and one client virtual machine (VM), and besides a recover VM is also created, but not provisioned. The main goal of this project is to foresee in a simple way to test the latest snapshot release of ReaR without too many manual interactions by doing a full backup with an automated restore into the *recover* VM.

In short we start the *client* and *server* VM via vagrant, and do a provisioning if required so that the *server* VM is capable of being a NFS server, PXE server and TFTP server. Then, we install the latest **ReaR** snapshot in the *client* VM and run a full backup using the *NETFS* backup method (with the help of `tar`). The tar archive is stored on the *server* VM and in the same time the PXE environment is configured on the *server* VM as well (when using KVM/libvirt), or a PXE environment is configured on the host system when using virtualbox..
When the ReaR backup is completed we halt the *client* VM and start the *recover* VM and do a full restore of the *client* content. Once the restore is completed the *recover* VM reboot automatically.

## Clone this Git repository

`$ git clone https://github.com/gdha/rear-automated-testing.git`

## Execute the automated ReaR Recovery test

Select the GNU/Linux OS to test by going into the proper directory. However, initially we only have **centos7**, but more will follow over time (and do not forget it goes much faster with *sponsoring*).

````
$ cd rear-automated-testing
$ sudo ./rear-automated-test.sh -h

+--------------------------------------------------+
|    Relax-and-Recover Automated Testing script    |
|             version 1.0                          |
+--------------------------------------------------+

Author: Gratien D'haese
Copyright: GPL v3


Usage: rear-automated-test.sh [-d distro] [-b <boot method>] [-s <server IP>] [-p provider] [-c rear-config-file.conf] [-t test] -vh
        -d: The distribution to use for this automated test (default: centos7)
        -b: The boot method to use by our automated test (default: PXE)
        -s: The <boot server> IP address (default: 192.168.33.15)
        -p: The vagrant <provider> to use (default: virtualbox)
        -c: The ReaR config file we want to use with this test (default: PXE-booting-example-with-URL-style.conf)
        -t: The ReaR validation test directory (see tests directory; no default)
        -h: This help message.
        -v: Revision number of this script.

Comments:
--------
<distro>: select the distribution you want to use for these testings
<boot method>: select the rescue image boot method (default PXE) - supported are PXE and ISO
<boot server>: is the server where the PXE or ISO images resides on (could be the hypervisor or host system)
<provider>: as we use vagrant we need to select the provider to use (virtualbox, libvirt)
<rear-config-file.conf>: is the ReaR config file we would like to use to drive the test scenario with (optional with PXE)
<test-dir>: under the tests/ directory there are sub-directories with the beakerlib tests (donated by RedHat).
       When -t option is used then we will not execute an automated recover test (at least no yet)

````

### Using KVM/libvirt

To use this tool on a KVM/libvirt Linux system and as CentOS7 and PXE are the default (so far) we only require one parameter and that is `-p libvirt` as virtualbox was choosen as the default provider.

### Using VirtualBox

To use this tool on a VirtualBox Linux system and as CentOS7 and PXE are the default (so far) we do not need additional parameters as it will try to set-up and FTPboot area on the host system itself. However, it is imported that the hosts system is a NFS server as the client VM will try to mount the TFTboot area. Why do we need the host system? That is because VirtualBox uses a NAT network to PXE boot from and that is always pointing the host system (a pity we cannot use the server VM).


