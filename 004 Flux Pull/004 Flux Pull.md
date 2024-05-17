# 004 Argo CD Pulling

## Explanation

So far, we have:

1. Set up Arc enabled AKS and ACR
2. Installed Flux the AKS Cluster
3. Given Flux access to the Git repository to monitor it
4. Enabled federated access for the Github Actions workflow
5. Pushed an image to the ACR through Github Actions

The next step is to ensure the image lands in the Kubernetes cluster. We do this through Kubernetes manifest files.

Specifically, we now need to:

1. Tell Flux what paths to monitor through Kustomization
2. Sync the manifests in the manifest directory and update cluster state

## Steps

1. Move the `deploy` folder to the root of the repository so that it's top level path is `./deploy/`
2. In `./deploy/node-app-deployment.yml`, change the image name to the image in your ACR. This should have been pushed by the workflow in 003.
3. Run the following to update the Flux configuration with a Kustomization:

    ```bash
    az k8s-configuration flux kustomization create \
    --cluster-name <clusterName> \ 
    --cluster-type connectedClusters \ 
    --name cluster-config  \
    --resource-group <resourceGroup> \
    --kustomization-name flux-node --path ./deploy --prune
    ```

4. And run the following to delete the default Kustomization that we don't need anymore:

    ```bash
    az k8s-configuration flux kustomization delete \
    --cluster-name <clusterName> \ 
    --cluster-type connectedClusters \ 
    --name cluster-config  \
    --resource-group <resourceGroup> \
    --kustomization-name kustomization-1
    ```

5. You can go to portal to see the new Kustomization you defined, and see how the GitOps flow is working.
