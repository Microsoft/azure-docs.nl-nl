---
title: Ontwikkel omgeving configureren voor implementatie scripts in sjablonen | Microsoft Docs
description: Configureer de ontwikkelings omgeving voor implementatie scripts in Azure Resource Manager sjablonen (ARM-sjablonen).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200934"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Ontwikkel omgeving configureren voor implementatie scripts in ARM-sjablonen

Meer informatie over het maken van een ontwikkel omgeving voor het ontwikkelen en testen van ARM-sjabloon implementatie scripts met een implementatie script installatie kopie. U kunt een exemplaar van [Azure container](../../container-instances/container-instances-overview.md) maken of [docker](https://docs.docker.com/get-docker/)gebruiken. Beide opties zijn opgenomen in dit artikel.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-powershell-container"></a>Azure PowerShell-container

Als u geen Azure PowerShell-implementatie script hebt, kunt u een *hello.ps1* bestand maken met behulp van de volgende inhoud:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI-container

Voor een Azure CLI-container installatie kopie kunt u een *Hello.sh* -bestand maken met behulp van de volgende inhoud:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Wanneer u een Azure CLI-implementatie script uitvoert, wordt `AZ_SCRIPTS_OUTPUT_PATH` de locatie van het script uitvoer bestand opgeslagen in een omgevings variabele met de naam. De omgevings variabele is niet beschikbaar in de container voor de ontwikkel omgeving. Zie [werken met uitvoer van CLI-script](deployment-script-template.md#work-with-outputs-from-cli-script)voor meer informatie over het werken met Azure cli-uitvoer.

## <a name="use-azure-powershell-container-instance"></a>Azure PowerShell container exemplaar gebruiken

Als u uw scripts op uw computer wilt schrijven, moet u een opslag account maken en het opslag account koppelen aan het container exemplaar. Zodat u uw script kunt uploaden naar het opslag account en het script moet uitvoeren op het container exemplaar.

> [!NOTE]
> Het opslag account dat u maakt om uw script te testen, is niet hetzelfde opslag account als de implementatie script service gebruikt om het script uit te voeren. De implementatie script service maakt tijdens elke uitvoering een unieke naam als een bestands share.

### <a name="create-an-azure-powershell-container-instance"></a>Een Azure PowerShell container exemplaar maken

Met de volgende Azure Resource Manager sjabloon (ARM-sjabloon) maakt u een container exemplaar en een bestands share, waarna u de bestands share koppelt aan de container installatie kopie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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

De standaard waarde voor het koppelingspad is `/mnt/azscripts/azscriptinput` . Dit is het pad in het container exemplaar waar het is gekoppeld aan de bestands share.

De standaard container installatie kopie die in de sjabloon is opgegeven, is **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az5.2**. Een lijst met alle [ondersteunde Azure PowerShell versies](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)weer geven.

De sjabloon onderbreekt het container exemplaar na 1.800 seconden. U hebt 30 minuten voordat de container instantie de status beëindigd krijgt en de sessie wordt beëindigd.

De sjabloon implementeren:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Het implementatie script uploaden

Upload uw implementatie script naar het opslag account. Hier volgt een voor beeld van een Power shell-script:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

U kunt het bestand ook uploaden met behulp van de Azure Portal of de Azure CLI.

### <a name="test-the-deployment-script"></a>Het implementatie script testen

1. Open in de Azure Portal de resource groep waarin u het container exemplaar en het opslag account hebt geïmplementeerd.
2. Open de container groep. De standaard naam van de container groep is de project naam die is toegevoegd aan *cg*. De container instantie heeft de status **bezig met uitvoeren** .
3. Selecteer **containers** in het menu resource. De naam van het container exemplaar is de project naam die is toegevoegd aan de *container*.

    ![Scherm opname van het implementatie script van de container instantie Connect in de Azure Portal.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Selecteer **verbinding maken** en selecteer vervolgens **verbinding maken**. Als u geen verbinding kunt maken met het container exemplaar, start u de container groep opnieuw en probeert u het opnieuw.
5. Voer de volgende opdrachten uit in het console venster:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    De uitvoer is **Hello John Davids**.

    ![Scherm afbeelding van de test uitvoer van het Connect-container exemplaar in de-console.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Een Azure CLI-container exemplaar gebruiken

Als u uw scripts op uw computer wilt schrijven, maakt u een opslag account en koppelt u het opslag account aan het container exemplaar. Vervolgens kunt u uw script uploaden naar het opslag account en het script uitvoeren op het container exemplaar.

> [!NOTE]
> Het opslag account dat u maakt om uw script te testen, is niet hetzelfde opslag account als de implementatie script service gebruikt om het script uit te voeren. De implementatie script service maakt tijdens elke uitvoering een unieke naam als een bestands share.

### <a name="create-an-azure-cli-container-instance"></a>Een Azure CLI-container exemplaar maken

Met de volgende ARM-sjabloon maakt u een container exemplaar en een bestands share, waarna u de bestands share koppelt aan de container installatie kopie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
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

De standaard waarde voor het koppelingspad is `/mnt/azscripts/azscriptinput` . Dit is het pad in het container exemplaar waar het is gekoppeld aan de bestands share.

De standaard container installatie kopie die in de sjabloon is opgegeven, is **MCR.Microsoft.com/azure-cli:2.9.1**. Een lijst met [ondersteunde Azure cli-versies](https://mcr.microsoft.com/v2/azure-cli/tags/list)weer geven.

> [!IMPORTANT]
> Het implementatie script maakt gebruik van de beschik bare CLI-installatie kopieën van micro soft Container Registry (MCR). Het duurt ongeveer één maand om een CLI-installatie kopie te certificeren voor een implementatie script. Gebruik de CLI-versies die binnen 30 dagen zijn uitgebracht. Zie opmerkingen bij de [release van Azure cli](/cli/azure/release-notes-azure-cli)om de release datums voor de installatie kopieën te vinden. Als u een niet-ondersteunde versie gebruikt, wordt in het fout bericht een lijst met ondersteunde versies weer gegeven.

De sjabloon onderbreekt het container exemplaar na 1.800 seconden. U hebt 30 minuten voordat het container exemplaar in een Terminal status komt en de sessie wordt beëindigd.

De sjabloon implementeren:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Het implementatie script uploaden

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

U kunt het bestand ook uploaden met behulp van de Azure Portal of de Azure CLI.

### <a name="test-the-deployment-script"></a>Het implementatie script testen

1. Open in de Azure Portal de resource groep waarin u het container exemplaar en het opslag account hebt geïmplementeerd.
1. Open de container groep. De standaard naam van de container groep is de project naam die is toegevoegd aan *cg*. De container instantie wordt weer gegeven in de status wordt **uitgevoerd** .
1. Selecteer **containers** in het menu resource. De naam van het container exemplaar is de project naam die is toegevoegd aan de *container*.

    ![implementatie script Connect-container exemplaar](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecteer **verbinding maken** en selecteer vervolgens **verbinding maken**. Als u geen verbinding kunt maken met het container exemplaar, start u de container groep opnieuw en probeert u het opnieuw.
1. Voer de volgende opdrachten uit in het console venster:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    De uitvoer is **Hello John Davids**.

    ![test voor implementatie script container exemplaar](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Docker gebruiken

U kunt een vooraf geconfigureerde docker-container installatie kopie gebruiken als uw ontwikkel omgeving voor implementatie scripts. Zie [docker ophalen](https://docs.docker.com/get-docker/)voor meer informatie over het installeren van docker.
U moet ook het delen van bestanden configureren om de map te koppelen. Deze bevat de implementatie scripts in docker-container.

1. Haal de installatie kopie van de implementatie script container op de lokale computer op:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    In het voor beeld wordt gebruikgemaakt van versie Power Shell 4.3.0.

    Een CLI-installatie kopie uit een MCR halen:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    In dit voor beeld wordt gebruikgemaakt van versie CLI 2.0.80. Het implementatie script maakt gebruik van de standaard installatie kopieën voor CLI-containers die [hier](https://hub.docker.com/_/microsoft-azure-cli)worden gevonden.

1. Voer de docker-installatie kopie lokaal uit.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Vervang de stationsletter van de **&lt; host>** en de naam van de **&lt; Host Directory>** door een bestaande map op het gedeelde station. De map wordt toegewezen aan de map _/Data_ in de container. Bijvoorbeeld om _D:\docker_ toe te wijzen:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-het** betekent dat de container installatie kopie actief blijft.

    Een CLI-voor beeld:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. In de volgende scherm afbeelding ziet u hoe u een Power shell-script uitvoert, op voorwaarde dat u een *helloworld.ps1* bestand in het gedeelde station hebt.

    ![Docker-opdracht voor implementatie script van Resource Manager-sjabloon](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Nadat het script is getest, kunt u dit als een implementatie script in uw sjablonen gebruiken.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u implementatie scripts gebruikt. Een zelf studie over het implementatie script door lopen:

> [!div class="nextstepaction"]
> [Zelf studie: implementatie scripts gebruiken in ARM-sjablonen](./template-tutorial-deployment-script.md)
