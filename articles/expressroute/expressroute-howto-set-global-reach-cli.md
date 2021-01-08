---
title: 'Azure ExpressRoute: ExpressRoute Global Reach configureren: CLI'
description: Leer hoe u ExpressRoute-circuits kunt koppelen om een privé netwerk te maken tussen uw on-premises netwerken en Global Reach in te scha kelen met behulp van de Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 27f16ac7d7d799c5467b11fd93352dc5fdef666c
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028060"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>ExpressRoute Global Reach configureren met behulp van de Azure CLI

Dit artikel helpt u bij het configureren van Azure ExpressRoute Global Reach met behulp van de Azure CLI. Zie [ExpressRoute Global Reach](expressroute-global-reach.md) voor meer informatie.
 
Voordat u begint met de configuratie, moet u de volgende vereisten volt ooien:

* Installeer de nieuwste versie van de Azure CLI. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli).
* Meer informatie over de [werk stroom](expressroute-workflows.md)voor het inrichten van ExpressRoute-circuits.
* Zorg ervoor dat uw ExpressRoute-circuits de status provisioned hebben.
* Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd op uw ExpressRoute-circuits.  

### <a name="sign-in-to-your-azure-account"></a>Aanmelden bij uw Azure-account

Meld u aan bij uw Azure-account om de configuratie te starten. Met de volgende opdracht wordt de standaard browser geopend en wordt u gevraagd om de aanmeldings referenties voor uw Azure-account:  

```azurecli
az login
```

Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account:

```azurecli
az account list
```

Geef het abonnement op dat u wilt gebruiken:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Uw ExpressRoute-circuits identificeren voor configuratie

U kunt ExpressRoute inschakelen Global Reach tussen twee ExpressRoute-circuits. De circuits moeten zich in ondersteunde landen/regio's bevinden en zijn gemaakt op verschillende peering-locaties. Als uw abonnement eigenaar is van beide circuits, kunt u een van beide circuits selecteren om de configuratie uit te voeren. Als de twee circuits zich echter in verschillende Azure-abonnementen bevinden, moet u een autorisatie sleutel maken op basis van een van de circuits. Met de autorisatie sleutel die is gegenereerd op basis van het eerste circuit, kunt u Global Reach inschakelen op het tweede circuit.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Connectiviteit tussen uw on-premises netwerken inschakelen

Wanneer u de opdracht uitvoert om connectiviteit in te scha kelen, moet u rekening houden met de volgende vereisten voor parameter waarden:

* *peer-circuit* moet de volledige resource-id zijn. Bijvoorbeeld:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* het *adres voorvoegsel* moet een '/29 ' IPv4-subnet zijn (bijvoorbeeld: ' 10.0.0.0/29 '). We gebruiken IP-adressen in dit subnet om verbinding te maken tussen de twee ExpressRoute-circuits. U kunt geen adressen gebruiken in dit subnet in uw Azure Virtual Networks of in uw on-premises netwerken.

Voer de volgende CLI-opdracht uit om twee ExpressRoute-circuits te verbinden:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

De CLI-uitvoer ziet er als volgt uit:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Wanneer deze bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Connectiviteit tussen ExpressRoute-circuits in verschillende Azure-abonnementen inschakelen

Als de twee circuits zich niet in hetzelfde Azure-abonnement bevinden, hebt u autorisatie nodig. In de volgende configuratie genereert u autorisatie in het abonnement van circuit 2. Vervolgens geeft u de autorisatie sleutel door aan Circuit 1.

1. Een autorisatie sleutel genereren:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   De CLI-uitvoer ziet er als volgt uit:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Noteer zowel de resource-ID als de autorisatie sleutel voor circuit 2.

1. Voer de volgende opdracht uit met Circuit 1, waarbij de resource-ID en autorisatie sleutel van circuit 2 worden door gegeven:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Wanneer deze bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="get-and-verify-the-configuration"></a>De configuratie ophalen en verifiëren

Gebruik de volgende opdracht om de configuratie te controleren van het circuit waarin de configuratie is gemaakt (Circuit 1 in het vorige voor beeld):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

In de CLI-uitvoer ziet u *CircuitConnectionStatus*. Hiermee wordt aangegeven of de verbinding tussen de twee circuits tot stand is gebracht (' verbonden ') of niet is ingesteld (' losgekoppeld '). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Connectiviteit tussen uw on-premises netwerken uitschakelen

Als u de verbinding wilt uitschakelen, voert u de volgende opdracht uit op het circuit waarin de configuratie is gemaakt (Circuit 1 in het vorige voor beeld).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Gebruik de ```show``` opdracht om de status te controleren.

Wanneer deze bewerking is voltooid, hebt u niet langer de connectiviteit tussen uw on-premises netwerken via uw ExpressRoute-circuits.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ExpressRoute Global Reach](expressroute-global-reach.md)
* [ExpressRoute-connectiviteit controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Een ExpressRoute-circuit koppelen aan een virtueel netwerk](expressroute-howto-linkvnet-arm.md)
