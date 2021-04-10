---
title: Een op Azure Files gebaseerd volume gebruiken in een Service Fabric mesh-app
description: Meer informatie over het opslaan van de status in een Azure Service Fabric mesh-toepassing door een Azure Files op basis van een volume in een service te koppelen met behulp van de Azure CLI.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625834"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Een op Azure Files gebaseerd volume koppelen in een Service Fabric mesh-toepassing 

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

In dit artikel wordt beschreven hoe u een op Azure Files gebaseerd volume koppelt in een service van een Service Fabric mesh-toepassing.  Het Azure Files-volume stuur programma is een docker-volume stuur programma dat wordt gebruikt om een Azure Files share te koppelen aan een container, waarmee u de service status persistent kunt maken. Volumes bieden u bestands opslag voor algemeen gebruik en bieden de mogelijkheid om bestanden te lezen/schrijven met behulp van normale I/O-bestand-Api's van de schijf.  Lees de [opslag status](service-fabric-mesh-storing-state.md)voor meer informatie over de volumes en opties voor het opslaan van toepassings gegevens.

Als u een volume in een service wilt koppelen, maakt u een volume bron in uw Service Fabric mesh-toepassing en verwijst u naar dat volume in uw service.  Het declareren van de volume resource en het verwijzen naar deze in de service resource kan worden uitgevoerd in de [op YAML gebaseerde resource bestanden](#declare-a-volume-resource-and-update-the-service-resource-yaml) of in het [JSON-gebaseerde implementatie sjabloon](#declare-a-volume-resource-and-update-the-service-resource-json). Voordat u het volume koppelt, moet u eerst een Azure-opslag account en een [Bestands share in azure files](../storage/files/storage-how-to-create-file-share.md)maken.

## <a name="prerequisites"></a>Vereisten
> [!NOTE]
> **Bekend probleem met de implementatie op Windows RS5 Development machine:** Er is een open bug met Power shell-cmdlet New-SmbGlobalMapping op RS5 Windows-machines waarmee het koppelen van Azurefile-volumes wordt voor komen. Hieronder vindt u een voorbeeld fout die wordt aangetroffen wanneer op AzureFile gebaseerd volume wordt gekoppeld op de lokale ontwikkel machine.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
De tijdelijke oplossing voor het probleem is 1) de onderstaande opdracht uitvoeren als Power shell-beheerder en 2) start de computer opnieuw op.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om dit artikel te volt ooien. 

Als u de Azure CLI lokaal wilt gebruiken met dit artikel, moet u ervoor zorgen dat u `az --version` ten minste een waarde retourneert `azure-cli (2.0.43)` .  Installeer de module Azure Service Fabric mesh CLI (of werk deze bij) door deze [instructies](service-fabric-mesh-howto-setup-cli.md)te volgen.

Aanmelden bij Azure en uw abonnement instellen:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Een opslag account en bestands share maken (optioneel)
Voor het koppelen van een Azure Files-volume is een opslag account en een bestands share vereist.  U kunt een bestaand Azure Storage-account en een bestands share gebruiken of resources maken:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>De naam en sleutel van het opslag account en de naam van de bestands share ophalen
In de naam van het opslag account, de sleutel van het opslag account en de naam van de bestands share wordt verwezen als `<storageAccountName>` , `<storageAccountKey>` en `<fileShareName>` in de volgende secties. 

Vermeld uw opslag accounts en haal de naam van het opslag account op met de bestands share die u wilt gebruiken:
```azurecli-interactive
az storage account list
```

De naam van de bestands share ophalen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

De sleutel van het opslag account (' Key1 ') ophalen:
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

U kunt deze waarden ook vinden in de [Azure Portal](https://portal.azure.com):
* `<storageAccountName>` -Onder **opslag accounts**, de naam van het opslag account dat wordt gebruikt voor het maken van de bestands share.
* `<storageAccountKey>` -Selecteer uw opslag account onder **opslag accounts** en selecteer vervolgens **toegangs sleutels** en gebruik de waarde onder **key1**.
* `<fileShareName>` -Selecteer uw opslag account onder  **opslag accounts** en selecteer vervolgens **bestanden**. De naam die u moet gebruiken, is de naam van de bestands share die u hebt gemaakt.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Een volume resource declareren en de service resource (JSON) bijwerken

Voeg para meters toe voor de `<fileShareName>` , `<storageAccountName>` en en `<storageAccountKey>` waarden die u in een vorige stap hebt gevonden. 

Maak een volume resource als een peer van de toepassings bron. Geef een naam en provider ("SFAzureFile") op om het op Azure Files gebaseerd volume te gebruiken). `azureFileParameters`Geef in de para meters voor de `<fileShareName>` , `<storageAccountName>` , en `<storageAccountKey>` waarden op die u in een vorige stap hebt gevonden.

Als u het volume in uw service wilt koppelen, voegt `volumeRefs` u een toe aan het- `codePackages` element van de service.  `name` is de bron-ID voor het volume (of een implementatie sjabloon parameter voor de volume bron) en de naam van het volume dat is gedeclareerd in het bron bestand volume. yaml.  `destinationPath` is de lokale map waaraan het volume wordt gekoppeld.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Een volume resource declareren en de service resource bijwerken (YAML)

Voeg een nieuw *volume. yaml* -bestand toe aan de map *app-resources* voor uw toepassing.  Geef een naam en provider ("SFAzureFile") op om het op Azure Files gebaseerd volume te gebruiken). `<fileShareName>`, `<storageAccountName>` en `<storageAccountKey>` zijn de waarden die u in een vorige stap hebt gevonden.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Werk het bestand *service. yaml* in de map *service resources* bij om het volume te koppelen aan uw service.  Voeg het `volumeRefs` element toe aan het- `codePackages` element.  `name` is de bron-ID voor het volume (of een implementatie sjabloon parameter voor de volume bron) en de naam van het volume dat is gedeclareerd in het bron bestand volume. yaml.  `destinationPath` is de lokale map waaraan het volume wordt gekoppeld.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de voorbeeld toepassing Azure Files volume op [github](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
- Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
