---
title: 'Zelfstudie: Peering configureren voor een ExpressRoute-circuit - Azure CLI'
description: In deze zelfstudie wordt uitgelegd hoe u de persoonlijke peering, openbare peering en Microsoft-peering van een ExpressRoute-circuit maakt en inricht. In dit artikel leest u hoe u de status controleert en peerings voor uw circuit bijwerkt of verwijdert.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2c56e847e3b112d50285cd2c116c8f22efbc507f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715527"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Zelfstudie: Peering voor een ExpressRoute-circuit maken en wijzigen met CLI

In deze zelfstudie wordt beschreven hoe u een routeringsconfiguratie/peering maakt en beheert voor een ExpressRoute-circuit in het Resource Manager-implementatiemodel met behulp van CLI. U kunt ook de status van de peering voor een ExpressRoute-circuit controleren en peerings bijwerken of verwijderen en de inrichting ervan ongedaan maken. Als u een andere methode voor uw circuit wilt gebruiken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Openbare peering](about-public-peering.md)
> * [Video - Persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Microsoft-peering voor een circuit configureren, bijwerken en verwijderen
> - Persoonlijke Azure-peering voor een circuit configureren, bijwerken en verwijderen

## <a name="prerequisites"></a>Vereisten

* Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.
* Zorg dat u de pagina's met [vereisten](expressroute-prerequisites.md), [routeringsvereisten](expressroute-routing.md) en [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zijn ingericht en zijn ingeschakeld om de opdrachten in dit artikel te kunnen uitvoeren.

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde Laag-3-services aanbiedt (meestal een IPVPN, zoals MPLS), zal de connectiviteitsprovider routering voor u configureren en beheren.

U kunt persoonlijke peering en Microsoft-peering configureren voor een ExpressRoute-circuit. Peerings kunnen worden geconfigureerd in elke gewenste volgorde. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [ExpressRoute-routeringsdomeinen](expressroute-circuit-peerings.md) voor meer informatie over routeringsdomeinen en peerings.

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor Microsoft-peering voor een ExpressRoute-circuit.

> [!IMPORTANT]
> Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via de Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd. Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld. Zie [Een routefilter configureren voor Microsoft-peering](how-to-routefilter-powershell.md) voor meer informatie.
> 


### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Installeer de nieuwste versie van Azure CLI. Gebruik de nieuwste versie van Azure CLI (de opdrachtregelinterface van Azure).

   ```azurecli
   az login
   ```

   Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

1. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om Microsoft-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als u het circuit hebt gemaakt en uw connectiviteitsprovider routering niet voor u beheert, gaat u verder met de volgende configuratiestappen. 

1. Controleer of het ExpressRoute-circuit is ingericht en ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurecli
   az network express-route list
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verdergaat.

   * Een /30-subnet voor de primaire koppeling. Het adresblok moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een /30-subnet voor de secundaire koppeling. Het adresblok moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: Verstrek een lijst van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U kunt een met komma's gescheiden lijst verzenden als u een set voorvoegsels wilt verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel -** Klant-ASN: Als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarbij ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel -** Een MD5-hash, als u er een wilt gebruiken.

   Voer het volgende voorbeeld uit om Microsoft-peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U kunt de configuratiegegevens weergeven met behulp van het volgende voorbeeld:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft controleert of de opgegeven geadverteerde openbare voorvoegsels en peer-ASN (of klant-ASN) aan u zijn toegewezen in het IRR (Internet Routing Register). Als u de openbare voorvoegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routeringsregister, wordt de automatische validatie niet voltooid en is handmatig validatie vereist. Als de automatische validatie mislukt, ziet u 'AdvertisedPublicPrefixesState' bij 'Validatie vereist' in de uitvoer van de bovenstaande opdracht. 
> 
> Als u het bericht 'Validatie vereist' ziet, verzamelt u de documenten met de openbare voorvoegsels die zijn toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en verzendt u deze documenten voor handmatige validatie door een ondersteuningsticket te openen. 
> 
>

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U kunt elk deel van de configuratie bijwerken. De geadverteerde voorvoegsels van het circuit worden bijgewerkt van 123.1.0.0/24 naar 124.1.0.0/24 in het volgende voorbeeld:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>IPv6-instellingen voor Microsoft-peering toevoegen aan een bestaande IPv4-configuratie

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een persoonlijke Azure-peering voor een ExpressRoute-circuit.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Installeer de nieuwste versie van Azure CLI.
1. 
   ```azurecli
   az login
   ```

   Het abonnement selecteren waarmee u een ExpressRoute-circuit wilt maken

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
1. Maak een ExpressRoute-circuit. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om persoonlijke Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als u het circuit hebt gemaakt en uw connectiviteitsprovider routering niet voor u beheert, gaat u verder met de volgende configuratiestappen.

1. Controleer of het ExpressRoute-circuit is ingericht en ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

1. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een paar subnetten die geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken. Eén /30-subnet wordt gebruikt voor de primaire koppeling en het andere wordt gebruikt voor de secundaire koppeling. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt. Er zijn drie opties voor dit paar subnetten:
       * IPv4: twee/30 subnetten.
       * IPv6: twee/126 subnetten.
       * Beide: twee/30 subnetten en twee/126 subnetten.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Gebruik niet 65515.
   * **Optioneel -** Een MD5-hash, als u er een wilt gebruiken.

   Gebruik het volgende voorbeeld om persoonlijke Azure-peering voor uw circuit te configureren:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt de configuratiegegevens weergeven met behulp van het volgende voorbeeld:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met het volgende voorbeeld. In dit voorbeeld wordt de VLAN-id van het circuit gewijzigd van 100 in 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt een peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren.

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt een peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

> [!WARNING]
> Zorg ervoor dat alle virtuele netwerken en ExpressRoute Global Reach-verbindingen zijn verwijderd voordat u dit voorbeeld uitvoert. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="next-steps"></a>Volgende stappen

Nadat u persoonlijke Azure-peering hebt geconfigureerd, kunt u virtuele netwerken koppelen aan het circuit. Zie voor meer informatie: 

> [!div class="nextstepaction"]
> [Een VNet koppelen aan een ExpressRoute-circuit](howto-linkvnet-cli.md)
