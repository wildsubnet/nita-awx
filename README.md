# nita-awx-setup

AWX Execution Environment is located in ansible-ee folder and based on https://github.com/juniper/nita-ansible container.

This is a work in progress and represents the files used to create an running k3s environment with AWX running along side a NITA install. This is being used to test the AWX execution environment.

## Commands used to install

1. Initial install using files in awx-operator folder

```
export NAMESPACE=awx 
kubectl create ns ${NAMESPACE}
sudo kubectl config set-context --current --namespace=$NAMESPACE 
cd awx-operator/
sudo apt install curl jq 
RELEASE_TAG=`curl -s https://api.github.com/repos/ansible/awx-operator/releases/latest | grep tag_name | cut -d '"' -f 4` 
echo $RELEASE_TAG 
git checkout $RELEASE_TAG
export NAMESPACE=awx
make deploy
```

2. Make dataspace persistenet:

```
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: static-data-pvc
  namespace: awx
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  resources:
    requests:
      storage: 5Gi
EOF
```

3. Build execution environment in ansible-ee folder.

