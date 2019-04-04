
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


The RPM-based installer uses Ansible installed via RPM packages to run playbooks and configuration files available on the local host.
	

Do not run OpenShift Ansible playbooks under nohup. Using nohup with the playbooks causes file descriptors to be created but not closed. Therefore, the system can run out of files to open and the playbook fails.

To run the RPM-based installer:

    Change to the playbook directory and run the prerequisites.yml playbook. This playbook installs required software packages, if any, and modifies the container runtimes. Unless you need to configure the container runtimes, run this playbook only once, before you deploy a cluster the first time:
~~~~~~~~~~~~~~~~~~~~
    $ cd /usr/share/ansible/openshift-ansible
    $ ansible-playbook [-i /path/to/inventory] \ 
      playbooks/prerequisites.yml
~~~~~~~~~~~~~~~~~~~~
    	If your inventory file is not in the /etc/ansible/hosts directory, specify -i and the path to the inventory file.

    Change to the playbook directory and run the deploy_cluster.yml playbook to initiate the cluster installation:

~~~~~~~~~~~~~~~~~~~~
    $ cd /usr/share/ansible/openshift-ansible
    $ ansible-playbook [-i /path/to/inventory] \
        playbooks/deploy_cluster.yml
~~~~~~~~~~~~~~~~~~~~
    	If your inventory file is not in the /etc/ansible/hosts directory, specify -i and the path to the inventory file.

. If your installation succeeded, verify the installation. If your installation failed, retry the installation.



===================================================================

hosts file for all in one


# Create an OSEv3 group that contains the masters, nodes, and etcd groups
[OSEv3:children]
masters
nodes
etcd

# Set variables common for all OSEv3 hosts
[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_ssh_user=root

# If ansible_ssh_user is not root, ansible_become must be set to true
#ansible_become=true

openshift_deployment_type=origin
openshift_disable_check=memory_availability,disk_availability,docker_image_availability,package_availability

# uncomment the following to enable htpasswd authentication; defaults to AllowAllPasswordIdentityProvider
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider'}]

# host group for masters
[masters]
jbonu.example.com

# host group for etcd
[etcd]
jbonu.example.com

# host group for nodes, includes region info
[nodes]
jbonu.example.com openshift_node_group_name='node-config-master'










