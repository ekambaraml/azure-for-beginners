```
Example:
export RESOURCE_GROUP=lxgroup
export AZ_LOCATION=eastus
export ACI_NAME=lxdemo
export DNS_LABEL=lx-aci-demo


1. Create Resource Group

az group create --name ${RESOURCE_GROUP}  --location ${AZ_LOCATION}

2. Create an Container

az container create --resource-group ${RESOURCE_GROUP} --name ${ACI_NAME} --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label ${DNS_LABEL} --ports 80

3. List the service
az container show --resource-group ${RESOURCE_GROUP} --name ${ACI_NAME} --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table

4. Check the status
az container logs --resource-group ${RESOURCE_GROUP} --name ${ACI_NAME}

5. Attach Output Stream
attach your console to container output

az container attach --resource-group ${RESOURCE_GROUP} --name ${ACI_NAME}

6. Delete and Cleanup
az container delete --resource-group ${RESOURCE_GROUP} --name ${ACI_NAME}

7. Verify the cleanup and resource removal

az container list --resource-group ${RESOURCE_GROUP} --output table

8. Delete the Resource Group
az group delete --name ${RESOURCE_GROUP}

```
