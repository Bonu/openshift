
Uninstall docker from CentOS
```# yum list installed | grep -i docker
# sudo yum remove docker-ce-cli.x86_64
# sudo yum remove containerd.io.x86_64
```

Set new hostname
`# hostnamectl set-hostname jbonu.example.com`

EPEL : Extra Packages for Enterprise Linux

OKD - Fedora

## RHEL/CentOS 7 64-Bit ##
```# wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# rpm -ivh epel-release-latest-7.noarch.rpm
# yum update
```

Create key

`[root@jbonu ~]# ssh-keygen`

Send key to nodes

`ssh-copy-id jbonu.example.com`


Base packages required for installation of cloud

```yum install -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-utils sos psacct
```

The OKD installer requires a user that has access to all hosts. If you want to run the installer as a non-root user, first configure passwordless sudo rights each host:

1>    Generate an SSH key on the host you run the installation playbook on:

    # ssh-keygen

    Do not use a password.

	

 2>   Distribute the key to the other cluster hosts. You can use a bash loop:

    # for host in master.example.com \     * 
        node1.example.com \                            *
        node2.example.com; \                           *
        do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; \
        done

    	- * Provide the host name for each cluster host.

    Confirm that you can access each host that is listed in the loop through SSH.



Installing base packages
	

If your hosts use RHEL 7.5 and you want to accept OKD’s default docker configuration (using OverlayFS storage and all default logging options), do not manually install these packages. These packages are installed when you run the prerequisites.yml playbook during installation.

If your hosts use RHEL 7.4 or if they use RHEL 7.5 and you want to customize the docker configuration, install these packages.
For RHEL 7 systems:

    Install the following base packages:

    # yum install  -y wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct

    Update the system to the latest packages:

    # yum update
    # reboot

    Install packages that are required for your installation method:

        If you plan to use the containerized installer, install the following package:

        # yum install atomic


        If you plan to use the RPM-based installer:

            Install Ansible. To use EPEL as a package source for Ansible:

                Install the EPEL repository:

                # yum -y install \
                    https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

                Disable the EPEL repository globally so that it is not accidentally used during later steps of the installation:

                # sed -i -e "s/^enabled=1/enabled=0/" /etc/yum.repos.d/epel.repo

                Install the packages for Ansible:

                # yum -y --enablerepo=epel install ansible pyOpenSSL

            Clone the openshift/openshift-ansible repository from GitHub, which provides the required playbooks and configuration files:

            # cd ~
            # git clone https://github.com/openshift/openshift-ansible
            # cd openshift-ansible
            # git checkout release-3.11



Installing Docker

At this point, install Docker on all master and node hosts. This allows you to configure your Docker storage options before you install OKD.
	

The cluster installation process automatically modifies the /etc/sysconfig/docker file.
For RHEL 7 systems:

    Install Docker 1.13:

    # yum install docker-1.13.1

    Verify that version 1.13 was installed:

    # rpm -V docker-1.13.1
    # docker version

Start or restart Docker.

    If Docker has never run on the host, enable and start the service, then verify that it is running:

    # systemctl enable docker
    # systemctl start docker
    # systemctl is-active docker



INVENTORY FILE VARIABLES: 
       https://docs.okd.io/3.11/install/configuring_inventory_file.html



INVENTORTY HOSTS FILE:


Single Master, Single etcd, and Multiple Nodes

The following table describes an example environment for a single master (with a single etcd instance running as a static pod on the same host), two nodes for hosting user applications, and two nodes with the node-role.kubernetes.io/infra=true label for hosting dedicated infrastructure:

Host Name 	Component/Role(s) to Install
	master.example.com

Master, etcd, and node
	node1.example.com
	
Compute node
	node2.example.com
	infra-node1.example.com
	
Infrastructure node
	infra-node2.example.com

You can see these example hosts present in the [masters], [etcd], and [nodes] sections of the following example inventory file:
Single Master, Single etcd, and Multiple Nodes Inventory File


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`

See Configuring Node Host Labels to ensure you understand the default node selector requirements and node label considerations beginning in OKD 3.9.

To use this example, modify the file to match your environment and specifications, and save it as /etc/ansible/hosts.



Running the RPM-based installer





