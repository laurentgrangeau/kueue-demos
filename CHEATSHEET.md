# Demo 1
## Create GKE cluster
```
gcloud container clusters create "kueue-demos" --zone "europe-west1-c" --cluster-version "1.32.1-gke.1357001" --machine-type "e2-standard-2"
```

## Get credentials
```
gcloud container clusters get-credentials kueue-demos --location europe-west1-c
```

## Apply Kueue
```
kubectl apply --server-side -f https://github.com/kubernetes-sigs/kueue/releases/download/v0.10.2/manifests.yaml
```

## Get pods
```
kubectl get pods -n kueue-system
```

## Get all
```
kubectl get clusterqueue
kubectl get localqueue
kubectl get resourceflavors
```

## Deploy namespace
```
kubectl apply -f team-a-namespace.yaml
kubectl apply -f team-b-namespace.yaml
kubectl get ns
```

## Deploy default flavor
```
kubectl apply -f default-flavor.yaml
kubectl get flavor
```

## Deploy other flavors
```
kubectl apply -f flavor-ondemand-l4.yaml
kubectl apply -f flavor-reservation-l4.yaml
kubectl apply -f flavor-spot-l4.yaml
kubectl get flavor
```

## Deploy cluster queue
```
kubectl apply -f batch-cluster-queue.yaml
kubectl get cq
```

## Deploy local queue
```
kubectl apply -f team-a-local-queue.yaml
kubectl apply -f team-b-local-queue.yaml
kubectl get lq -n team-a
```

## Deploy job
```
kubectl apply -f batch-job.yaml
kubectl get clusterqueue cluster-queue -o wide
kubectl get localqueue -n team-a
kubectl get pods -n team-a
```

# Demo 2 - Priority scheduling
## Deploy priority class
```
kubectl apply -f dev-priority.yaml
kubectl apply -f prod-priority.yaml
```

## Deploy cluster queue
```
kubectl apply -f priority-cluster-queue.yaml
```

## Deploy dev priority job
```
kubectl apply -f dev-job.yaml
kubectl get pods -n team-a
```

## Deply prod priority job
```
kubectl apply -f prod-job.yaml
kubectl get pods -n team-a
```

# Demo 3 - DWS
## Create t4 nodepool
```
gcloud container node-pools create nvidia-t4s \
    --cluster=kueue-demos \
    --location=europe-west1-c \
     --enable-queued-provisioning \
    --accelerator type=nvidia-tesla-t4,count=1,gpu-driver-version=latest \
    --machine-type=n1-standard-8 \
    --enable-autoscaling \
    --num-nodes=0 \
    --total-max-nodes 10 \
    --location-policy=ANY \
    --reservation-affinity=none \
    --no-enable-autorepair
```

## Deploy admission check
```
kubectl apply -f admission-check.yaml
kubectl get admissioncheck
```

## Deploy provision request config
```
kubectl apply -f provision-request-config.yaml
kubectl get provisioningrequestconfig
```

## Deploy dws cluster queue
```
kubectl apply -f dws-cluster-queue.yaml
kubectl get cq
```

## Deploy dws local queue
```
kubectl apply -f dws-local-queue.yaml
kubectl get lq
```

## Deploy dws job
```
kubectl apply -f dws-batch-job.yaml
kubectl describe job sample-job
kubectl get provreq
```

# Take some time to be admitted
```
kubectl get nodes
kubectl get pods
```

# Demo 4 - Topology Aware Scheduling
```
cat tas-topology.yaml
cat tas-flavor.yaml
cat tas-queue.yaml
```

# Demo 5 - Kubeflow training operator
```
kubectl get configmaps kueue-manager-config -o yaml -n kueue-system
kubectl apply --server-side -k "github.com/kubeflow/training-operator.git/manifests/overlays/standalone?ref=v1.8.1"
cat tf-job.yaml
```
