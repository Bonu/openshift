check status of OKD : 
`oc status`

oc whoami
system:admin // cannot login to console, it doesn't have password

Create user using CLI
oc create user <<openshift>>

htpasswd -b /etc/origin/master/htpasswd <<username>> <<pwd>>
htpasswd -b /etc/origin/master/htpasswd openshift openshift


cd /etc/origin/master/

vi /etc/hosts

make an entry 192.168.1.33


rushil1234@gmail.com

```oc create serviceaccount jenkins
oc new-project dev --display-name="Tasks-Dev"
co new-project stage --display-name="Tasks-Stage"
co new-project cicd --display-name="CI/CD"
co policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n dev
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n stage
git clone https://github.com/OpenShiftDemos/openshift-cd-demo.git
cd openshift-cd-demo/
oc create -f cicd-template.yaml 
oc new-app --template=cicd
```
--> create account in in your gog pod and then login with the account and migrate the sample git repo.(https://github.com/OpenShiftDemos/openshift-tasks.git) into that. gog pod.
--> after that go to jenkins pod and change the configuration :

for example----------snippet--------------------------------------------
 stage ('Build') {
     git branch: 'eap-7', url: 'http://gogs:3000/username/<reponame>.git'
-------------------------------------------------------------------------

