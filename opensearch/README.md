# OpenSearch Deployment Guide (Helm)
This guide explains how to install OpenSearch using Helm.

- [OpenSearch Deployment Guide (Helm)](#opensearch-deployment-guide-helm)
  - [Prerequisites](#prerequisites)
  - [Step-by-Step Deployment](#step-by-step-deployment)
    - [1. Add the OpenSearch Helm repo](#1-add-the-opensearch-helm-repo)
    - [2. Verify available charts](#2-verify-available-charts)
    - [3. Deploy OpenSearch using your custom values](#3-deploy-opensearch-using-your-custom-values)
  - [Verify the Deployment](#verify-the-deployment)
    - [Access a pod shell](#access-a-pod-shell)
  - [Uninstall OpenSearch](#uninstall-opensearch)
  - [Resources](#resources)

## Prerequisites
- A running **Kubernetes cluster**
- Helm >= 2.17.0 ([Helm installation guide](https://helm.sh/docs/intro/install/))
- Minimum 8 GiB RAM available for the cluster

## Step-by-Step Deployment
### 1. Add the OpenSearch Helm repo
```bash
helm repo add opensearch https://opensearch-project.github.io/helm-charts/
helm repo update
```

### 2. Verify available charts
```bash
helm search repo opensearch
```

### 3. Deploy OpenSearch using your custom values
For **OpenSearch 2.12** or greater, customize the admin password in `values.yaml` under `extraEnvs`, as shown in the following example:

```yaml
extraEnvs:
  - name: OPENSEARCH_INITIAL_ADMIN_PASSWORD
    value: <custom-admin-password>
```
**NOTE:** Make sure to update this `OPENSEARCH_INITIAL_ADMIN_PASSWORD`

We’ve provided a <a href="./values.yaml">`values.yaml`</a> in this directory with recommended configurations. You can modify this as needed.

```bash
helm upgrade --install --generate-name opensearch/opensearch \
  -f values.yaml \
  --namespace opensearch \
  --create-namespace
```
`helm upgrade --install` ensures idempotency: it installs if not present or upgrades if it exists.

Sample output:
```bash
  NAME: opensearch-1-1629223146
  LAST DEPLOYED: Tue Mar 25 17:59:07 2025
  NAMESPACE: opensearch
  STATUS: deployed
  REVISION: 1
  TEST SUITE: None
  NOTES:
  Watch all cluster members come up.
    $ kubectl get pods --namespace=opensearch -l app=opensearch-cluster-master -w
```

## Verify the Deployment
```bash
kubectl get pods -n opensearch
```
Expected output:
```bash
NAME                            READY   STATUS    RESTARTS   AGE
opensearch-cluster-master-0     1/1     Running   0          2m
opensearch-cluster-master-1     1/1     Running   0          2m
opensearch-cluster-master-2     1/1     Running   0          2m
```
### Access a pod shell
```bash
kubectl exec -it -n opensearch opensearch-cluster-master-0 -- /bin/bash
```

Test OpenSearch endpoint by sending requests to the pod to verify that OpenSearch is up and running:
```bash
curl -XGET https://localhost:9200 -u 'admin:<custom-admin-password>' --insecure
```
Expected Output:
```json
{
  "name" : "opensearch-cluster-master-1",
  "cluster_name" : "opensearch-cluster",
  "cluster_uuid" : "hP2gq5bPS3SLp8Z7wXm8YQ",
  "version" : {
    "distribution" : "opensearch",
    "number" : <version>,
    "build_type" : <build-type>,
    "build_hash" : <build-hash>,
    "build_date" : <build-date>,
    "build_snapshot" : false,
    "lucene_version" : <lucene-version>,
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```

## Uninstall OpenSearch
To identify the OpenSearch deployment that you want to delete:
```bash
$ helm list -n opensearch 
NAME  NAMESPACEREVISIONUPDATED  STATUS  CHART APP VERSION
opensearch-1-1629223146 opensearch 1 2025-03-25 17:59:07.664498239 +0000 UTCdeployedopensearch-1.0.0    1.0.0       
```
To delete or uninstall a deployment, run the following command:
```bash
helm delete opensearch-1-1629223146 -n opensearch
```

## Resources
For offical documentation, see <a href="https://docs.opensearch.org/docs/latest/install-and-configure/install-opensearch/helm/" >Helm install OpenSearch Cluster</a>

For OpenSearch Dashboards, see <a href="../dashboards/README.md">Helm to install OpenSearch Dashboards.</a>