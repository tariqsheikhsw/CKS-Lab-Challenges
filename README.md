# CKS-Lab-Challenges
CKS-Lab-Challenges

Practice CKS Challenge Labslink: 
https://kodekloud.com/courses/cks-challenges/

# CKS Challenge Lab - 1

Here's the summary of activities performed during this lab :

- :radio_button: Task1 - PVC to PV binding
- :radio_button: Task2 - Image Scanning using Aquasec Trivy
- :radio_button: Task3 - Ingress and Egress Network Policy Implementation
- :radio_button: Task4 - Secure Deployment using AppArmor Profile
- :radio_button: Task5 - Expose Deployment with 'ClusterIP' Type Service

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

Final alpha-xyz.yaml file : https://github.com/tariqsheikhsw/CKS-Lab-Challenges/blob/main/CKS-Challenge-1/alpha-xyz.yaml

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

## FINAL STATUS:

- [✔️] PVC to PV binding
- [✔️] Image Scanning using Aquasec Trivy
- [✔️] Ingress and Egress Network Policy Implementation
- [✔️] Secure Deployment using AppArmor Profile
- [✔️] Expose Deployment with NodePort Type Service

# CKS Challenge Lab - 2

Here's the summary of activities performed during this lab :

- Edit and Build Docker Image using Dockerfile
- Inspect and fix security issues using kubesec

![image](https://user-images.githubusercontent.com/54164634/189633811-21148382-67be-4d8f-b11d-adde4e390f41.png)

## Edit and Build Docker Image using Dockerfile

```
docker build -t kodekloud/webapp-color:stable .
```

## Inspect and fix security issues using kubesec
which kubesec

kubesec scan staging-webapp.yaml

![image](https://user-images.githubusercontent.com/54164634/189636584-39055a7f-a54e-4c2c-acef-7f8492110a1b.png)

![image](https://user-images.githubusercontent.com/54164634/189636939-2e5f1998-00f3-4ad8-9afe-3ff694c0fa60.png)

```
k get pod -n dev

k replace --force -f dev-webapp.yaml -n dev

k replace --force -f staging-webapp.yaml -n staging
```

```
k exec -it dev-webapp -n dev -- sh

k exec -it staging-webapp -n staging -- sh
```

```
k apply -f prod-np.yaml
```

```
k get deployments.apps prod-web -n prod -oyaml > prod-web.yaml

cat prod-web.yaml | grep -A 7 "env:"

kubectl create secret generic prod-db --from-literal DB_Host=prod-db --from-literal DB_User=root --from-literal DB_Password=paswrd -n prod

k describe secrets prod-db 

k edit deployments.apps prod-web -n prod

k get pod -n prod
```

# CKS Challenge Lab - 3

 ![image](https://user-images.githubusercontent.com/54164634/189655541-0d019a03-902b-4c7c-8516-99a127ebd7ac.png)

curl -L https://github.com/aquasecurity/kube-bench/releases/download/v0.6.2/kube-bench_0.6.2_linux_amd64.tar.gz -o kube-bench_0.6.2_linux_amd64.tar.gz

tar -xvf kube-bench_0.6.2_linux_amd64.tar.gz

mkdir -p /var/www/html/

./kube-bench run --config-dir /opt/cfg --config /opt/cfg/config.yaml > /var/www/html/index.html

cd /var/www/html/
cat index.html |egrep "INFO|FAIL"

ps -ef |grep kubelet
/var/lib/kubelet/config.yaml

cat /var/lib/kubelet/config.yaml

$$$ staticPodPath: /etc/kubernetes/manifests

cd  /etc/kubernetes/manifests

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


rictl ps -a

kubectl config set-context --current --namespace kube-system

ls -l /var/lib/ | grep etcd

chown etcd:etcd /var/lib/etcd
!

ps -ef |grep kubelet

/var/lib/kubelet/config.yaml 

vi /var/lib/kubelet/config.yaml 
!
protectKernelDefaults: true

systemctl restart kubelet

k get nodes
ssh node01

ps -ef |grep kubelet
vi /var/lib/kubelet/config.yaml 


vi kube-controller-manager.yaml 
- --profiling=false

vi kube-scheduler.yaml 
- --profiling=false

crictl ps -a

systemctl restart kubelet

# CKS Challenge Lab - 4

![image](https://user-images.githubusercontent.com/54164634/189811410-e8de8186-41a7-4adb-9733-7150d6b6e6ad.png)

- Configure Auditing using Audio Policy
- Apply auditing to kube-apiserver 
- Install & Analyze Falco 

Check ConfigMaps/Roles for Citadel Namespace
```
k get all -n citadel

k get cm -n citadel

k get role -n citadel
```
## Configure Auditing using Audio Policy

Audit Policy:

```
/etc/kubernetes/audit-policy.yaml
```

## Apply auditing to kube-apiserver 

Modify kube-apiserver 

```
cd /etc/kubernetes/manifests/
vi kube-apiserver.yaml
```

Make Changes
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

Check kube-apiserver Status
```
systemctl restart kubelet
crictl ps | grep api
journalctl | grep apiserver
```

## Install & Analyze Falco 

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

```
crictl ps
crictl pods

systemctl restart kubelet 

systemctl restart falco
systemctl status falco
```

```
cd /etc/falco
ls
vim falco.yaml

file_output:
  enabled:  true
  keep_alive: false
  filename: /opt/falco.log
```

```
systemctl restart falco
systemctl status falco
```

```
cd /var/log/kubernetes/audit/
ls -al
//audit.log 

cat audit.log |grep citadel |egrep -v "\"get|\"watch|\"list" |jq
```

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

```
echo "agent-smith,important_role_do_not_delete,important_binding_do_not_delete" > /opt/blacklist_users
```

```
cat falco.log 

05:32:46.651495067: Error Package management process launched in container (user=root user_loginuid=-1 command=apt install nginx container_id=e23544847bbf container_name=k8s_eden-software2_eden-software2_eden-prime_07eb74da-63d8-4ac5-8310-ab49fa93cbc6_0 image=ubuntu:latest)
```
```
//container_id=e23544847bbf

crictl ps |grep "e23544847bbf"
```

```
crictl pods| grep "91efe3fe8bed6"
root@controlplane /opt ➜  crictl pods| grep "91efe3fe8bed6"
91efe3fe8bed6       54 minutes ago       Ready               eden-software2                         eden-prime          0                   (default)
```

```
echo "eden-prime,eden-software2" > /opt/compromised_pods
```

```
k delete pod eden-software2 -n eden-prime
```

```
k get role -n citadel
k get rolebinding -n citadel
```

```
k delete role important_role_do_not_delete -n citadel
k delete rolebinding important_binding_do_not_delete -n citadel 
```




