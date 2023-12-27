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
   
   - chain-spec.yaml
   
   ```bash
    [[params.verifier_list]]
    bls_pub_key = "0xa26e3fe1cf51bd4822072c61bdc315ac32e3d3c2e2484bb92942666399e863b4bf56cf2926383cc706ffc15dfebc85c6"
    pub_key = "0x031ddc35212b7fc7ff6685b17d91f77c972535aee5c7ae5684d3e72b986f08834b"
    propose_weight = 1
    vote_weight = 1

    [[params.verifier_list]]
    bls_pub_key = "0x80310fa9df724b5603d283b472ed3bf85254a8a4ceda8a274b421f6cf2be1d9184267cdfe9a199d36ff14e57668a55d0"
    pub_key = "0x02b77c74eb68af3d4d6cc7884ed6709f1a2a1af0f713382a4438ec2ea3a70d4d7f"
    propose_weight = 1
    vote_weight = 1

    [[params.verifier_list]]
    bls_pub_key = "0x897721e9016864141a8b982a48217f66ef318ce598aa31842cddaaebe3cd7feab17050022afa6c2123aba39938fe4142"
    pub_key = "0x027ffd6a6a231561f2afe5878b1c743323b34263d16787130b1815fe35649b0bf5"
    propose_weight = 1
    vote_weight = 1

    [[params.verifier_list]]
    bls_pub_key = "0x98eef09a3927acb225191101a1d9aa85775fdcdc87b9ba36898f6c132b485d66aef91c0f51cda331be4f985c3be6761c"
    pub_key = "0x0232c489c23b1207107e9a24648c1e4754a8c1c0b38db96df57a526201035058cb"
    propose_weight = 1
    vote_weight = 1
   ```

   - node_{1,2,3,4}.toml

   ```bash
    [[network.bootstraps]]
    multi_address = "/dns4/axon1/tcp/8001/p2p/QmNk6bBwkLPuqnsrtxpp819XLZY3ymgjs3p1nKtxBVgqxj"

    [[network.bootstraps]]
    multi_address = "/dns4/axon2/tcp/8001/p2p/QmaHBJqULbLGDn7Td196goNebH6XMTMMu2sKNNP2DiX9S2"

    [[network.bootstraps]]
    multi_address = "/dns4/axon3/tcp/8001/p2p/QmQLufVVmBuHKoYhdDCqUFYVtLYs1quryoaA1mkQYQdWkn"

    [[network.bootstraps]]
    multi_address = "/dns4/axon4/tcp/8001/p2p/QmXoSkz4zkHHiFZqmDZQ4gFYtJ72uqtp4m6FX373X4VkRq"
   ```

7. **Start Axon After the axon initialization is successful, modify the axon1 to axon4 statefulset file to the following format**

    ```bash
    containers:
     - name: axon1
       args:
         - ./axon
         - init
         - --config=/app/devtools/chain/k8s/node_1.toml
         - --chain-spec=/app/devtools/chain/chain-spec.toml  
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

