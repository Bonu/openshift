#### Install Docker 

```sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
```

type: EOF

```#sudo yum install docker-engine -y
```

Run the docker service in RHEL
`#dockerd`

Seach docker repository
`#docker search ubuntu`

CNCF- Cloud Native Computing Foundation
https://www.cncf.io/

```#docker images
#docker pull ubuntu
#docker pull centos

#docker run -it ubuntu 
it=>interactive
```
--------------------break-------------------------

https://docs.openshift.com/container-platform/3.5/architecture/index.html

All kernels are created in C language

Docker is a container and Openshift is a plaform. Docker creates a container.

Kubernetes has projects and projects has pods. A pod is a sandbox with multiple containers in it.

```#kubectl get projects
# oc get projects
```

-------------------------break-------------------------
`#yum repolist`

-repository 

```[root@localhost ~]# cat /etc/redhat-release 
CentOS Linux release 7.6.1810 (Core) 

#docker container ls

#docker run -d ubuntu // -d runs os on deamon
```

Docker container Ubuntu uses resources(Kernel, IO, RAM, HDD) of the host machine.

Windows container cannot run on Linux

Dual boot Partition
Hardware -> OS1K
         -> OS2K
         
Virtualization
Hardware -> OSK1 -> VMware -> OS1K, OS2K, ... OSKN

Container
Hardware -> OSK1 -> Docker -> OS1, OS2, ...OSN

OSK = OperatingSystem + Kernel
OS = OperatingSystem without Kernel

-----------------------------------break--------------------------------
```#oc get projects
#oc project default
#oc logs projectname
#oc get events - problamatic events
#oc logs -f bc/ruby-ex
#oc get pods
#oc logs <podname>
```
podman uses QUAY registry

SDN - software defined networking

`#oc describe pod <podname> // to see the content of the pod`

--------------------------------EOD----------------------------------



        



