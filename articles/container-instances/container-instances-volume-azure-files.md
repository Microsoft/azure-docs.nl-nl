---
title: Een Azure Files aan een containergroep
description: Leer hoe u een volume Azure Files om de status te blijven houden met Azure Container Instances
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763735"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Een Azure-bestandsshare koppelen in Azure Container Instances

Azure-containerinstanties zijn standaard staatloos. Als de container opnieuw wordt opgestart, vast loopt of stopt, gaat alle status verloren. Als u een status langer wilt behouden dan de levensduur van de container, moet u een volume vanuit een externe opslag koppelen. Zoals in dit artikel wordt weergegeven, Azure Container Instances een Azure-bestands share die is gemaakt met [Azure Files.](../storage/files/storage-files-introduction.md) Azure Files biedt volledig beheerde bestands shares die worden gehost in Azure Storage die toegankelijk zijn via het industriestandaard Server Message Block (SMB)-protocol. Het gebruik van een Azure-bestands share Azure Container Instances biedt functies voor het delen van bestanden die vergelijkbaar zijn met het gebruik van een Azure-bestandsdeling met virtuele Azure-machines.

## <a name="limitations"></a>Beperkingen

* U kunt alleen shares Azure Files aan Linux-containers. Lees meer over de verschillen in functieondersteuning voor Linux- en Windows-containergroepen in het [overzicht](container-instances-overview.md#linux-and-windows-containers).
* Voor de volume mount van azure-bestands share moet de Linux-container worden uitgevoerd als *root.*
* Azure File Share-volume mounts zijn beperkt tot CIFS-ondersteuning.

> [!NOTE]
> Het Azure Files van een share aan een container-instantie is vergelijkbaar met een [Docker-bindings mount](https://docs.docker.com/storage/bind-mounts/). Als u een share aan een containermap met bestanden of mappen wilt toevoegen, worden bestanden of mappen door de mount verborgen, waardoor ze ontoegankelijk zijn terwijl de container wordt uitgevoerd.
>

> [!IMPORTANT]
> Als u containergroepen implementeert in een Azure Virtual Network, moet u een [service-eindpunt toevoegen](../virtual-network/virtual-network-service-endpoints-overview.md) aan uw Azure Storage account.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Voordat u een Azure-bestandsshare met Azure Container Instances kunt gebruiken, moet u deze maken. Voer het volgende script uit om een opslagaccount te maken voor het hosten van de bestands share en de share zelf. De naam van het opslagaccount moet globaal uniek zijn, dus voegt het script een willekeurige waarde toe aan de basistekenreeks.

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

* **Naam van opslagaccount:** als u het voorgaande script hebt gebruikt, is de naam van het opslagaccount opgeslagen in de `$ACI_PERS_STORAGE_ACCOUNT_NAME` variabele . Als u de accountnaam wilt zien, typt u:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Sharenaam:** deze waarde is al bekend (gedefinieerd `acishare` zoals in het voorgaande script)

* **Sleutel van opslagaccount:** deze waarde kunt u vinden met de volgende opdracht:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Container implementeren en volume monteren - CLI

Als u een Azure-bestands share wilt toevoegen als een volume in een container met behulp van de Azure CLI, geeft u de share en het volume-bevestigingspunt op wanneer u de container maakt [met az container create][az-container-create]. Als u de vorige stappen hebt gevolgd, kunt u de share die u eerder hebt gemaakt, aan de hand van de volgende opdracht een container maken:

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

De `--dns-name-label` waarde moet uniek zijn binnen de Azure-regio waar u de container-instantie maakt. Werk de waarde in de voorgaande opdracht bij als u een foutbericht ontvangt over het **DNS-naamlabel** wanneer u de opdracht uitvoert.

## <a name="manage-files-in-mounted-volume"></a>Bestanden in een bevestigd volume beheren

Zodra de container is gestart, kunt u de eenvoudige web-app gebruiken die is geïmplementeerd via de Microsoft [aci-hellofiles-afbeelding][aci-hellofiles] om kleine tekstbestanden te maken in de Azure-bestands share op het pad dat u hebt opgegeven. Verkrijg de Fully Qualified Domain Name (FQDN) van de web-app met de [opdracht az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Nadat u tekst hebt opgeslagen met behulp van de app, kunt u de [Azure Portal][portal] of een hulpprogramma zoals de [Microsoft Azure Storage Explorer][storage-explorer] gebruiken om het bestand of de bestanden die naar de bestands share worden geschreven, op te halen en te inspecteren.

## <a name="deploy-container-and-mount-volume---yaml"></a>Container implementeren en volume monteren - YAML

U kunt ook een containergroep implementeren en een volume aan een container toevoegen met de Azure CLI en een [YAML-sjabloon.](container-instances-multi-container-yaml.md) Het implementeren op basis van een YAML-sjabloon is een voorkeursmethode bij het implementeren van containergroepen die uit meerdere containers bestaan.

De volgende YAML-sjabloon definieert een containergroep met één container die is gemaakt met de `aci-hellofiles` -afbeelding. Met de container wordt de Azure-bestandsshare *acishare* die eerder is gemaakt, als een volume aan elkaar toegevoegd. Voer, indien aangegeven, de naam en opslagsleutel in voor het opslagaccount dat als host voor de bestands share wordt gebruikt. 

Net als in het CLI-voorbeeld moet `dnsNameLabel` de waarde uniek zijn binnen de Azure-regio waar u de container-instantie maakt. Werk indien nodig de waarde in het YAML-bestand bij.

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

Als u wilt implementeren met de YAML-sjabloon, moet u de voorgaande YAML opslaan in een bestand met de naam en vervolgens de `deploy-aci.yaml` opdracht az container [create][az-container-create] uitvoeren met de `--file` parameter :

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Container implementeren en volume monteren - Resource Manager

Naast cli- en YAML-implementatie kunt u een containergroep implementeren en een volume aan een container toevoegen met behulp van een Azure [Resource Manager sjabloon](/azure/templates/microsoft.containerinstance/containergroups).

Vul eerst de matrix `volumes` in de sectie containergroep van de sjabloon `properties` in. 

Vul vervolgens voor elke container waarin u het volume wilt monteren de `volumeMounts` matrix in de sectie van de `properties` containerdefinitie.

De volgende Resource Manager definieert een containergroep met één container die is gemaakt met de `aci-hellofiles` afbeelding. In de container wordt de Azure-bestandsshare *acishare die* eerder is gemaakt als een volume, aan de container toegevoegd. Voer, indien aangegeven, de naam en opslagsleutel in voor het opslagaccount dat als host voor de bestands share wordt gebruikt. 

Net als in de vorige voorbeelden moet `dnsNameLabel` de waarde uniek zijn binnen de Azure-regio waar u de container-instantie maakt. Werk indien nodig de waarde in de sjabloon bij.

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

Als u wilt implementeren met Resource Manager sjabloon, moet u de voorgaande JSON opslaan in een bestand met de naam en vervolgens de `deploy-aci.json` opdracht az deployment group [create][az-deployment-group-create] uitvoeren met de `--template-file` parameter :

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Meerdere volumes aan elkaar monteren

Als u meerdere volumes in een container-exemplaar wilt toevoegen, moet u implementeren met behulp van een [Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups)sjabloon, een YAML-bestand of een andere programmatische methode. Als u een sjabloon of YAML-bestand wilt gebruiken, geeft u de sharegegevens op en definieert u de volumes door de matrix in de sectie van `volumes` `properties` het bestand te vullen. 

Als u bijvoorbeeld twee Azure Files-shares hebt gemaakt met de naam *share1* en *share2* in het opslagaccount *myStorageAccount,* ziet de matrix in een Resource Manager-sjabloon er ongeveer als volgt `volumes` uit:

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

Vul vervolgens voor elke container in de containergroep waarin u de volumes wilt monteren de matrix in de sectie van `volumeMounts` `properties` de containerdefinitie. Hiermee worden bijvoorbeeld de twee volumes, *myvolume1* en *myvolume2,* die eerder zijn gedefinieerd:

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

Meer informatie over het aan elkaar Azure Container Instances:

* [Een leegDir-volume in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een gitRepo-volume in een Azure Container Instances](container-instances-volume-gitrepo.md)
* [Een geheim volume in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
