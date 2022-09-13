# CKS-Lab-Challenges
CKS-Lab-Challenges

# CKS Challenge Lab - 1

![image](https://user-images.githubusercontent.com/54164634/189648588-04da9975-54f4-4134-921a-0bdfcf1adbfb.png)

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

Kubectl autocomplete
BASH

source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.

You can also use a shorthand alias for kubectl that also works with completion:

alias k=kubectl
complete -o default -F __start_kubectl k


![image](https://user-images.githubusercontent.com/54164634/189614849-3360c01d-8908-4b8c-9ab9-22a92725d413.png)

![image](https://user-images.githubusercontent.com/54164634/189616447-ba8486ef-b5dc-4fba-8678-d89d0ea4f8bd.png)

k edit pvc -n alpha
 
k apply --force -f /tmp/kubectl-edit-668393621.yaml


![image](https://user-images.githubusercontent.com/54164634/189616574-ca6cbe46-98f4-4476-a56c-8100ba8bfdb1.png)

docker images | grep nginx

docker images | grep nginx |awk '{print $1 ":" $2}' |sort -u

which trivy

trivy image --help

trivy image --severity=CRITICAL bitnami/nginx:latest

trivy image --severity=CRITICAL nginx:1.13

trivy image --severity=CRITICAL nginx:1.14

trivy image --severity=CRITICAL nginx:1.16

trivy image --severity=CRITICAL nginx:1.17

trivy image --severity=CRITICAL nginx:alpine

trivy image --severity=CRITICAL nginx:latest



k apply -f alpha-xyz.yaml

![image](https://user-images.githubusercontent.com/54164634/189618304-84c50ad1-5263-40b7-a129-98b9f55a5e87.png)

 kubectl expose deploy alpha-xyz --name alpha-svc --port 80 --target-port 80 --type ClusterIP --namespace=alpha --dry-run=client -oyaml > alpha-svc.yaml
 
 k apply -f alpha-svc.yaml 
 
 k get svc -n alpha
 
 ![image](https://user-images.githubusercontent.com/54164634/189619316-b4ae95ad-af5e-4e10-96e3-1a078a859f14.png)
 
 k apply -f enp.yaml
 
 ![image](https://user-images.githubusercontent.com/54164634/189620467-9f952599-273b-4ab5-af91-2ba0a8eeab40.png)

k apply -f inp.yaml

![image](https://user-images.githubusercontent.com/54164634/189620938-ad934c5b-40d1-44bd-b23b-4dd4575f184d.png)

cp /root/usr.sbin.nginx /etc/apparmor.d/usr.sbin.nginx
 
k replace --force -f alpha-xyz.yaml
 
vi usr.sbin.nginx

apparmor_status | grep custom-nginx

apparmor_parser -a /etc/apparmor.d/usr.sbin.nginx

# CKS Challenge Lab - 2

![image](https://user-images.githubusercontent.com/54164634/189633811-21148382-67be-4d8f-b11d-adde4e390f41.png)

docker build -t kodekloud/webapp-color:stable .

which kubesec

kubesec scan staging-webapp.yaml

![image](https://user-images.githubusercontent.com/54164634/189636584-39055a7f-a54e-4c2c-acef-7f8492110a1b.png)

![image](https://user-images.githubusercontent.com/54164634/189636939-2e5f1998-00f3-4ad8-9afe-3ff694c0fa60.png)

k get pod -n dev

k replace --force -f dev-webapp.yaml -n dev

k replace --force -f staging-webapp.yaml -n staging

k exec -it dev-webapp -n dev -- sh

k exec -it staging-webapp -n staging -- sh

k apply -f prod-np.yaml

k get deployments.apps prod-web -n prod -oyaml > prod-web.yaml

cat prod-web.yaml | grep -A 7 "env:"

kubectl create secret generic prod-db --from-literal DB_Host=prod-db --from-literal DB_User=root --from-literal DB_Password=paswrd -n prod

k describe secrets prod-db 

k edit deployments.apps prod-web -n prod

k get pod -n prod

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






