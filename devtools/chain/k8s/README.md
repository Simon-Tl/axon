# Axon Kubernetes Deployment

## Introduction
This repository contains Axon Kubernetes deployment files. The following sections provide detailed instructions for deploying Axon Chain quickly, while optimizing resource usage.

## Environmental preparation
Kubernetes enables you to deploy Axon Chain rapidly while conserving resources.

- First, you need a kubernetes system, either new or existing
- Secondly, it is necessary to plan the storageClass inside kubernetes
- The third is a machine that can have kubectl installed and can operate kubernetes

## Instructions

1. **Download the Project**

    ```bash
    git clone https://github.com/axonweb3/axon.git
    ```

2. **Navigate to the Corresponding Directory**

    ```bash
    cd devtools/chain/k8s/multple
    ```

3. **Create the Corresponding Namespace**

    ```bash
    kubectl create namespace axon-alphanet
    ```

4. **Check Axon Version**

- Modify ```newTag: forcerelay-dev-c203acb``` to the version you want to deploy

    ```bash
    images:
      - name: ghcr.io/axonweb3/axon:0.2.0-dev
        newName: ghcr.io/axonweb3/axon 
        newTag: forcerelay-dev-c203acb    
    
    ```

5. **Check Axon's Required StorageClass and Modify**

- modifying  StorageClass ```storageClassName: chain``` for your own cluster

    ```bash
    volumeClaimTemplates:
    - metadata:
        name: data1
        spec:
        accessModes: ["ReadWriteOnce"]
        storageClassName: chain
        resources:
            requests:
            storage: 100Gi    
    ```

6. **Perform initialization and modify the axon1 to axon4 statefulset file to the following format**

    ```bash
    containers:
     - name: axon1
       args:
         - ./axon
         - init
         - --config=/app/devtools/chain/k8s/node_1.toml
         - --chain-spec=/app/devtools/chain/chain-spec.toml    
    ```

7. [Generate key](https://github.com/axonweb3/axon/tree/main/core/cli#generate-keypair:~:text=Generate%20Keypair,in%20config%20file.), and update the fields of the chain-spec.yaml and toml files
   
   - [chain-spec.yaml](https://github.com/axonweb3/axon/blob/axon-deploy-kubernetes/devtools/chain/k8s/multiple/chain-spec.toml#:~:text=interval%20%3D%203000-,%5B%5Bparams.verifier_list%5D%5D,vote_weight%20%3D%201,-axon/devtools/chain)

   - [node_1.toml](https://github.com/axonweb3/axon/blob/axon-deploy-kubernetes/devtools/chain/k8s/multiple/node_1.toml#:~:text=%5B%5Bnetwork.,p2p/QmXoSkz4zkHHiFZqmDZQ4gFYtJ72uqtp4m6FX373X4VkRq%22)

   - [node_2.toml](https://github.com/axonweb3/axon/blob/axon-deploy-kubernetes/devtools/chain/k8s/multiple/node_2.toml#:~:text=%5B%5Bnetwork.,p2p/QmXoSkz4zkHHiFZqmDZQ4gFYtJ72uqtp4m6FX373X4VkRq%22)

   - [node_3.toml](https://github.com/axonweb3/axon/blob/axon-deploy-kubernetes/devtools/chain/k8s/multiple/node_3.toml#:~:text=%5B%5Bnetwork.,p2p/QmXoSkz4zkHHiFZqmDZQ4gFYtJ72uqtp4m6FX373X4VkRq%22)

   - [node_4.toml](https://github.com/axonweb3/axon/blob/axon-deploy-kubernetes/devtools/chain/k8s/multiple/node_4.toml#:~:text=%5B%5Bnetwork.,p2p/QmXoSkz4zkHHiFZqmDZQ4gFYtJ72uqtp4m6FX373X4VkRq%22) 

7. **Start Axon After the axon initialization is successful, modify the axon1 to axon4 statefulset file to the following format**

    ```bash
    containers:
     - name: axon1
       args:
         - ./axon
         - run
         - --config=/app/devtools/chain/k8s/node_1.toml
    ```
    ```
    cd devtools/chain/k8s/
    kubectl apply -k multiple -n axon-alphanet
    ```

8. **After the startup command is executed, check that the pod status is ```runing``` and the axon log is blocked normally**
    ```bash
    kubectl get pods -n axon-alphanet
    kubectl logs axon1 -n axon-alphanet -f 
    ```
