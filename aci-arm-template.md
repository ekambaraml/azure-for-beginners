## Part 2: Azure Container Deployment using ARM template
### Azure Resource Manager template
```
Example:
export RESOURCE_GROUP=lxgroup
export AZ_LOCATION=eastus
export ACI_NAME=lxdemo
export DNS_LABEL=lx-aci-demo
```

![Validataion](https://github.com/ekambaraml/azure-for-beginners/blob/main/Screen%20Shot%202021-07-18%20at%2011.58.38%20AM.png)


![deployment inprogress](https://github.com/ekambaraml/azure-for-beginners/blob/main/Screen%20Shot%202021-07-18%20at%2011.59.25%20AM.png)

![result](https://github.com/ekambaraml/azure-for-beginners/blob/main/Screen%20Shot%202021-07-18%20at%2011.59.49%20AM.png)

![result](https://github.com/ekambaraml/azure-for-beginners/blob/main/Screen%20Shot%202021-07-18%20at%2012.00.23%20PM.png)

### Template
```
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "defaultValue": "acilinuxpublicipcontainergroup",
            "type": "String",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "type": "String",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "defaultValue": 80,
            "type": "Int",
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "defaultValue": 1,
            "type": "Int",
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "defaultValue": 2,
            "type": "Int",
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "type": "String",
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "String",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
  ```
  
### Parameters
```
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "value": "lxdemo"
        },
        "image": {
            "value": "mcr.microsoft.com/azuredocs/aci-helloworld"
        },
        "port": {
            "value": 80
        },
        "cpuCores": {
            "value": 1
        },
        "memoryInGb": {
            "value": 2
        },
        "restartPolicy": {
            "value": "Always"
        },
        "location": {
            "value": "eastus"
        }
    }
}
```

