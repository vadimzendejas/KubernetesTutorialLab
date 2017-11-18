# Clean your resources and terminate your enviroment

You will need to delete and clean all your resources so you don't incour with extra charges.

Delete your resource group with the [az group delete](https://docs.microsoft.com/en-us/cli/azure/group#az_group_delete) command. In this example, a resource group named `myResourceGroup` is going to be deleted.

```azurecli
az group delete --name myResourceGroup --yes --no-wait
```
