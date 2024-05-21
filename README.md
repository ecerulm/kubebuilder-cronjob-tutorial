
This is just the result of following along the [Tutorial 1: Building CronJob][1]



# kubebuilder-cronjob-tutorial

https://book.kubebuilder.io/cronjob-tutorial/cronjob-tutorial

## Step 1


```
# create a project directory, and then run the init command.
mkdir project
cd project
# we'll use a domain of tutorial.kubebuilder.io,
# so all API groups will be <group>.tutorial.kubebuilder.io.
kubebuilder init --domain tutorial.kubebuilder.io --repo tutorial.kubebuilder.io/project

```

The result is in tag `V1` in this repo

    git checkout V1



## Step 2: Modify project to add cont


## Step 3: Install in kind cluster



```
kind get clusters
kind create cluster
kubectx kind-kind
make manifest
export ENABLE_WEBHOOK=false
make run

kubectl create -f config/samples/batch_v1_cronjob.yaml

kubectl get cronjob.batch.tutorial.kubebuilder.io -o yaml
kubectl get job
```


# Step 4: Publish on docker 

```
IMG=docker.io/ecerulm/kubebuilder-cronjob:latest

make docker-build docker-push IMG=$IMG # This pushes to dockerhub
make deploy IMG=$IMG
```


[1]: https://book.kubebuilder.io/cronjob-tutorial/cronjob-tutorial
