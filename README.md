# CKS-Lab-Challenges
CKS-Lab-Challenges

Practice CKS Challenge Labslink: 
https://kodekloud.com/courses/cks-challenges/




# CKS Challenge Lab - 1

Here's the summary of activities performed during this lab :

:radio_button: Task1 - PVC to PV binding  
:radio_button: Task2 - Image Scanning using Aquasec Trivy  
:radio_button: Task3 - Ingress and Egress Network Policy Implementation  
:radio_button: Task4 - Secure Deployment using AppArmor Profile  
:radio_button: Task5 - Expose Deployment with 'ClusterIP' Type Service  

## Scenario / Architecture

![image](https://user-images.githubusercontent.com/54164634/189648588-04da9975-54f4-4134-921a-0bdfcf1adbfb.png)

## Basic Environment Pre-requisites
https://kubernetes.io/docs/reference/kubectl/cheatsheet/

Kubectl autocomplete
BASH
```
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```

You can also use a shorthand alias for kubectl that also works with completion:
```
alias k=kubectl
complete -o default -F __start_kubectl k
```

## Task1 - PVC to PV binding

PersistentVolume 'alpha-pv' has already been created. Inspect parameters i.e. 'accessModes' & 'capacity' and modify PVC accordingly

![image](https://user-images.githubusercontent.com/54164634/189614849-3360c01d-8908-4b8c-9ab9-22a92725d413.png)

![image](https://user-images.githubusercontent.com/54164634/189616447-ba8486ef-b5dc-4fba-8678-d89d0ea4f8bd.png)


Apply the changes and ensure PVC status changes from 'pending' to 'Bound'
```
k edit pvc -n alpha
 
k apply --force -f /tmp/kubectl-edit-668393621.yaml
```

![image](https://user-images.githubusercontent.com/54164634/189616574-ca6cbe46-98f4-4476-a56c-8100ba8bfdb1.png)

## Task2 - Image Scanning using Trivy

List '6' NGINX images 
```
docker images | grep nginx

docker images | grep nginx |awk '{print $1 ":" $2}' |sort -u
```

Scan each image and identify the image with 'least number of CRITICAL vulnerabilites'
```
which trivy

trivy image --help

trivy image --severity=CRITICAL bitnami/nginx:latest

trivy image --severity=CRITICAL nginx:1.13

trivy image --severity=CRITICAL nginx:1.14

trivy image --severity=CRITICAL nginx:1.16

trivy image --severity=CRITICAL nginx:1.17

trivy image --severity=CRITICAL nginx:alpine

trivy image --severity=CRITICAL nginx:latest
```

Docker Image: nginx:alpine has 0 CRITICAL vulnerabilites. Modify 'alpha-xyz' deployment to use 'nginx:alpine' image and apply the configuration


```
k apply -f alpha-xyz.yaml
```

![image](https://user-images.githubusercontent.com/54164634/189618304-84c50ad1-5263-40b7-a129-98b9f55a5e87.png)

## Task3 - Expose Deployment with 'ClusterIP' Type Service

Expose 'alpha-xyz' deployment as a 'ClusterIP' type service
```
kubectl expose deploy alpha-xyz --name alpha-svc --port 80 --target-port 80 --type ClusterIP --namespace=alpha --dry-run=client -oyaml > alpha-svc.yaml
 
k apply -f alpha-svc.yaml 
 
k get svc -n alpha
```
 
 ![image](https://user-images.githubusercontent.com/54164634/189619316-b4ae95ad-af5e-4e10-96e3-1a078a859f14.png)
 
## Task4 - Ingress and Egress Network Policy Implementation

Restrict external POD from accessing 'alpha-svc' on port 80
```
k apply -f enp.yaml
```
 
 ![image](https://user-images.githubusercontent.com/54164634/189620467-9f952599-273b-4ab5-af91-2ba0a8eeab40.png)

Allow Inbound access only from 'middleware' POD
```
k apply -f inp.yaml
```

![image](https://user-images.githubusercontent.com/54164634/189620938-ad934c5b-40d1-44bd-b23b-4dd4575f184d.png)


## Task5 - Secure Deployment using AppArmor Profile

Move AppArmor profile to given location on controlplane node. Load and Enforce AppArmor profile 'custom-nginx'

```
cp /root/usr.sbin.nginx /etc/apparmor.d/usr.sbin.nginx
 
k replace --force -f alpha-xyz.yaml
 
cat usr.sbin.nginx | grep profile

apparmor_parser -a /etc/apparmor.d/usr.sbin.nginx
```

Verify AppArmor status
```
apparmor_status | grep custom-nginx
```

## CONFIGURATION FILES:

:link: Deployment ['alpha-xyz'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/alpha-xyz.yaml)  
:link: Service ['alpha-svc'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/alpha-svc.yaml)  
:link: NetworkPolicy ['restrict-inbound'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/inp.yaml)  
:link: NetworkPolicy ['external-network-policy'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/enp.yaml)  
:link: AppArmor Profile ['custom-nginx'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/usr.sbin.nginx)  

## FINAL STATUS:

- [✔️] PVC to PV binding
- [✔️] Image Scanning using Aquasec Trivy
- [✔️] Ingress and Egress Network Policy Implementation
- [✔️] Secure Deployment using AppArmor Profile
- [✔️] Expose Deployment with NodePort Type Service




# CKS Challenge Lab - 2

Here's the summary of activities performed during this lab :

:radio_button: Task1 - Edit and Build Docker Image using Dockerfile  
:radio_button: Task2 - Inspect and fix security issues using kubesec  
:radio_button: Task3 - Use startupProbe to remove shell access  
:radio_button: Task4 - Access Secret using environment variables within deployment  
:radio_button: Task5 - Implement Ingress Network Policy  

## Scenario / Architecture

![image](https://user-images.githubusercontent.com/54164634/189633811-21148382-67be-4d8f-b11d-adde4e390f41.png)

## Task1 - Edit and Build Docker Image using Dockerfile

Copy the required files (app.py,requirements.txt,templates) and build docker image
```
docker build -t kodekloud/webapp-color:stable .
```

## Task2 - Inspect and fix security issues using kubesec

Scan YAML using kubesec

```
which kubesec

kubesec scan staging-webapp.yaml
```

![image](https://user-images.githubusercontent.com/54164634/189636584-39055a7f-a54e-4c2c-acef-7f8492110a1b.png)

![image](https://user-images.githubusercontent.com/54164634/189636939-2e5f1998-00f3-4ad8-9afe-3ff694c0fa60.png)

## Task3 - Use startupProbe to remove shell access

Re-create 'dev-webapp' and 'staging-webapp' PODS after fixing security issues
```
k get pod -n dev

k replace --force -f dev-webapp.yaml -n dev

k replace --force -f staging-webapp.yaml -n staging
```

Try shell access and see it failing with 'exit' status
```
k exec -it dev-webapp -n dev -- sh

k exec -it staging-webapp -n staging -- sh
```

## Task4 - Access Secret using environment variables within deployment

Create Generic Secret 'prod-db' and access Secret using envFrom within deployment 'prod-web'
```
k get deployments.apps prod-web -n prod -oyaml > prod-web.yaml

cat prod-web.yaml | grep -A 7 "env:"

kubectl create secret generic prod-db --from-literal DB_Host=prod-db --from-literal DB_User=root --from-literal DB_Password=paswrd -n prod

k describe secrets prod-db 

k edit deployments.apps prod-web -n prod

k get pod -n prod
```

## Task5 - Implement Ingress Network Policy

Create NetworkPolicy 'prod-netpol' and allow traffic only within 'prod' namespace. Deny traffic from other namespaces.

```
k apply -f prod-np.yaml
```

## CONFIGURATION FILES:

:link: Dockerfile ['Dockerfile'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-2/Dockerfile)  
:link: POD ['dev-webapp'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-2/dev-webapp.yaml)  
:link: POD ['staging-webapp'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-2/staging-webapp.yaml)  
:link: Deployment ['prod-web'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-2/prod-web.yaml)  
:link: NetworkPolicy ['prod-netpol'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-2/prod-np.yaml)  

## FINAL STATUS:

- [✔️] Edit and Build Docker Image using Dockerfile
- [✔️] Inspect and fix security issues using kubesec
- [✔️] Use startupProbe to remove shell access
- [✔️] Access Secret using environment variables within deployment
- [✔️] Implement Ingress Network Policy





# CKS Challenge Lab - 3

## Scenario / Architecture

 ![image](https://user-images.githubusercontent.com/54164634/189655541-0d019a03-902b-4c7c-8516-99a127ebd7ac.png)



Here's the summary of activities performed during this lab :

:radio_button: Task1 - Use AquaSec 'kube-bench' to identify and fix issues related to controlplane and work node components  
:radio_button: Task2 - Inspect and fix kube-apiserver auditing issues  
:radio_button: Task3 - Fix kubelet security issues  
:radio_button: Task4 - Inspect and fix etcd / kube-controller-manager / kube-scheduler security issues  

. 

## Task1 - Use AquaSec 'kube-bench' to identify and fix issues related to controlplane and work node components

Install 'kube-bench' tool

```
curl -L https://github.com/aquasecurity/kube-bench/releases/download/v0.6.2/kube-bench_0.6.2_linux_amd64.tar.gz -o kube-bench_0.6.2_linux_amd64.tar.gz
tar -xvf kube-bench_0.6.2_linux_amd64.tar.gz
mkdir -p /var/www/html/
```

Run 'kube-bench'

```
./kube-bench run --config-dir /opt/cfg --config /opt/cfg/config.yaml > /var/www/html/index.html
```

Identify 'failed' issues 

```
cd /var/www/html/
cat index.html |egrep "INFO|FAIL"
```

## Task2 - Inspect and fix kube-apiserver auditing issues

Kubelet 
```
ps -ef |grep kubelet
/var/lib/kubelet/config.yaml

cat /var/lib/kubelet/config.yaml

$$$ staticPodPath: /etc/kubernetes/manifests

cd  /etc/kubernetes/manifests
```

Apply fixes as diagnosed using 'kube-bench' tool

```
vi kube-apiserver.yaml
parameters:

- --profiling=false
- --enable-admission-plugins=NodeRestriction,PodSecurityPolicy
- --insecure-port=0

  - mountPath: /var/log/apiserver/
    name: audit-log
    readOnly: false

  - name: audit-log
    hostPath:
      path: /var/log/apiserver/
      type: DirectoryOrCreate


    - --audit-log-path=/var/log/apiserver/audit.log
    - --audit-log-maxage=30
    - --audit-log-maxbackup=10
    - --audit-log-maxsize=100
```

Ensure process is running post changes
```
crictl ps -a
```


## Task3 - Fix kubelet security issues

Apply fixes on both controlplane and workernode 

```
ps -ef |grep kubelet

/var/lib/kubelet/config.yaml 

vi /var/lib/kubelet/config.yaml 
!
protectKernelDefaults: true
```

```
systemctl restart kubelet
```

```
k get nodes
ssh node01

ps -ef |grep kubelet
vi /var/lib/kubelet/config.yaml 
```

## Task4 - Inspect and fix etcd / kube-controller-manager / kube-scheduler security issues

Fix ETCD issues

```
kubectl config set-context --current --namespace kube-system

ls -l /var/lib/ | grep etcd

chown etcd:etcd /var/lib/etcd
```

Fix Kube Controller Manager issues

```
vi kube-controller-manager.yaml 
- --profiling=false
```

Fix Kube Scheduler issues

```
vi kube-scheduler.yaml 
- --profiling=false
```

Restart kubelet and ensure all services are up and running after fixing security issues

```
crictl ps -a

systemctl restart kubelet
```

## FINAL YAML FILES:

:link: etcd ['etcd.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-3/etcd.yaml)  
:link: kubelet-config ['kubelet-config.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-3/kubelet-config.yaml)   
:link: kube-apiserver ['kube-apiserver.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-3/kube-apiserver.yaml)   
:link: kube-scheduler ['kube-scheduler.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-3/kube-scheduler.yaml)    
:link: kube-controller-manager ['kube-controller-manager.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-3/kube-controller-manager.yaml)  

## FINAL STATUS:

[✔️] Task1 - Use AquaSec 'kube-bench' to identify and fix issues related to controlplane and work node components    
[✔️] Task2 - Inspect and fix kube-apiserver auditing issues    
[✔️] Task3 - Fix kubelet security issues    
[✔️] Task4 - Inspect and fix etcd / kube-controller-manager / kube-scheduler security issues    





# CKS Challenge Lab - 4

## Scenario / Architecture
![image](https://user-images.githubusercontent.com/54164634/189811410-e8de8186-41a7-4adb-9733-7150d6b6e6ad.png)

:radio_button: Task1 - Configure Auditing using Audit Policy  
:radio_button: Task2 - Apply auditing to kube-apiserver   
:radio_button: Task3 - Install & Analyze Falco   


Check ConfigMaps/Roles for Citadel Namespace
```
k get all -n citadel

k get cm -n citadel

k get role -n citadel
```
## Task1 - Configure Auditing using Audio Policy

Audit Policy:

Create Single rule in audit policy as per the requirement give at path /etc/kubernetes/audit-policy.yaml'

```
/etc/kubernetes/audit-policy.yaml
```

## Task2 - Apply auditing to kube-apiserver 

Modify kube-apiserver and create required mountPaths

```
cd /etc/kubernetes/manifests/
vi kube-apiserver.yaml
```

Make Changes as required in given scenario
```
- --audit-policy-file=/etc/kubernetes/audit-policy.yaml
- --audit-log-path=/var/log/kubernetes/audit/audit.log

    - mountPath: /etc/kubernetes/audit-policy.yaml
      name: audit
      readOnly: true
    - mountPath: /var/log/kubernetes/audit/
      name: audit-log
      readOnly: false

  - name: audit
    hostPath:
      path: /etc/kubernetes/audit-policy.yaml
      type: File
  - name: audit-log
    hostPath:
      path: /var/log/kubernetes/audit/
      type: DirectoryOrCreate
```

Check kube-apiserver Status and ensure it is running
```
systemctl restart kubelet
crictl ps | grep api
journalctl | grep apiserver
```

## Task3 - Install & Analyze Falco 

Install Falco Utility and start it as systemd service
```
cat /etc/os-release
```
URL : https://falco.org/docs/getting-started/installation/#debian

```
curl -s https://falco.org/repo/falcosecurity-3672BA8F.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update -y
apt-get -y install linux-headers-$(uname -r)
apt-get install -y falco
```

Check process status
```
crictl ps
crictl pods

systemctl restart kubelet 

systemctl restart falco
systemctl status falco
```

Configure Falco to save event output to given path: /opt/falco.log

```
cd /etc/falco
ls
vim falco.yaml

file_output:
  enabled:  true
  keep_alive: false
  filename: /opt/falco.log
```

Restart once changes have been made

```
systemctl restart falco
systemctl status falco
```

Insect API server audit logs and identify user causing abnormal behaviour
```
cd /var/log/kubernetes/audit/
ls -al
//audit.log 

cat audit.log |grep citadel |egrep -v "\"get|\"watch|\"list" |jq
```

Find the name of the 'user', 'role' and 'rolebinding' responsible for the event

```
k get sa -n citadel
k get role -n citadel
```

```
k get role important_role_do_not_delete -n citadel

k get rolebindings.rbac.authorization.k8s.io -n citadel
```

```
k get rolebindings.rbac.authorization.k8s.io important_binding_do_not_delete -n citadel

k get rolebindings.rbac.authorization.k8s.io important_binding_do_not_delete -n citadel -oyaml
```

Save the name of the 'user', 'role' and 'rolebinding' responsible for the event to the file '/opt/blacklist_users' file 

```
echo "agent-smith,important_role_do_not_delete,important_binding_do_not_delete" > /opt/blacklist_users
```

Inspect the 'falco' logs and identify the pod that has events generated because of packages being updated on it
```
cat falco.log 

05:32:46.651495067: Error Package management process launched in container (user=root user_loginuid=-1 command=apt install nginx container_id=e23544847bbf container_name=k8s_eden-software2_eden-software2_eden-prime_07eb74da-63d8-4ac5-8310-ab49fa93cbc6_0 image=ubuntu:latest)
```
Identify the container ID
```
//container_id=e23544847bbf

crictl ps |grep "e23544847bbf"
```

Identify the namespace and pod name
```
crictl pods| grep "91efe3fe8bed6"
root@controlplane /opt ➜  crictl pods| grep "91efe3fe8bed6"
91efe3fe8bed6       54 minutes ago       Ready               eden-software2                         eden-prime          0                   (default)
```

Save the namespace and pod name to file '/opt/compromised_pods' 
```
echo "eden-prime,eden-software2" > /opt/compromised_pods
```

Delete the POD belonging to the 'omega' namespace that were flagged in the 'Security Report' file '/opt/compromised_pods'

```
k delete pod eden-software2 -n eden-prime
```

Identify and delete the role and rolebinding causing the constant deletion and creation of the configmaps and pods in this namespace
```
k get role -n citadel
k get rolebinding -n citadel
```

Take Action
```
k delete role important_role_do_not_delete -n citadel
k delete rolebinding important_binding_do_not_delete -n citadel 
```



## CONFIGURATION FILES:

:link: Audit Policy ['audit-policy.yaml'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-4/audit-policy.yaml)  
:link: KUBE API Server ['kube-apiserver'](https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-4/kube-apiserver.yaml)  

## FINAL STATUS:

[✔️] Task1 - Configure Auditing using Audit Policy  
[✔️] Task2 - Apply auditing to kube-apiserver   
[✔️] Task3 - Install & Analyze Falco   


## Tariq A Sheikh  

:link: LinkedIn: https://www.linkedin.com/in/tariq-a-sheikh/  
:link: Credly: https://www.credly.com/users/tariqsheikh


