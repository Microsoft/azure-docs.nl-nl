---
title: Open bare Azure ExpressRoute-peering maken en beheren
description: Meer informatie over het beheren van open bare Azure-peering
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202545"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Open bare ExpressRoute-peering maken en beheren

> [!div class="op_single_selector"]
> * [Artikel-open bare peering](about-public-peering.md)
> * [Video-open bare peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artikel-micro soft-peering](expressroute-circuit-peerings.md#microsoftpeering)
>

Dit artikel helpt u bij het maken en beheren van een configuratie voor een open bare peering-routering voor een ExpressRoute-circuit. U kunt ook de status van peerings controleren, bijwerken of verwijderen en de inrichting ongedaan maken. Dit artikel is van toepassing op de Resource Manager-circuits die zijn gemaakt voordat open bare peering werd afgeschaft. Als u een eerder bestaand circuit hebt (gemaakt voordat open bare peering wordt afgeschaft), kunt u open bare peering beheren/configureren met [Azure PowerShell](#powershell), [Azure CLI](#cli)en de [Azure Portal](#portal).

>[!NOTE]
>Open bare peering is afgeschaft. U kunt geen open bare peering maken op nieuwe ExpressRoute-circuits. Als u een nieuw ExpressRoute-circuit hebt, gebruikt u in plaats daarvan [micro soft-peering](expressroute-circuit-peerings.md#microsoftpeering) voor uw Azure-Services.
>

## <a name="connectivity"></a>Connectiviteit

Connectiviteit wordt altijd geïnitieerd vanuit uw WAN naar Microsoft Azure Services. Microsoft Azure Services kunnen geen verbindingen met uw netwerk initiëren via dit routerings domein. Als uw ExpressRoute-circuit is ingeschakeld voor open bare Azure-peering, kunt u toegang krijgen tot de [open bare IP-bereiken die in Azure worden gebruikt](../virtual-network/public-ip-addresses.md#public-ip-addresses) via het circuit.

Zodra open bare peering is ingeschakeld, kunt u verbinding maken met de meeste Azure-Services. U kunt niet selectief Services picken waarvoor we routes aankondigen.

* Services als Azure Storage, SQL-data bases en websites worden aangeboden op open bare IP-adressen.
* Via het routerings domein voor open bare peering kunt u privé verbinding maken met services die worden gehost op open bare IP-adressen, inclusief Vip's van uw Cloud Services.
* U kunt het open bare peering domein koppelen aan uw DMZ en verbinding maken met alle Azure-Services op hun open bare IP-adres van uw WAN zonder dat u verbinding hoeft te maken via internet.

## <a name="services"></a><a name="services"></a>Services

In deze sectie worden de services weer gegeven die beschikbaar zijn via open bare peering. Omdat open bare peering is afgeschaft, is er geen plan om nieuwe of extra services aan open bare peering toe te voegen. Als u open bare peering gebruikt en de service die u wilt gebruiken alleen wordt ondersteund voor micro soft-peering, moet u overschakelen naar micro soft-peering. Zie [micro soft-peering](expressroute-faqs.md#microsoft-peering) voor een lijst met ondersteunde services.

**Geboden**

* Power BI
* De meeste Azure-Services worden ondersteund. Neem rechtstreeks contact op met de service die u wilt gebruiken om de ondersteuning te controleren.

**Niet ondersteund:**
  * CDN
  * Azure Front Door
  * Multi-factor Authentication-Server (verouderd)
  * Traffic Manager

Als u de beschik baarheid voor een specifieke service wilt valideren, kunt u de documentatie voor die service controleren om te zien of er een gereserveerd bereik voor die service is gepubliceerd. Vervolgens kunt u de IP-bereiken van de doel service opzoeken en vergelijken met de bereiken die worden vermeld in het [Azure IP-bereik en de service Tags – Public Cloud XML-bestand](https://www.microsoft.com/download/details.aspx?id=56519). U kunt ook een ondersteunings ticket voor de betreffende service openen ter verduidelijking.

## <a name="peering-comparison"></a><a name="compare"></a>Peering-vergelijking

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Open bare Azure-peering heeft één NAT IP-adres dat is gekoppeld aan elke BGP-sessie. Voor meer dan 2 NAT IP-adressen, gaat u naar micro soft-peering. Met micro soft-peering kunt u uw eigen NAT-toewijzingen configureren, maar ook route filters gebruiken voor selectieve voor voegsels van advertenties. Zie [verplaatsen naar micro soft-peering](./how-to-move-peering.md)voor meer informatie.
>

## <a name="custom-route-filters"></a>Aangepaste route filters

U kunt aangepaste route filters definiëren in uw netwerk om alleen de routes te gebruiken die u nodig hebt. Raadpleeg de pagina [route ring](expressroute-routing.md) voor gedetailleerde informatie over de routerings configuratie.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell stappen


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Omdat open bare peering is afgeschaft, kunt u open bare peering niet configureren voor een nieuw ExpressRoute-circuit.

1. Controleer of u een ExpressRoute-circuit hebt dat is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Configureer openbare Azure-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Optioneel:
   * Een MD5-hash, als u er een wilt gebruiken.

   Voer het volgende voor beeld uit om open bare Azure-peering voor uw circuit te configureren

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Details van open bare Azure-peering ophalen

U kunt Configuratie Details ophalen met behulp van de volgende cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>De configuratie van openbare Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met het volgende voorbeeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 200 naar 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Openbare Azure-peering verwijderen

U kunt een peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Stappen voor Azure CLI


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Controleer het ExpressRoute-circuit om te controleren of het is ingericht en ook is ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurecli-interactive
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

2. Configureer openbare Azure-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * **Optioneel -** Een MD5-hash, als u er een wilt gebruiken.

   Voer het volgende voor beeld uit om open bare Azure-peering voor uw circuit te configureren:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>De details van openbare Azure-peering weergeven

Gebruik het volgende voorbeeld als u de configuratiegegevens wilt weergeven:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
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

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>De configuratie van openbare Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met het volgende voorbeeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 200 naar 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Openbare Azure-peering verwijderen

U kunt een peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal stappen

Als u peering wilt configureren, gebruikt u de Power shell-of CLI-stappen die in dit artikel zijn opgenomen. Als u een peering wilt beheren, kunt u de onderstaande secties gebruiken. Ter referentie zijn deze stappen vergelijkbaar met [het beheren van een micro soft-peering in de portal](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>De details van openbare Azure-peering weergeven

Bekijk de eigenschappen van open bare Azure-peering door de peering te selecteren in de portal.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>De configuratie van openbare Azure-peering bijwerken

Selecteer de rij voor peering en wijzig vervolgens de eigenschappen van de peering.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Openbare Azure-peering verwijderen

Verwijder de peering-configuratie door het pictogram verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

Volgende stap [koppelt u een virtueel netwerk aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).

* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.