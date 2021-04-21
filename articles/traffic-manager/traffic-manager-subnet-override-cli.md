---
title: Azure Traffic Manager subnet overschrijven met behulp van Azure CLI | Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe Traffic Manager subnet-overschrijven kan worden gebruikt om de routeringsmethode van een Traffic Manager-profiel te overschrijven om verkeer naar een eindpunt te leiden op basis van het IP-adres van de eindgebruiker via vooraf gedefinieerde IP-bereik naar eindpunttoewijzingen.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767795"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Traffic Manager subnet overschrijven met behulp van Azure CLI

Traffic Manager subnet kunt u de routeringsmethode van een profiel wijzigen.  De toevoeging van een overschrijven leidt verkeer op basis van het IP-adres van de eindgebruiker met een vooraf gedefinieerd IP-bereik naar eindpunttoewijzing. 

## <a name="how-subnet-override-works"></a>Hoe subnet-overschrijven werkt

Wanneer subnetoverschrijvingen worden toegevoegd aan een Traffic Manager-profiel, controleert Traffic Manager eerst of er een subnetoverschrijvingen zijn voor het IP-adres van de eindgebruiker. Als er een wordt gevonden, wordt de DNS-query van de gebruiker omgeleid naar het bijbehorende eindpunt.  Als er geen toewijzing wordt gevonden, Traffic Manager terugvallen op de oorspronkelijke routeringsmethode van het profiel. 

De IP-adresbereiken kunnen worden opgegeven als CIDR-adresbereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereiken (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-adresbereiken die aan elk eindpunt zijn gekoppeld, moeten uniek zijn voor dat eindpunt. Elke overlapping van IP-adresbereiken tussen verschillende eindpunten zorgt ervoor dat het profiel wordt geweigerd door Traffic Manager.

Er zijn twee soorten routeringsprofielen die ondersteuning bieden voor subnet-overschrijvingen:

* **Geografisch:** als Traffic Manager subnet-overschrijvingen vindt voor het IP-adres van de DNS-query, wordt de query doorgeleid naar het eindpunt, ongeacht de status van het eindpunt.
* **Prestaties:** als Traffic Manager subnet-overschrijvingen vindt voor het IP-adres van de DNS-query, wordt het verkeer alleen naar het eindpunt gerouted als het in orde is.  Traffic Manager wordt terugvallen op de heuristiek van de prestatieroutering als het eindpunt voor het overschrijven van het subnet niet in orde is.

## <a name="create-a-traffic-manager-subnet-override"></a>Een subnet-overschrijvingen Traffic Manager een nieuw subnet maken

Als u een Traffic Manager subnet-overschrijven wilt maken, kunt u Azure CLI gebruiken om de subnetten voor de overschrijven toe te voegen aan het Traffic Manager eindpunt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Werk het Traffic Manager eindpunt bij met subnet-overschrijvingen.
Gebruik Azure CLI om uw eindpunt bij te werken [met az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

U kunt de IP-adresbereiken verwijderen door [de eindpuntupdate az network traffic-manager uit te uitvoeren](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) met de optie **--remove.**

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Traffic Manager [verkeersrouteringsmethoden](traffic-manager-routing-methods.md).

Meer informatie over de [verkeersrouteringsmethode Subnet](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)