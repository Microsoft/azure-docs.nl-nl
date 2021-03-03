---
title: 'Snelstartgids: route server maken en configureren met behulp van Azure CLI'
description: In deze Quick Start leert u hoe u een route server maakt en configureert met behulp van Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 518baa47fd16d69bf935cd3253f5bebeb413b513
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680615"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Snelstartgids: route server maken en configureren met behulp van Azure CLI 

Dit artikel helpt u bij het configureren van Azure route server naar peer met virtuele netwerk apparaten (NVA) in uw virtuele netwerk met behulp van Azure CLI. Azure route server leert routes van de NVA en Program meren voor de virtuele machines in het virtuele netwerk. Ook worden de routes van het virtuele netwerk naar de NVA geadverteerd. Lees [Azure route server](overview.md)voor meer informatie.

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

##  <a name="prerequisites"></a>Vereisten 

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
* Zorg ervoor dat u de nieuwste Azure CLI hebt, of gebruik Azure Cloud Shell in de portal. 
* Controleer de [service limieten voor Azure route server](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Een route server maken 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement. 

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)] 

### <a name="create-a-resource-group-and-virtual-network"></a>Een resource groep en een virtueel netwerk maken 

Voordat u een Azure-route server kunt maken, hebt u een virtueel netwerk nodig om de implementatie te hosten. Gebruik de volgende opdracht om een resource groep en een virtueel netwerk te maken. Als u al een virtueel netwerk hebt, kunt u door gaan naar de volgende sectie.

```azurecli-interactive
az group create -n “RouteServerRG” -l “westus” 
az network vnet create -g “RouteServerRG” -n “myVirtualNetwork” --address-prefix “10.0.0.0/16” 
``` 

### <a name="add-a-subnet"></a>Een subnet toevoegen 

1. Voeg een subnet met de naam *RouteServerSubnet* toe om de Azure route server in te implementeren. Dit subnet is alleen een specifiek subnet voor Azure route server. De RouteServerSubnet moet/27 of een kortere voor voegsel (zoals/26,/25) zijn of u ontvangt een fout bericht wanneer u de Azure route server toevoegt.

    ```azurecli-interactive 
    az network vnet subnet create -g “RouteServerRG” --vnet-name “myVirtualNetwork” --name “RouteServerSubnet” --address-prefix “10.0.0.0/24”  
    ``` 

1. Haal de RouteServerSubnet-ID op. Als u de resource-ID van alle subnetten in het virtuele netwerk wilt weer geven, gebruikt u deze opdracht: 

    ```azurecli-interactive 
    subnet_id = $(az network vnet subnet show -n “RouteServerSubnet” --vnet-name “myVirtualNetwork” -g “RouteServerRG” --query id -o tsv) 
    ``` 

De RouteServerSubnet-ID ziet er als volgt uit: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>De route server maken 

Maak de route server met de volgende opdracht: 

```azurecli-interactive
az network routeserver create -n “myRouteServer” -g “RouteServerRG” --hosted-subnet $subnet_id  
``` 

De locatie moet overeenkomen met de locatie van het virtuele netwerk. De HostedSubnet is de RouteServerSubnet-ID die u hebt verkregen in de vorige sectie. 

## <a name="create-peering-with-an-nva"></a>Peering maken met een NVA 

Gebruik de volgende opdracht om peering van de route server naar het NVA te maken: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name “myRouteServer” -g “RouteServerRG” --peer-ip “nva_ip” --peer-asn “nva_asn” -n “NVA1_name” 

``` 

"nva_ip" is het IP-adres van het virtuele netwerk dat is toegewezen aan de NVA. "nva_asn" is het autonome systeem nummer (ASN) dat is geconfigureerd in de NVA. Het ASN kan een wille keurig 16-bits nummer zijn dan het aantal in het bereik van 65515-65520. Dit bereik van Asn's is gereserveerd door micro soft. 

Gebruik deze opdracht om peering in te stellen met een ander NVA of een ander exemplaar van hetzelfde NVA voor redundantie:

```azurecli-interactive 

az network routeserver peering create --routeserver-name “myRouteServer” -g “RouteServerRG” --peer-ip “nva_ip” --peer-asn “nva_asn” -n “NVA2_name” 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>De configuratie op de NVA volt ooien 

Als u de configuratie wilt volt ooien op de NVA en de BGP-sessies wilt inschakelen, hebt u het IP-adres en de ASN van Azure route server nodig. U kunt deze informatie ophalen met behulp van de volgende opdracht: 

```azurecli-interactive 
az network routeserver show -g “RouteServerRG” -n “myRouteServer” 
``` 

De uitvoer bevat de volgende informatie. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Route uitwisseling configureren 

Als u een ExpressRoute-gateway en een Azure VPN-gateway in hetzelfde VNet hebt en u wilt dat ze routes uitwisselen, kunt u route Exchange inschakelen op de Azure route server.

> [!IMPORTANT]
> Voor ontwikkel-implementaties moet u de Azure VPN-gateway maken voordat u Azure route server maakt. anders mislukt de implementatie van Azure VPN Gateway.
> 

1. Als u route uitwisseling tussen Azure route server en de gateway (s) wilt inschakelen, gebruikt u deze opdracht:

```azurecli-interactive 
az network routeserver update -g “RouteServerRG” -n “myRouteServer” --allow-b2b-traffic true 

``` 

2. Als u route uitwisseling tussen Azure route server en de gateway (s) wilt uitschakelen, gebruikt u deze opdracht:

```azurecli-interactive
az network routeserver update -g “RouteServerRG” -n “myRouteServer” --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Problemen oplossen 

Met deze opdracht kunt u de routes weer geven die worden geadverteerd en ontvangen door Azure route server:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up"></a>Opschonen 

Als u de Azure route server niet meer nodig hebt, gebruikt u deze opdrachten om de BGP-peering te verwijderen en de route server vervolgens te verwijderen. 

1. Verwijder de BGP-peering tussen Azure route server en een NVA met deze opdracht:

```azurecli-interactive
az network routeserver peering delete --routeserver-name “myRouteServer” -g “RouteServerRG” -n “NVA2_name” 
``` 

2. Azure route server verwijderen met deze opdracht: 

```azurecli-interactive 
az network routeserver delete -n “myRouteServer” -g “RouteServerRG” 
``` 

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure route server hebt gemaakt, gaat u verder met meer informatie over hoe Azure route server communiceert met ExpressRoute en VPN-gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute-en Azure VPN-ondersteuning](expressroute-vpn-support.md)
 
