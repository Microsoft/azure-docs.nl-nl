---
title: Zone-redundant register voor hoge beschikbaarheid
description: Meer informatie over het inschakelen van zone-redundantie in Azure Container Registry. Maak een containerregister of replicatie in een Azure-beschikbaarheidszone. Zone-redundantie is een functie van de Premium-servicelaag.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479012"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Zone-redundantie inschakelen in Azure Container Registry voor tolerantie en hoge beschikbaarheid

Naast [geo-replicatie,](container-registry-geo-replication.md)waarmee registergegevens worden gerepliceerd in een of meer Azure-regio's om beschikbaarheid te bieden en de latentie voor regionale bewerkingen te verminderen, ondersteunt Azure Container Registry optionele *zone-redundantie.* [Zone-redundantie](../availability-zones/az-overview.md#availability-zones) biedt tolerantie en hoge beschikbaarheid voor een register of replicatieresource (replica) in een specifieke regio.

In dit artikel wordt beschreven hoe u een zone-redundant containerregister of -replica in kunt stellen met behulp van de Azure CLI, Azure Portal of Azure Resource Manager sjabloon. 

Zone-redundantie is een **preview-functie** van de servicelaag Premium Container Registry. Zie Azure Container Registry servicelagen voor meer informatie [over registerservicelagen en -limieten.](container-registry-skus.md)

## <a name="preview-limitations"></a>Preview-beperkingen

* Momenteel ondersteund in de volgende regio's: VS - oost, VS - oost 2, VS - west 2, Europa - noord, Europa - west, Japan - oost.
* Regioconversies naar beschikbaarheidszones worden momenteel niet ondersteund. Als u ondersteuning voor beschikbaarheidszone in een regio wilt inschakelen, moet het register worden gemaakt in de gewenste regio, met ondersteuning voor beschikbaarheidszone ingeschakeld of moet er een gerepliceerde regio worden toegevoegd met ondersteuning voor beschikbaarheidszone ingeschakeld.
* Zone-redundantie kan niet worden uitgeschakeld in een regio.
* [ACR-taken](container-registry-tasks-overview.md) biedt nog geen ondersteuning voor beschikbaarheidszones.

## <a name="about-zone-redundancy"></a>Over zone-redundantie

Gebruik [Azure-beschikbaarheidszones](../availability-zones/az-overview.md) om een robuust azure-containerregister met hoge beschikbaarheid te maken binnen een Azure-regio. Organisaties kunnen bijvoorbeeld een zone-redundant Azure-containerregister instellen met andere ondersteunde [Azure-resources](../availability-zones/az-region.md) om te voldoen aan gegevensstatus of andere nalevingsvereisten, terwijl ze hoge beschikbaarheid binnen een regio bieden.

Azure Container Registry ondersteunt ook [geo-replicatie,](container-registry-geo-replication.md)waarmee de service in meerdere regio's wordt gerepliceerd, waardoor redundantie en lokaliteit mogelijk zijn voor het berekenen van resources op andere locaties. De combinatie van beschikbaarheidszones voor redundantie binnen een regio en geo-replicatie tussen meerdere regio's verbetert zowel de betrouwbaarheid als prestaties van een register.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. Elke zone heeft een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Wanneer deze is geconfigureerd voor zone-redundantie, wordt een register (of een registerreplica in een andere regio) gerepliceerd naar alle beschikbaarheidszones in de regio, zodat het beschikbaar blijft als er datacenterfouten optreden.

## <a name="create-a-zone-redundant-registry---cli"></a>Een zone-redundant register maken - CLI

Als u de Azure CLI wilt gebruiken om zone-redundantie in te Azure Cloud Shell, hebt u Azure CLI versie 2.17.0 of hoger Azure Cloud Shell. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voer indien nodig de opdracht [az group create uit](/cli/azure/group#az_group_create) om een resourcegroep voor het register te maken.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Zone-ingeschakeld register maken

Voer de [opdracht az acr create uit](/cli/azure/acr#az_acr_create) om een zone-redundant register te maken in de Premium-servicelaag. Kies een regio die [ondersteuning biedt voor beschikbaarheidszones](../availability-zones/az-region.md) voor Azure Container Registry. In het volgende voorbeeld wordt zone-redundantie ingeschakeld in de *regio eastus.* Zie de `az acr create` Help-opdracht voor meer registeropties.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Noteer in de uitvoer van de opdracht `zoneRedundancy` de eigenschap voor het register. Wanneer deze functie is ingeschakeld, is het register zone-redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Zone-redundante replicatie maken

Voer de [opdracht az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) uit om een zone-redundante registerreplica te maken in een regio die ondersteuning biedt voor beschikbaarheidszones voor Azure Container Registry, zoals *westus2.* [](../availability-zones/az-region.md) 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Noteer in de uitvoer van de opdracht `zoneRedundancy` de eigenschap voor de replica. Wanneer deze functie is ingeschakeld, is de replica zone-redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Een zone-redundant register maken - portal

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Selecteer **Een resource maken** > **Containers** > **Container Registry**.
1. Selecteer of **maak op** het tabblad Basisinformatie een resourcegroep en voer een unieke registernaam in. 
1. Selecteer **in** Locatie een regio die ondersteuning biedt voor zone-redundantie voor Azure Container Registry, zoals VS *- oost.*
1. Selecteer premium in  **SKU.**
1. Selecteer **ingeschakeld in** Beschikbaarheidszones. 
1. Configureer eventueel aanvullende registerinstellingen en selecteer controleren **en maken.**
1. Selecteer **Maken om** het register-exemplaar te implementeren.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Zone-redundantie inschakelen in Azure Portal":::

Een zone-redundante replicatie maken:

1. Navigeer naar het containerregister van de Premium-laag en selecteer **Replicaties.**
1. Selecteer op de kaart die wordt weergegeven een groene zeshoek in een regio die ondersteuning biedt voor zone-redundantie voor Azure Container Registry, zoals VS - west **2.** Of selecteer **+ Toevoegen.**
1. Bevestig in **het venster Replicatie** maken de **Locatie**. Selecteer **in Beschikbaarheidszones** **de optie Ingeschakeld** en selecteer vervolgens **Maken.**

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Zone-redundante replicatie inschakelen in Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Een zone-redundant register maken - sjabloon

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voer indien nodig de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep voor het register te maken in een regio die ondersteuning biedt voor beschikbaarheidszones voor Azure Container Registry, zoals *eastus*. [](../availability-zones/az-region.md) Deze regio wordt gebruikt door de sjabloon om de registerlocatie in te stellen.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>De sjabloon implementeren 

U kunt de volgende sjabloon Resource Manager om een zone-redundant, geo-gerepliceerd register te maken. Met de sjabloon worden zone-redundantie in het register en een regionale replica standaard ingeschakeld. 

Kopieer de volgende inhoud naar een nieuw bestand en sla deze op met een bestandsnaam zoals `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Voer de volgende [opdracht az deployment group create uit om](/cli/azure/group/deployment#az_group_deployment_create) het register te maken met behulp van het voorgaande sjabloonbestand. Geef, indien aangegeven, het volgende op:

* een unieke registernaam, of implementeer de sjabloon zonder parameters en er wordt een unieke naam voor u gemaakt
* een locatie voor de replica die beschikbaarheidszones ondersteunt, zoals *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Noteer in de uitvoer van de opdracht `zoneRedundancy` de eigenschap voor het register en de replica. Wanneer deze functie is ingeschakeld, is elke resource zone-redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [regio's die beschikbaarheidszones ondersteunen.](../availability-zones/az-region.md)
* Meer informatie over bouwen [voor betrouwbaarheid](/azure/architecture/framework/resiliency/overview) in Azure.
