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



