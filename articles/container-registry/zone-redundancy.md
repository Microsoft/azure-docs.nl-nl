---
title: Zone-redundant REGI ster voor hoge Beschik baarheid
description: Meer informatie over het inschakelen van zone redundantie in Azure Container Registry. Maak een container register of replicatie in een Azure-beschikbaarheids zone. Zone redundantie is een functie van de service laag Premium.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 7de8ed101d2df9e491c475f522a56580798c49a9
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696275"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Zone redundantie inschakelen in Azure Container Registry voor tolerantie en hoge Beschik baarheid

Naast [geo-replicatie](container-registry-geo-replication.md), waarmee register gegevens worden gerepliceerd over een of meer Azure-regio's om Beschik baarheid te bieden en de latentie voor regionale bewerkingen te verminderen, Azure container Registry ondersteuning biedt voor optionele *zone redundantie*. [Zone redundantie](../availability-zones/az-overview.md#availability-zones) biedt een tolerantie en hoge Beschik baarheid van een REGI ster of replicatie bron (replica) in een specifieke regio.

In dit artikel wordt beschreven hoe u een zone-redundant container register of replica instelt met behulp van de Azure CLI-, Azure Portal-of Azure Resource Manager-sjabloon. 

Zone redundantie is een **Preview** -functie van de service tier van het Premium-container register. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten van het REGI ster.

## <a name="preview-limitations"></a>Preview-beperkingen

* Momenteel ondersteund in de volgende regio's: VS-Oost, VS-Oost 2 en VS-West 2.
* Regionale conversies naar beschikbaarheids zones worden momenteel niet ondersteund. Als u ondersteuning voor beschikbaarheids zones in een regio wilt inschakelen, moet u het REGI ster in de gewenste regio maken, waarbij ondersteuning voor beschikbaarheids zone is ingeschakeld, of dat er een gerepliceerde regio moet worden toegevoegd met ondersteuning voor beschikbaarheids zone ingeschakeld.
* Zone redundantie kan niet worden uitgeschakeld in een regio.
* [ACR-taken](container-registry-tasks-overview.md) bieden nog geen ondersteuning voor beschikbaarheids zones.

## <a name="about-zone-redundancy"></a>Over zone redundantie

Gebruik Azure- [beschikbaarheids zones](../availability-zones/az-overview.md) om een Azure-container register voor robuuste en maximale Beschik baarheid te maken binnen een Azure-regio. Bijvoorbeeld: organisaties kunnen een zone-redundant Azure container Registry instellen met andere [ondersteunde Azure-resources](../availability-zones/az-region.md) om te voldoen aan gegevens locatie of andere vereisten voor naleving, terwijl er hoge Beschik baarheid binnen een regio wordt geboden.

Azure Container Registry biedt ook ondersteuning voor [geo-replicatie](container-registry-geo-replication.md), waarmee de service wordt gerepliceerd over meerdere regio's, waardoor redundantie en beschik baarheid worden berekend voor het berekenen van resources op andere locaties. De combi natie van beschikbaarheids zones voor redundantie binnen een regio en geo-replicatie tussen meerdere regio's, verbetert de betrouw baarheid en prestaties van een REGI ster.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. Elke zone heeft een of meer data centers met onafhankelijke voeding, koeling en netwerken. Wanneer het is geconfigureerd voor zone redundantie, wordt een REGI ster (of een register replica in een andere regio) gerepliceerd over alle beschikbaarheids zones in de regio, zodat deze beschikbaar blijven als er problemen met data centers zijn.

## <a name="create-a-zone-redundant-registry---cli"></a>Een zone maken-redundant REGI ster-CLI

Als u de Azure CLI wilt gebruiken om zone redundantie in te scha kelen, hebt u Azure CLI versie 2.17.0 of hoger nodig of Azure Cloud Shell. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Als dat nodig is, voert u de opdracht [AZ Group Create](/cli/azure/group#az_group_create) uit om een resource groep te maken voor het REGI ster.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>REGI ster met zone-ingeschakeld maken

Voer de opdracht [AZ ACR Create](/cli/azure/acr?view=azure-cli-latest#az_acr_create) uit om een zone-redundant REGI ster te maken in de laag Premium-Service. Kies een regio die [beschikbaarheids zones](../availability-zones/az-region.md) voor Azure container Registry ondersteunt. In het volgende voor beeld wordt zone redundantie ingeschakeld in de regio *oostus* . Raadpleeg de `az acr create` Help van de opdracht voor meer Register opties.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

In de uitvoer van de opdracht noteert u de `zoneRedundancy` eigenschap van het REGI ster. Wanneer deze functie is ingeschakeld, is het REGI ster zone redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Zone-redundante replicatie maken

Voer de opdracht [AZ ACR Replication Create](/cli/azure/acr/replication?view=azure-cli-latest#az_acr_replication_create) uit om een zone-redundante register replica te maken in een regio die [beschikbaarheids zones ondersteunt](../availability-zones/az-region.md) voor Azure container Registry, zoals *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
In de uitvoer van de opdracht noteert u de `zoneRedundancy` eigenschap voor de replica. Wanneer deze functie is ingeschakeld, is de replica zone redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Een zone maken-redundant REGI ster-Portal

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Selecteer **Een resource maken** > **Containers** > **Container Registry**.
1. Op het tabblad **basis beginselen** selecteert of maakt u een resource groep en voert u een unieke register naam in. 
1. Selecteer bij **locatie** een regio die ondersteuning biedt voor zone redundantie voor Azure container Registry, zoals *VS-Oost*.
1. Selecteer **Premium** in **SKU**.
1. Selecteer **ingeschakeld** in **beschikbaarheids zones**.
1. Desgewenst kunt u aanvullende register instellingen configureren en vervolgens bekijken en **maken** selecteren.
1. Selecteer **maken** om het register exemplaar te implementeren.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Zone redundantie inschakelen in Azure Portal":::

Een zone-redundante replicatie maken:

1. Navigeer naar het container register voor de Premium-laag en selecteer **replicaties**.
1. Op de kaart die wordt weer gegeven, selecteert u een groene zeshoek in een regio die ondersteuning biedt voor zone redundantie voor Azure Container Registry, zoals **VS-West 2**. Of selecteer **+ toevoegen**.
1. Bevestig in het venster **replicatie maken** de **locatie**. Selecteer **ingeschakeld** in **beschikbaarheids zones** en selecteer vervolgens **maken**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Zone-redundante replicatie inschakelen in Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Een zone maken-redundant REGI ster-sjabloon

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Als dat nodig is, voert u de opdracht [AZ Group Create](/cli/azure/group#az_group_create) uit om een resource groep te maken voor het REGI ster in een regio die [beschikbaarheids zones ondersteunt](../availability-zones/az-region.md) voor Azure container Registry, zoals *oostus*. Deze regio wordt gebruikt door de sjabloon om de register locatie in te stellen.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>De sjabloon implementeren 

U kunt de volgende Resource Manager-sjabloon gebruiken om een zone-redundant, geo-gerepliceerd REGI ster te maken. Met de sjabloon wordt standaard zone redundantie in het REGI ster en een regionale replica ingeschakeld. 

Kopieer de volgende inhoud naar een nieuw bestand en sla het op met een bestands naam zoals `registryZone.json` .

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

Voer de volgende opdracht [AZ Deployment Group Create](/cli/azure/group/deployment?view=azure-cli-latest#az_group_deployment_create) uit om het REGI ster te maken met het voor gaande sjabloon bestand. Indien aangegeven, bieden:

* een unieke register naam of implementeer de sjabloon zonder para meters, zodat er een unieke naam voor u wordt gemaakt
* een locatie voor de replica die beschikbaarheids zones ondersteunt, zoals *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

In de uitvoer van de opdracht noteert u de `zoneRedundancy` eigenschap voor het REGI ster en de replica. Wanneer deze functie is ingeschakeld, is elke bron zone redundant:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [regio's die beschikbaarheids zones ondersteunen](../availability-zones/az-region.md).
* Meer informatie over het bouwen van [betrouw baarheid](/azure/architecture/framework/resiliency/overview) in Azure.