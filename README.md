

# Create scaffold


```
mkdir cronjob-project
cd cronjob-project
kubebuilder init --domain tutorial.kubebuilder.io --repo tutorial.kubebuilder.io/project --project-name cronjobplus
```


Note that we pass `--project-name cronjobplus` otherwise the project name will be `cronjob-project` 
taken from the folder name.

The project name will determine:

* The kubernetes namespace `$projectname-system`
* The name of the docker image `<someregistry>/$projectname:<tag>`
* the kubernetes label `app.kubernetes.io/name: $projectname` for resources

This will generate

```
A  .dockerignore
A  .gitignore
A  .golangci.yml
A  Dockerfile
A  Makefile
A  PROJECT
A  README.md
A  cmd/main.go
A  config/default/kustomization.yaml
A  config/default/manager_config_patch.yaml
A  config/default/manager_metrics_patch.yaml
A  config/manager/kustomization.yaml
A  config/manager/manager.yaml
A  config/prometheus/kustomization.yaml
A  config/prometheus/monitor.yaml
A  config/rbac/kustomization.yaml
A  config/rbac/leader_election_role.yaml
A  config/rbac/leader_election_role_binding.yaml
A  config/rbac/metrics_service.yaml
A  config/rbac/role.yaml
A  config/rbac/role_binding.yaml
A  config/rbac/service_account.yaml
A  go.mod
A  go.sum
A  hack/boilerplate.go.txt
A  test/e2e/e2e_suite_test.go
A  test/e2e/e2e_test.go
A  test/utils/utils.go``

```

The important files there are : 

* `cmd/main.go`

The result is in tag `cronjobplus-v1`

    git checkout cronjobplus-v1
    cd cronjob-project
    make build

# Generate API (Group Version Kind)

```
cd cronjob-project
kubebuilder create api --group batch --version v1 --kind CronJob
INFO Create Resource [y/n]
y
INFO Create Controller [y/n]
y
```

This will modify:

```
 M PROJECT
 M cmd/main.go
 M config/default/kustomization.yaml
 M config/rbac/kustomization.yaml
```

and will add:

```
A  api/v1/cronjob_types.go
A  api/v1/groupversion_info.go
A  api/v1/zz_generated.deepcopy.go
A  config/crd/kustomization.yaml
A  config/crd/kustomizeconfig.yaml
A  config/rbac/cronjob_editor_role.yaml
A  config/rbac/cronjob_viewer_role.yaml
A  config/samples/batch_v1_cronjob.yaml
A  config/samples/kustomization.yaml
A  internal/controller/cronjob_controller.go
A  internal/controller/cronjob_controller_test.go
A  internal/controller/suite_test.go
```

The important files are

* `cmd/main.go`
* `api/v1/cronjob_types.go`
* `internal/controller/cronjob_controller.go`


The result is tag `cronjobplus-v2`

    git checkout cronjobplus-v2
    cd cronjob-project
    make build



# Implement the CRD and the controller Reconcile

Add code to:

* `api/v1/cronjob_types.go`
* `internal/controller/cronjob_controller.go`

Then compile:

    cd cronjob-project
    make build

You can test with

    brew install kind
    kind create cluster
    kubectx kind-kind
    make install # Installs the CRDs in kind
    make run # Runs the controller locally towards kind

The result is tag `cronjobplus-v3`

    git checkout cronjobplus-v3
    cd cronjob-project
    make build


# Generate the webhooks

    cd cronjob-project
    kubebuilder create webhook --group batch --version v1 --kind CronJob --defaulting --programmatic-validation


This will modify the following files

```
M  PROJECT
M  api/v1/zz_generated.deepcopy.go
M  cmd/main.go
M  config/crd/kustomization.yaml
M  config/default/kustomization.yaml
M  go.mod
```

And will add:

```
A  api/v1/cronjob_webhook.go
A  api/v1/cronjob_webhook_test.go
A  api/v1/webhook_suite_test.go
A  config/certmanager/certificate.yaml
A  config/certmanager/kustomization.yaml
A  config/certmanager/kustomizeconfig.yaml
A  config/crd/patches/cainjection_in_cronjobs.yaml
A  config/crd/patches/webhook_in_cronjobs.yaml
A  config/default/manager_webhook_patch.yaml
A  config/default/webhookcainjection_patch.yaml
A  config/webhook/kustomization.yaml
A  config/webhook/kustomizeconfig.yaml
A  config/webhook/manifests.yaml
A  config/webhook/service.yaml
```

The result is tag `cronjobplus-v4`:

    git checkout cronjobplus-v4
    cd cronjob-project
    make build

# Implement the webhooks

Modify:

* `api/v1/cronjob_webhook.go`


The result is tag `cronjobplus-v5`:

    git checkout cronjobplus-v5
    cd cronjob-project
    make build

# Now to test for test in kind


Modify the `Makefile` to use `apply --server-side --force-conflicts`  to avoid
the problem with `last-applied-configuration` being too long

The result is tag `cronjobplus-v6`:

    git checkout cronjobplus-v6
    cd cronjob-project
    make build


Now you need to modify the 

* `config/default/kustomization.yaml`

to use `cert-manager` self-signed certificates for the webhooks.



```
brew install kind
kind create cluster
kubectx kind-kind
kubectl cluster-info
# We need cert-manager running in Kind to generate the self signed certificates for the webhook
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.yaml
# We need kubernetes-dashboard to verify that CronJob controller is working, etc
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard 
kubectl -n kubernetes-dashboard create serviceaccount admin-user
kubectl create  clusterrolebinding kubernetes-dashboard-admin --clusterrole cluster-admin --serviceaccount kubernetes-dashboard:admin-user
kubectl -n kubernetes-dashboard create token admin-user --duration=720h
kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443
open https://localhost:8443
```

Then we install the CRDs and deploy the controller+webhooks:

    make install
    IMG=docker.io/ecerulm/cronjobplus:latest
    make docker-build IMG=$IMG
    kind load docker-image $IMG --name kind
    make deploy IMG=$IMG


The last step is to apply a `CronJob` resource:

```

```
