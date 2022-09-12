# CKS-Lab-Challenges
CKS-Lab-Challenges

CKS Challenge Lab - 1

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


k apply --force -f /tmp/kubectl-edit-668393621.yaml


![image](https://user-images.githubusercontent.com/54164634/189616574-ca6cbe46-98f4-4476-a56c-8100ba8bfdb1.png)

k apply -f alpha-xyz.yaml

![image](https://user-images.githubusercontent.com/54164634/189618304-84c50ad1-5263-40b7-a129-98b9f55a5e87.png)

 kubectl expose deploy alpha-xyz --name alpha-svc --port 80 --target-port 80 --type ClusterIP --namespace=alpha --dry-run=client -oyaml > alpha-svc.yaml
 
 ![image](https://user-images.githubusercontent.com/54164634/189619316-b4ae95ad-af5e-4e10-96e3-1a078a859f14.png)
 
 k apply -f enp.yaml
 
 ![image](https://user-images.githubusercontent.com/54164634/189620467-9f952599-273b-4ab5-af91-2ba0a8eeab40.png)


