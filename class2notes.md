
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






