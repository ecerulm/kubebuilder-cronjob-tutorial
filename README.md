

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


