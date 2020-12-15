---
title: Ontwikkel omgeving configureren voor implementatie scripts in sjablonen | Microsoft Docs
description: Configureer de ontwikkelings omgeving voor implementatie scripts in Azure Resource Manager-sjablonen.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: 4a7f21410bb97db0a7974870efb812c9954ac241
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503553"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates"></a>Ontwikkel omgeving configureren voor implementatie scripts in sjablonen

Meer informatie over het maken van een ontwikkel omgeving voor het ontwikkelen en testen van implementatie scripts met een implementatie script installatie kopie. U kunt een [exemplaar van Azure container](../../container-instances/container-instances-overview.md) maken of [docker](https://docs.docker.com/get-docker/)gebruiken. Beide zijn opgenomen in dit artikel.

## <a name="prerequisites"></a>Vereisten

Als u geen implementatie script hebt, kunt u een **hello.ps1** -bestand maken met de volgende inhoud:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Azure container instance gebruiken

Als u uw scripts op uw computer wilt schrijven, moet u een opslag account maken en het opslag account koppelen aan het container exemplaar. Zodat u uw script kunt uploaden naar het opslag account en het script moet uitvoeren op het container exemplaar.

> [!NOTE]
> Het opslag account dat u maakt om uw script te testen, is niet hetzelfde opslag account als de implementatie script service gebruikt om het script uit te voeren. De implementatie script service maakt tijdens elke uitvoering een unieke naam als een bestands share.

### <a name="create-an-azure-container-instance"></a>Een Azure-container exemplaar maken

Met de volgende ARM-sjabloon maakt u een container exemplaar en een bestands share, waarna u de bestands share koppelt aan de container installatie kopie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
De standaard waarde voor het koppelingspad is **deploymentScript**.  Dit is het pad in het container exemplaar waarnaar het is gekoppeld aan de bestands share.

De standaard container installatie kopie die in de sjabloon is opgegeven, is **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:AZ4.3**.  Zie [Azure PowerShell of Azure cli](./deployment-script-template.md#prerequisites)voor een lijst met ondersteunde Azure PowerShell versies en Azure cli-versies.

De sjabloon onderbreekt het container exemplaar 1800 seconden. U hebt 30 minuten voordat de container instantie de Terminal status krijgt en de sessie wordt beëindigd.

De sjabloon implementeren:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Implementatie script uploaden

Upload uw implementatie script naar het opslag account. Hier volgt een Power shell-voor beeld:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

U kunt het bestand ook uploaden met behulp van de Azure Portal en Azure CLI.

### <a name="test-the-deployment-script"></a>Het implementatie script testen

1. Open vanuit het Azure Portal de resource groep waarin u het container exemplaar en het opslag account hebt geïmplementeerd.
1. Open de container groep. De standaard naam van de container groep is de naam van het project waaraan **cg** is toegevoegd. U ziet dat de container instantie de status **actief** heeft.
1. Selecteer **containers** in het menu links. Er wordt een container exemplaar weer geven.  De naam van het container exemplaar is de project naam waaraan de **container** is toegevoegd.

    ![implementatie script Connect-container exemplaar](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecteer **verbinding maken** en selecteer vervolgens **verbinding maken**. Als u geen verbinding kunt maken met het container exemplaar, start u de container groep opnieuw en probeert u het opnieuw.
1. Voer de volgende opdrachten uit in het console venster:

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    De uitvoer is **Hello John Davids**.

    ![test voor implementatie script container exemplaar](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>Docker gebruiken

U kunt een vooraf geconfigureerde docker-container installatie kopie gebruiken als uw ontwikkel omgeving voor implementatie scripts. Zie [docker ophalen](https://docs.docker.com/get-docker/)voor meer informatie over het installeren van docker.
U moet ook het delen van bestanden configureren om de map te koppelen. Deze bevat de implementatie scripts in docker-container.

1. Haal de installatie kopie van de implementatie script container op de lokale computer op:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    In het voor beeld wordt gebruikgemaakt van versie Power Shell 4.3.0.

    Een CLI-installatie kopie ophalen uit een micro soft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    In dit voor beeld wordt gebruikgemaakt van versie CLI 2.0.80. Het implementatie script maakt gebruik van de standaard installatie kopieën voor CLI-containers die [hier](https://hub.docker.com/_/microsoft-azure-cli)worden gevonden.

1. Voer de docker-installatie kopie lokaal uit.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Vervang de stationsletter van de **&lt; host>** en de naam van de **&lt; Host Directory>** door een bestaande map op het gedeelde station.  De map wordt toegewezen aan de map **/Data** in de container. Voor voor beelden, om D:\docker toe te wijzen:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-het** betekent dat de container installatie kopie actief blijft.

    Een CLI-voor beeld:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. In de volgende scherm afbeelding ziet u hoe u een Power shell-script uitvoert, op voorwaarde dat u een helloworld.ps1 bestand in het gedeelde station hebt.

    ![Docker-opdracht voor implementatie script van Resource Manager-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Nadat het script is getest, kunt u dit als een implementatie script in uw sjablonen gebruiken.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatie scripts gebruikt. Een zelf studie over het implementatie script door lopen:

> [!div class="nextstepaction"]
> [Zelf studie: implementatie scripts gebruiken in Azure Resource Manager sjablonen](./template-tutorial-deployment-script.md)
