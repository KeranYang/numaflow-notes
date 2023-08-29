# Testing using sample dataflow

### Step 1 - Find the namespace on the dev portal

* https://devportal.intuit.com/app/dp/
* My Project - OSS Platform Analytics
* Assets - sample dataflow
* Service Configuration - oss-analytics-sampledataflow-usw2-stg
* Argo - sync it

### Step 2 - Download the Kube config file

* Click on ikms
* SETTINGS - Kube Config - Download DevOps
* `eiamCli login`
* Configure kubectl
  * `cp devops@oss-analytics-sampledataflow-usw2-stg ~/.kube`
  * `vi ~/.zshrc`
  * Add to zshrc `alias ksam='kubectl --kubeconfig /Users/kyang5/.kube/devops@oss-analytics-sampledataflow-usw2-stg'`

### Step 3 - Create a test pipeline

```
ksam create -f simple-pipeline.yaml
```

### Step 4 - Explore the numaflow UI

* https://numaflow.ipmsaasprodusw2.iks2.a.intuit.com/
* Search for namespace `oss-analytics-sampledataflow-usw2-stg`

### Step 5 - Edit Quota

* Go to ikms
* K8S
* Resource Quota - Edit