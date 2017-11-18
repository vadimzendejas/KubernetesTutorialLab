# Deploy and use Azure Container Registry

Azure Container Registry (ACR) is an Azure-based, private registry, for Docker container images. This tutorial, part two of eight, walks through deploying an Azure Container Registry instance, and pushing a container image to it. Steps completed include:

> * Deploying an Azure Container Registry (ACR) instance
> * Tagging a container image for ACR
> * Uploading the image to ACR

In subsequent tutorials, this ACR instance is integrated with a Kubernetes cluster in AKS.

## Before you begin

In the [previous tutorial](./1-tutorial-kubernetes-prepare-app.md), a container image was created for a simple Azure Voting application. If you have not created the Azure Voting app image, return to [Tutorial 1 – Create container images](./1-tutorial-kubernetes-prepare-app.md).

This tutorial requires that you are running the Azure CLI version 2.0.21 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## Deploy Azure Container Registry

When deploying an Azure Container Registry, you first need a resource group. An Azure resource group is a logical container into which Azure resources are deployed and managed.

Create a resource group with the [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) command. In this example, a resource group named `myResourceGroup` is created in the `eastus` region.

```azurecli
az group create --name myResourceGroup --location eastus
```

Create an Azure Container registry with the [az acr create](https://docs.microsoft.com/en-us/cli/azure/acr#create) command. The name of a Container Registry **must be unique**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Throughout the rest of this tutorial, we use `<acrname>` as a placeholder for the container registry name.

## Container registry login

Use the [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) command to log in to the ACR instance. You need to provide the unique name given to the container registry when it was created.

```azurecli
az acr login --name <acrName>
```

The command returns a 'Login Succeeded’ message once completed.

## Tag container images

To see a list of current images, use the [docker images](https://docs.docker.com/engine/reference/commandline/images/) command.

```console
docker images
```

Output:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Each container image needs to be tagged with the loginServer name of the registry. This tag is used for routing when pushing container images to an image registry.

To get the loginServer name, run the following command.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Now, tag the `azure-vote-front` image with the loginServer of the container registry. Also, add `:redis-v1` to the end of the image name. This tag indicates the image version.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Once tagged, run [docker images](https://docs.docker.com/engine/reference/commandline/images/) to verify the operation.

```console
docker images
```

Output:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## Push images to registry

Push the `azure-vote-front` image to the registry.

Using the following example, replace the ACR loginServer name with the loginServer from your environment.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

This takes a couple of minutes to complete.

## List images in registry

To return a list of images that have been pushed to your Azure Container registry, user the [az acr repository list](https://docs.microsoft.com/en-us/cli/azure/acr/repository#list) command. Update the command with the ACR instance name.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

And then to see the tags for a specific image, use the [az acr repository show-tags](https://docs.microsoft.com/en-us/cli/azure/acr/repository#show-tags) command.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
redis-v1
```

At tutorial completion, the container image has been stored in a private Azure Container Registry instance. This image is deployed from ACR to a Kubernetes cluster in subsequent tutorials.

## Next steps

In this tutorial, an Azure Container Registry was prepared for use in an AKS cluster. The following steps were completed:

> * Deployed an Azure Container Registry instance
> * Tagged a container image for ACR
> * Uploaded the image to ACR

Advance to the next tutorial to learn about deploying a Kubernetes cluster in Azure.

> [Deploy Kubernetes cluster](./3-tutorial-kubernetes-deploy-cluster.md)