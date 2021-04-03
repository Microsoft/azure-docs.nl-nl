---
title: Azure Files volume koppelen aan container groep
description: Meer informatie over het koppelen van een Azure Files-volume om de status te behouden met Azure Container Instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d52ad8ad02735c98b29a83d8ca69cdea8c6af7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97954971"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Een Azure-bestandsshare koppelen in Azure Container Instances

Azure-containerinstanties zijn standaard staatloos. Als de container opnieuw wordt opgestart, vastloopt of wordt gestopt, is de status van het geheel verloren gegaan. Als u een status langer wilt behouden dan de levensduur van de container, moet u een volume vanuit een externe opslag koppelen. Zoals in dit artikel wordt weer gegeven, kunt Azure Container Instances een Azure-bestands share koppelen die is gemaakt met [Azure files](../storage/files/storage-files-introduction.md). Azure Files biedt volledig beheerde bestands shares die worden gehost in Azure Storage die toegankelijk zijn via het industrie standaard SMB-protocol (Server Message Block). Het gebruik van een Azure-bestands share met Azure Container Instances biedt functies voor het delen van bestanden die vergelijkbaar zijn met het gebruik van een Azure-bestands share met Azure virtual machines.

> [!NOTE]
> Het koppelen van een Azure Files share is momenteel beperkt tot Linux-containers. Zoek de huidige platform verschillen in het [overzicht](container-instances-overview.md#linux-and-windows-containers).
>
> Het koppelen van een Azure Files share aan een container exemplaar is vergelijkbaar met een docker [BIND-koppeling](https://docs.docker.com/storage/bind-mounts/). Houd er rekening mee dat als u een share koppelt in een container Directory waarin bestanden of directory's bestaan, deze bestanden of mappen worden verborgen door de koppeling en niet toegankelijk zijn wanneer de container wordt uitgevoerd.
>

> [!IMPORTANT]
> Als u container groepen implementeert in een Azure-Virtual Network, moet u een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) toevoegen aan uw Azure Storage-account.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Voordat u een Azure-bestandsshare met Azure Container Instances kunt gebruiken, moet u deze maken. Voer het volgende script uit om een opslag account te maken voor het hosten van de bestands share en de share zelf. De naam van het opslagaccount moet globaal uniek zijn, dus voegt het script een willekeurige waarde toe aan de basistekenreeks.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Opslagreferenties ophalen

Als u een Azure-bestandsshare wilt koppelen als een volume in Azure Container Instances, hebt u drie waarden nodig: de naam van het opslagaccount, de sharenaam en de toegangssleutel voor opslag.

* **Naam van opslag account** : als u het voor gaande script hebt gebruikt, is de naam van het opslag account opgeslagen in de `$ACI_PERS_STORAGE_ACCOUNT_NAME` variabele. Als u de account naam wilt zien, typt u:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Share naam** -deze waarde is al bekend (gedefinieerd als `acishare` in het voor gaande script)

* **Sleutel van het opslag account** : deze waarde kan worden gevonden met behulp van de volgende opdracht.

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Container en koppel volume implementeren-CLI

Als u een Azure-bestands share als een volume in een container wilt koppelen met behulp van de Azure CLI, geeft u het share-en volume koppel punt op wanneer u de container maakt met [AZ container Create][az-container-create]. Als u de vorige stappen hebt uitgevoerd, kunt u de share die u eerder hebt gemaakt, koppelen met behulp van de volgende opdracht voor het maken van een container:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

De `--dns-name-label` waarde moet uniek zijn binnen de Azure-regio waar u het container exemplaar maakt. Werk de waarde in de voor gaande opdracht bij als u een fout bericht over het **DNS-naam label** ontvangt wanneer u de opdracht uitvoert.

## <a name="manage-files-in-mounted-volume"></a>Bestanden in gekoppelde volume beheren

Zodra de container is gestart, kunt u de eenvoudige web-app die is geïmplementeerd via de micro soft [ACI-hellofiles-][aci-hellofiles] installatie kopie, gebruiken om kleine tekst bestanden in de Azure-bestands share te maken op het koppelingspad dat u hebt opgegeven. Haal de Fully Qualified Domain Name (FQDN) van de web-app op met de opdracht [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Nadat u de tekst hebt opgeslagen met de app, kunt u de [Azure Portal][portal] of een hulp programma gebruiken, zoals de [Microsoft Azure Storage Explorer][storage-explorer] om het bestand of de bestanden die naar de bestands share zijn geschreven, op te halen en te controleren.

## <a name="deploy-container-and-mount-volume---yaml"></a>Container en koppel volume implementeren-YAML

U kunt ook een container groep implementeren en een volume in een container koppelen met de Azure CLI en een [yaml-sjabloon](container-instances-multi-container-yaml.md). Implementeren op basis van de YAML-sjabloon is een voorkeurs methode bij het implementeren van container groepen die uit meerdere containers bestaan.

De volgende YAML-sjabloon definieert een container groep met één container die met de `aci-hellofiles` installatie kopie is gemaakt. De container koppelt de Azure-bestands share *acishare* die eerder zijn gemaakt als een volume. Indien aangegeven, voert u de naam en de opslag sleutel in voor het opslag account dat als host fungeert voor de bestands share. 

Net als in het CLI-voor beeld `dnsNameLabel` moet de waarde uniek zijn binnen de Azure-regio waar u het container exemplaar maakt. Werk indien nodig de waarde in het YAML-bestand bij.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Als u wilt implementeren met de YAML-sjabloon, slaat u de voor gaande YAML op in een bestand met de naam `deploy-aci.yaml` en voert u de opdracht [AZ container Create][az-container-create] uit met de `--file` para meter:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Container implementeren en volume koppelen-Resource Manager

Naast de implementatie van CLI en YAML kunt u een container groep implementeren en een volume in een container koppelen met behulp van een Azure [Resource Manager-sjabloon](/azure/templates/microsoft.containerinstance/containergroups).

Vul eerst de `volumes` matrix in het gedeelte container Group `properties` van de sjabloon. 

Voor elke container waarin u het volume wilt koppelen, vult u de `volumeMounts` matrix in de `properties` sectie van de container definitie.

De volgende Resource Manager-sjabloon definieert een container groep met één container die is gemaakt met de `aci-hellofiles` installatie kopie. De container koppelt de Azure-bestands share *acishare* die eerder zijn gemaakt als een volume. Indien aangegeven, voert u de naam en de opslag sleutel in voor het opslag account dat als host fungeert voor de bestands share. 

Net als in de voor gaande voor beelden `dnsNameLabel` moet de waarde uniek zijn binnen de Azure-regio waar u het container exemplaar maakt. Werk de waarde in de sjabloon zo nodig bij.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Als u wilt implementeren met de Resource Manager-sjabloon, slaat u de voor gaande JSON op in een bestand met de naam `deploy-aci.json` en voert u de opdracht [AZ Deployment Group Create][az-deployment-group-create] uit met de `--template-file` para meter:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Meerdere volumes koppelen

Als u meerdere volumes in een container exemplaar wilt koppelen, moet u implementeren met behulp van een [Azure Resource Manager-sjabloon](/azure/templates/microsoft.containerinstance/containergroups), een yaml-bestand of een andere programmatische methode. Als u een sjabloon-of YAML-bestand wilt gebruiken, geeft u de delen Details op en definieert u de volumes door de `volumes` matrix in de `properties` sectie van het bestand in te vullen. 

Als u bijvoorbeeld twee Azure Files shares hebt gemaakt met de naam *share1* en *Share2* in de *myStorageAccount* van het opslag account, ziet de `volumes` matrix in een resource manager-sjabloon er ongeveer als volgt uit:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Voor elke container in de container groep waarin u de volumes wilt koppelen, vult u de `volumeMounts` matrix in de `properties` sectie van de container definitie. Hiermee koppelt u bijvoorbeeld de twee volumes, *myvolume1* en *myvolume2*, die eerder zijn gedefinieerd:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van andere volume typen in Azure Container Instances:

* [Een emptyDir-volume koppelen in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume koppelen in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Een geheim volume koppelen in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
