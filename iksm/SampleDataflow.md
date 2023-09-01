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

### Step 6 - Use my own numaflow image

Go to my Numaflow workspace, make code changes and then run the following commands to publish the new image to Intuit's internal docker repository.

* Find current version
  * Go to https://quay.io/repository/keranyang/numaflow?tab=history

```
IMAGE_NAMESPACE=quay.io/keranyang DOCKER_PUSH=true VERSION=v23.0.0 make start
```

```
docker image tag quay.io/keranyang/numaflow:v23.0.0 docker.intuit.com/personal/keran/numaflow:v23.0.0
```

```
docker push docker.intuit.com/personal/keran/numaflow:v23.0.0
```

Use Lens to update the Numaflow images

* Go to numaflow-system namespace
* Find the numaflow-control/server **Deployment**
* Manually update images using `Edit`