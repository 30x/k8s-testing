# Operations Guide for GCE Kubernetes

> This guide assumes you are using MacOS

# Prerequisites

Latest Kubernetes Release (1.5.2)
```
wget https://github.com/kubernetes/kubernetes/releases/download/v1.5.2/kubernetes.tar.gz
tar -xf kubernetes.tar.xf
```

Kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

Gcloud SDK
```
wget https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-142.0.0-darwin-x86_64.tar.gz
tar -xf google-cloud-sdk-142.0.0-darwin-x86_64.tar.gz
google-cloud-sdk/install.sh
```
  
# Set Environment Variables

Can place environment variables in `{KUBE_ROOT}/cluster/env.sh`

```
export KUBERNETES_PROVIDER=gce
export NUM_NODES=3
export MULTIZONE=true 
export ENABLE_ETCD_QUORUM_READ=true
```

Needed to prevent continually uploading binaries
```
export SERVER_BINARY_TAR_URL=https://storage.googleapis.com/kubernetes-staging-45dc3a65fb/kubernetes-devel/kubernetes-server-linux-amd64.tar.gz
export SERVER_BINARY_TAR_HASH=4dbdcfa623412dac6be8fd5a4209a1f1423e8d30
export SALT_TAR_URL=https://storage.googleapis.com/kubernetes-staging-45dc3a65fb/kubernetes-devel/kubernetes-salt.tar.gz
export SALT_TAR_HASH=7dd7a17eebc5e943c5be53ba64c15c1bdeea7c80
```  

Currently Broken
```
export STORAGE_BACKEND=etcd3
```

Can set custom config
```
export KUBE_CONFIG_FILE=<your config file>
```


# Explanation of cluster/kube-up.sh

- Calls `source cluster/env.sh` if is present.
    - User created script to set environment variables
- Calls `source cluster/kube-util.sh` 
    - Calls `source cluster/skeleton/util.sh`
        - Initializes the base functions that each provider (gce, aws, etc.) must implement
    - Sets `KUBERNETES_PROVIDER=gce` if an alternative isn't set with an env var.
    - Sets `PROVIDER_UTILS=cluster/$KUBERNETES_PROVIDER/util.sh`
    - Calls `source PROVIDER_UTILS` (actually calling `cluster/gce/util.sh`)
        - Calls `source cluster/gce/${KUBE_CONFIG_FILE}"` or `source cluster/gce/config-default.sh` if the former isn't present.
            - Call `source cluster/gce/config-common.sh`
                - t


# Notes


# Spinup Cluster

```
cluster/kube-up.sh
```

# Setup Calio

```
wget http://docs.projectcalico.org/v2.0/getting-started/kubernetes/installation/hosted/calico.yaml
kubectl create -f calico.yaml
```

