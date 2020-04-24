# Setup

## Install OpenFaaS CLI

`curl -sL https://cli.openfaas.com | sh`

## Install [faas-netes](https://github.com/openfaas/faas-netes) [provider](https://docs.openfaas.com/architecture/faas-provider/)

`git clone https://github.com/openfaas/faas-netes`

### Create the K8s namespaces

`kubectl apply -f ./faas-netes/namespaces.yaml`


### Create the password for the [gateway](https://docs.openfaas.com/architecture/gateway/)

```
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="${PASSWORD}"
```  

### Deploy OpenFaaS

`kubectl apply -f ./faas-netes/yaml`


# Demo

## Access to the [gateway](https://docs.openfaas.com/architecture/gateway/)

`export OPENFAAS_URL=http://$(minikube ip):31112`

`echo -n $PASSWORD | faas-cli login --password-stdin`

## Deploy a [sample](https://blog.alexellis.io/first-faas-python-function/) Python function

```
pushd ./functions
faas-cli new --lang python hello-python
popd
```

### Build the function

Important: First, start the Docker daemon, in order to build and (optionally) publish the Docker image artifact

`faas-cli build -f ./functions/hello-python.yml`

### (optional) Publish the function
(optional) If configured a registry repo URL (i.e. in ./functions/hello-python.yml):

`faas-cli push -f ./functions/hello-python.yml`

### Deploy the function
`faas-cli deploy -f ./functions/hello-python.yml`

## [Workshops](https://github.com/openfaas/workshop)
