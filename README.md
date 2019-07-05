# 1. Setup kubectl
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

## Autocomplete Bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
# 2. Try kubectl

```bash
kubectl config view
```

Output:

```
apiVersion: v1
clusters: []
contexts: []
current-context: ""
kind: Config
preferences: {}
users: []
```

# 3. Control cluster on Google Kubernetes Engine (GKE)

## 3.1 Install gcloud command line

```bash
export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"

echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

sudo apt-get update && sudo apt-get install google-cloud-sdk

gcloud init
```

## 3.2 Config credentials for kubectl

```bash
# Show all clusters:
gcloud container clusters list

# Get credentials
gcloud container clusters get-credentials <cluster name>
```

## 3.3 Check config

```bash
kubectl config view
```

Output:

```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://35.187.236.110
  name: gke_fpt-fti_asia-southeast1-a_k8s-demo
contexts:
- context:
    cluster: gke_fpt-fti_asia-southeast1-a_k8s-demo
    user: gke_fpt-fti_asia-southeast1-a_k8s-demo
  name: gke_fpt-fti_asia-southeast1-a_k8s-demo
current-context: gke_fpt-fti_asia-southeast1-a_k8s-demo
kind: Config
preferences: {}
users:
- name: gke_fpt-fti_asia-southeast1-a_k8s-demo
  user:
    auth-provider:
      config:
        cmd-args: config config-helper --format=json
        cmd-path: /usr/lib/google-cloud-sdk/bin/gcloud
        expiry-key: '{.credential.token_expiry}'
        token-key: '{.credential.access_token}'
      name: gcp
```

Show all nodes

```bash
kubectl get nodes
```

Output:

```
NAME                                STATUS   ROLES    AGE     VERSION
gke-k8s-demo-pool-1-c938b222-3w3x   Ready    <none>   3h29m   v1.12.7-gke.10
gke-k8s-demo-pool-1-c938b222-bbwh   Ready    <none>   3h29m   v1.12.7-gke.10
gke-k8s-demo-pool-1-c938b222-prrp   Ready    <none>   3h29m   v1.12.7-gke.10
```

# 4. Setup Kubernetes Dashboard
## Apply dashboard deployment
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

## Create user
```
kubectl apply -f https://raw.githubusercontent.com/hiephm/devops-course/master/hello-k8s/rbac.yml
```

## Get access token
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

## Start proxy
```
kubectl proxy
```

## Access dashboard from browser, using above token:
```
http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

