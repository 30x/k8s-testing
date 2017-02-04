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
                - Calls `get-master-size` which returns the suggest size of the master node
                - Sets `${NODE_SCOPES}` based on if `${FEDERATION}` is `true`.
            - Sets a large number of environment variables. Best to refer directly to `cluster/gce/config-default.sh` for reference.
        - Calls `source cluster/common.sh`
            - Sets `DEFAULT_KUBECONFIG` to existing `${HOME}/.kube/config`
            - Calls `source cluster/lib/util.sh`
                - Creates `function kube::utio::wait-for-jobs()`
                - Creates `function kube::util::join`
                - Provides some colors
            - Calls `source cluster/lib/logging.sh`
                - Creates functions relevant to logging
            - Creates functions to interact with `kubeconfig` file
            - Creates functions to interact with kubernetes binaries
            - Creates function to generate environment variable configuration
        - Calls `source cluster/lib/util.sh`
            - Creates `function kube::utio::wait-for-jobs()`
            - Creates `function kube::util::join`
            - Provides some colors
        - Calls `source cluster/gce/${NODE_OS_DISTRIBUTION}/node-helper.sh`
            - placeholder
        - Calls `source cluster/gce/${MASTER_OS_DISTRIBUTION}/master-helper.sh`
            - placeholder
        - Sets several environemnt variables.
        - Creates the majority of the used functions.



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

