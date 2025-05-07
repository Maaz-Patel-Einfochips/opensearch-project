# OpenSearch Dashboards Guide (Helm)
This guide explains how to install OpenSearch Dashboards using Helm after your OpenSearch cluster is up and running in a Kubernetes environment.

- [OpenSearch Dashboards Guide (Helm)](#opensearch-dashboards-guide-helm)
  - [Prerequisites](#prerequisites)
  - [Verify OpenSearch Cluster](#verify-opensearch-cluster)
  - [Step-by-Step Deployment](#step-by-step-deployment)
    - [1. Add the OpenSearch Dashboard Helm repo](#1-add-the-opensearch-dashboard-helm-repo)
    - [2. Verify available charts](#2-verify-available-charts)
    - [3. Deploy OpenSearch Dashboard using your custom values](#3-deploy-opensearch-dashboard-using-your-custom-values)
  - [Access OpenSearch Dashboards](#access-opensearch-dashboards)
    - [Check Pod Status](#check-pod-status)
    - [Use LoadBalancer IP](#use-loadbalancer-ip)
    - [Port Forward to Access UI (Optional)](#port-forward-to-access-ui-optional)
    - [Get Application URL (Alternative Method)](#get-application-url-alternative-method)
  - [Uninstall OpenSearch Dashboards](#uninstall-opensearch-dashboards)
  - [Resources](#resources)

## Prerequisites
- A running Kubernetes cluster
- Helm >= 2.17.0 ([Helm installation guide](https://helm.sh/docs/intro/install/))
- <a href="../opensearch-dashboards/README.md" >OpenSearch cluster installed via Helm</a>
- Access to the OpenSearch cluster (e.g., using curl to verify it’s running)

## Verify OpenSearch Cluster
```bash
curl -XGET https://localhost:9200 -u 'admin:<custom-admin-password>' --insecure
```

Expected output:

```json
{
  "name": "opensearch-cluster-master-1",
  "cluster_name": "opensearch-cluster",
  ...
  "tagline": "The OpenSearch Project: https://opensearch.org/"
}
```
## Step-by-Step Deployment
### 1. Add the OpenSearch Dashboard Helm repo
```bash
helm repo add opensearch https://opensearch-project.github.io/helm-charts/
helm repo update
```

### 2. Verify available charts
```bash
helm search repo opensearch
```

### 3. Deploy OpenSearch Dashboard using your custom values
We’ve provided a <a href="./values.yaml">`values.yaml`</a> in this directory with recommended configurations. You can modify this as needed.

**NOTE:** Make sure to update `opensearch.password: "<custom-admin-password>"`

For `<custom-admin-password>` please refer <a href="../opensearch/README.md">Opensearch installation guide</a>

```bash
helm upgrade --install --generate-name opensearch/opensearch-dashboards \
  -f values.yaml \
  --namespace opensearch \
  --create-namespace
```
`helm upgrade --install` ensures idempotency: it installs if not present or upgrades if it exists.

## Access OpenSearch Dashboards
### Check Pod Status
```bash
kubectl get pods -n opensearch
```
Expected pod list:

```sql
NAME                                                  READY   STATUS    RESTARTS   AGE
opensearch-cluster-master-0                           1/1     Running   0          4m35s
opensearch-cluster-master-1                           1/1     Running   0          4m35s
opensearch-cluster-master-2                           1/1     Running   0          4m35s
opensearch-dashboards-1-1629223356-758bd8747f-8www5   1/1     Running   0          66s
```

### Use LoadBalancer IP
```bash
kubectl get svc -n opensearch
```

Expected svc list:
```sql
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                         AGE
opensearch-cluster-master            ClusterIP      10.0.221.254   <none>         9200/TCP,9300/TCP,9600/TCP      5m25s
opensearch-cluster-master-headless   ClusterIP      None           <none>         9200/TCP,9300/TCP,9600/TCP      5m25s
opensearch-dashboards-1-1629223356   LoadBalancer   10.0.116.133   4.144.196.14   5601:32760/TCP,9601:31177/TCP   1m14s
```
Now, you can visit OpenSearch Dashboards at:

```
http://4.144.196.14:5601
```

### Port Forward to Access UI (Optional)
```bash
kubectl port-forward deployment/opensearch-dashboards-1-1629223356 5601
```
Now, access OpenSearch Dashboards at:

```
http://localhost:5601
```
### Get Application URL (Alternative Method)
```bash
export POD_NAME=$(kubectl get pods --namespace opensearch -l "app.kubernetes.io/name=opensearch-dashboards" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace opensearch $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
kubectl --namespace opensearch port-forward $POD_NAME 8080:$CONTAINER_PORT
```
Visit:
```
http://127.0.0.1:8080
```

## Uninstall OpenSearch Dashboards
List Helm Releases
```bash
helm list -n opensearch
```
Delete the Deployment
```bash
helm delete opensearch-dashboards-1-1629223356 -n opensearch
```

## Resources
For offical documentation, see <a href="https://docs.opensearch.org/docs/latest/install-and-configure/install-dashboards/helm/" >Helm install OpenSearch DashBoard Cluster</a>