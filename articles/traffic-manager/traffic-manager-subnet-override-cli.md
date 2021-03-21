---
title: Het subnet van Azure Traffic Manager worden overschreven met behulp van Azure CLI | Microsoft Docs
description: Dit artikel helpt u inzicht te krijgen in de manier waarop Traffic Manager subnet overschrijving kan worden gebruikt om de routerings methode van een Traffic Manager profiel te onderdrukken voor het omleiden van verkeer naar een eind punt op basis van het IP-adres van de eind gebruiker met behulp van een vooraf gedefinieerd IP-bereik voor eindpunt toewijzingen.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211325"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Onderdrukking van het subnet Traffic Manager met behulp van Azure CLI

Met Traffic Manager subnet override kunt u de routerings methode van een profiel wijzigen.  Door het toevoegen van een onderdrukking wordt verkeer doorgestuurd op basis van het IP-adres van de eind gebruiker met een vooraf gedefinieerd IP-bereik voor de toewijzing van eind punten. 

## <a name="how-subnet-override-works"></a>Hoe subnet overschrijving werkt

Wanneer subnet onderdrukkingen worden toegevoegd aan een Traffic Manager-profiel, wordt door Traffic Manager eerst gecontroleerd of er een subnet wordt overschreven voor het IP-adres van de eind gebruiker. Als er een wordt gevonden, wordt de DNS-query van de gebruiker omgeleid naar het bijbehorende eind punt.  Als er geen toewijzing wordt gevonden, wordt Traffic Manager terugvallen op de oorspronkelijke routerings methode van het profiel. 

De IP-adresbereiken kunnen worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereiken (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-bereiken die zijn gekoppeld aan elk eind punt, moeten uniek zijn voor dat eind punt. Elke overlap ping van IP-bereiken tussen verschillende eind punten leidt ertoe dat het profiel wordt geweigerd door Traffic Manager.

Er zijn twee typen routerings profielen die ondersteuning bieden voor subnet onderdrukkingen:

* **Geografisch** : als Traffic Manager een onderdrukking van het subnet voor het IP-adres van de DNS-query vindt, stuurt de query door naar het eind punt, ongeacht de status van het eind punt.
* **Prestaties** : als Traffic Manager een onderdrukking van het subnet voor het IP-adres van de DNS-query vindt, wordt het verkeer alleen doorgestuurd naar het eind punt als dit in orde is.  Traffic Manager gaat terug naar de prestatie routering heuristisch als het subnet onderdrukkings eindpunt niet in orde is.

## <a name="create-a-traffic-manager-subnet-override"></a>Een Traffic Manager subnet overschrijven maken

Als u een Traffic Manager subnet overschrijven wilt maken, kunt u Azure CLI gebruiken om de subnetten voor de onderdrukking toe te voegen aan het Traffic Manager-eind punt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Werk het Traffic Manager-eind punt bij met het overschrijven van het subnet.
Gebruik Azure CLI om uw eind punt bij te werken met [AZ Network Traffic-Manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

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

U kunt de IP-adresbereiken verwijderen door het uitvoeren van de [Update AZ Network Traffic-Manager endpoint](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) met de optie **--Remove** .

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over methoden voor het [routeren](traffic-manager-routing-methods.md)van Traffic Manager verkeer.

Meer informatie over het [subnet Traffic-routerings methode](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)