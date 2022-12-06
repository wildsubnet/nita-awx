# nita-awx

This is a work in progress and represents the files used to create an running AWX running along side an existing NITA (docker based) install. This is being used to test the AWX execution environment. AWX requires kubernetes.

## Folders

<b>ansible-ee</b> - setup files to build ansible execution environment based on https://github.com/juniper/nita-ansible container.

<b>awx-operator-files</b> - clone of AWX self-reported version 21.3.0 from https://github.com/ansible/awx-operator.git (use reference tag [0.25.0](https://github.com/ansible/awx-operator/releases/tag/0.25.0)) setup repository and here for reference purposes. This is the version testing of the ansible-ee environment occurred against.


## Installation

1. Install Kubernetes. The build script assumes kubernetes is already installed. K3s (https://k3s.io/) was used to test this installation procedure. To install k3s issue the following commands:
```
curl -sfL https://get.k3s.io | sudo bash - 
sudo chmod 644 /etc/rancher/k3s/k3s.yaml 
```

You can test the installation of k3s with the following commands:
```
kubectl get nodes
kubectl version --short
```

2. Install AWX using the provided script as show below. This does a few things in addition to pulling a specific version of AWX. The script creates a namespace in k8s called ```awx```. It also creates the pods required to run AWX and spins up a service running on default ports for AWX. 

```
cd nita-awx
./build_container.sh
```

3. After installing AWX, you should create a superuser. This can be done by initiating a bash shell on the awx-web pod and executing the appropriate command (follow the prompts):

```
kubectl exec -ti deploy/awx -c awx-web -- "/bin/bash"
bash-5.1$ awx-manage createsuperuser
```

4. Finally you will need an ansible execution environment. 

```
cd ansible-ee
./build_container.sh
```

See [ansible-ee/README.md](ansible-ee/README.md) for details.

