---
title: 'Snelstart: Een ExpressRoute-circuit maken en wijzigen: Azure CLI'
description: In deze quickstart leert u hoe u met behulp van de Azure CLI een ExpressRoute-circuit kunt maken, inrichten, controleren, verwijderen en de inrichting ervan ongedaan kunt maken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969055"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Snelstart: Een ExpressRoute-circuit maken en wijzigen met behulp van de Azure CLI

In deze quickstart wordt beschreven hoe u een Azure ExpressRoute-circuit maakt met behulp van de CLI of opdrachtregelinterface. U leest hier ook hoe u de status controleert, en een circuit bijwerkt, verwijdert of de inrichting ervan ongedaan maakt.

## <a name="prerequisites"></a>Vereisten

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installeer de nieuwste versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Aanmelden bij uw Azure-account en uw abonnement selecteren

Meld u aan bij uw Azure-account om de configuratie te starten. Als u de Cloud Shell gebruikt via de knop 'Proberen', wordt u automatisch aangemeld. Gebruik de volgende voorbeelden als hulp bij het maken van verbinding:

```azurecli-interactive
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>De lijst met ondersteunde providers, locaties en bandbreedten ophalen

U kunt pas een ExpressRoute-circuit maken als u beschikt over de lijst met ondersteunde connectiviteitsproviders, locaties en bandbreedte-opties. De CLI-opdracht `az network express-route list-service-providers` retourneert deze informatie, die u in latere stappen nodig hebt:

```azurecli-interactive
az network express-route list-service-providers
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Controleer het antwoord om te zien of uw connectiviteitsprovider wordt vermeld. Noteer de volgende gegevens, aangezien u die nodig hebt bij het maken van een circuit:

* Naam
* PeeringLocations
* BandwidthsOffered

U bent nu klaar om een ExpressRoute-circuit te maken.

### <a name="create-an-expressroute-circuit"></a>Een ExpressRoute-circuit maken

> [!IMPORTANT]
> Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Voer deze bewerking uit wanneer de connectiviteitsprovider klaar is om het circuit in te richten.
>
>

Als u nog geen resourcegroep hebt, moet u er een maken voordat u het ExpressRoute-circuit maakt. U kunt een resourcegroep maken door de volgende opdracht uit te voeren:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

In het volgende voorbeeld ziet u hoe u een ExpressRoute-circuit van 200 Mbps maakt met behulp van Equinix in Silicon Valley. Als u een andere provider en andere instellingen gebruikt, vervangt u de gegevens uit het voorbeeld door uw gegevens wanneer u uw aanvraag verstuurt.

Zorg ervoor dat u de juiste SKU-categorie en SKU-familie opgeeft:

* De SKU-categorie bepaalt of een ExpressRoute-circuit [Local](expressroute-faqs.md#expressroute-local), Standard of [Premium](expressroute-faqs.md#expressroute-premium) is. U kunt *Local*, *Standard of *Premium* opgeven. U kunt de SKU niet wijzigen van *Standard/Premium* in *Local*.
* De SKU-familie bepaalt het type facturering. U kunt *MeteredData* opgeven voor een data-abonnement met een datalimiet en *UnlimitedData* voor een onbeperkt data-abonnement. U kunt het type facturering wijzigen van *MeteredData* in *UnlimitedData*, maar niet van *UnlimitedData* in *MeteredData*. Een *Local* circuit kan alleen *UnlimitedData* zijn.


Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Het volgende voorbeeld is een aanvraag voor een nieuwe servicesleutel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Het antwoord bevat de servicesleutel.

### <a name="list-all-expressroute-circuits"></a>Alle ExpressRoute-circuits opvragen

Als u een lijst wilt weergeven met alle ExpressRoute-circuits die u hebt gemaakt, voert u de opdracht `az network express-route list` uit. U kunt deze informatie op elk gewenst moment ophalen met behulp van deze opdracht. Als u alle circuits wilt weergeven, doet u de aanroep zonder parameters.

```azurecli-interactive
az network express-route list
```

Uw servicesleutel wordt vermeld in het veld *ServiceKey* van het antwoord.

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

U kunt gedetailleerde beschrijvingen van alle parameters opvragen door de opdracht uit te voeren met de parameter -h.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>De servicesleutel voor inrichting verzenden naar de connectiviteitsprovider

'ServiceProviderProvisioningState' bevat informatie over de huidige status van de inrichting aan de zijde van de serviceprovider. 'circuitProvisioningState' vermeldt de status aan de zijde van Microsoft. Zie voor meer informatie [het artikel over workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Wanneer u een nieuw ExpressRoute-circuit maakt, heeft het circuit de volgende status:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Wanneer de connectiviteitsprovider bezig is met het inschakelen van uw circuit, verandert de status van het circuit in:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Als u het ExpressRoute-circuit wilt gebruiken, moet het de volgende status hebben:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Controleer regelmatig de circuitstatus en de status van de circuitsleutel

Als u de circuitstatus en de status van de servicesleutel controleert, kunt u zien wanneer uw provider uw circuit heeft ingericht. Nadat het circuit is geconfigureerd, wordt *ServiceProviderProvisioningState* weergegeven als *Provisioned*, zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>De routeringsconfiguratie maken

Raadpleeg het artikel over [de routeringsconfiguratie voor ExpressRoute-circuits](howto-routing-cli.md) voor stapsgewijze instructies over het maken en wijzigen van circuitpeerings.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde laag-3-services aanbiedt (meestal een IP-adres voor VPN, zoals MPLS), wordt de routering voor u geconfigureerd en beheerd via de connectiviteitsprovider.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Een virtueel netwerk koppelen aan een ExpressRoute-circuit

Koppel vervolgens een virtueel netwerk aan een ExpressRoute-circuit. Raadpleeg hiervoor het artikel over [het koppelen van virtuele netwerken aan ExpressRoute-circuits](howto-linkvnet-cli.md).

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen

U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit van invloed is op de connectiviteit. U kunt de volgende wijzigingen aanbrengen zonder downtime:

* U kunt een Premium-invoegtoepassing in- of uitschakelen voor uw ExpressRoute-circuit. Het is niet mogelijk om de SKU te wijzigen van *Standard/Premium* in *Local*.
* U kunt de bandbreedte van uw ExpressRoute-circuit vergroten, op voorwaarde dat er capaciteit beschikbaar is op de poort. Het verminderen van de bandbreedte van een circuit wordt daarentegen niet ondersteund.
* U kunt het verbruiksabonnement wijzigen van Datalimiet in Onbeperkte data. Het verbruiksabonnement wijzigen van Onbeperkte data in Datalimiet wordt echter niet ondersteund.
* U kunt *klassieke bewerkingen toestaan* in- en uitschakelen.

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over limieten en beperkingen.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Premium-invoegtoepassing voor ExpressRoute inschakelen

U kunt de ExpressRoute Premium-invoegtoepassing inschakelen voor uw bestaande circuit met behulp van de volgende opdracht:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

De functies van de ExpressRoute Premium-invoegtoepassing zijn nu ingeschakeld voor het circuit. U wordt gefactureerd voor de mogelijkheden van de Premium-invoegtoepassing zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Premium-invoegtoepassing voor ExpressRoute uitschakelen

> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan wat is toegestaan voor het Standard-circuit.
>
>

Voordat u de ExpressRoute Premium-invoegtoepassing uitschakelt, is het belangrijk dat u de volgende criteria begrijpt:

* Voordat u een downgrade uitvoert van Premium naar Standard, moet u ervoor zorgen dat het aantal virtuele netwerken dat is gekoppeld aan het circuit, kleiner is dan 10. Als u dat niet doet, mislukt de aanvraag en wordt u gefactureerd tegen Premium-tarieven.
* Alle virtuele netwerken in andere geopolitieke regio's moeten eerst worden ontkoppeld. Als u dat niet doet, mislukt de aanvraag en blijven we u factureren tegen Premium-tarieven.
* De routetabel moet kleiner zijn dan 4.000 routes voor persoonlijke peering. Als uw routetabel groter is dan 4.000 routes, wordt de BGP-sessie beëindigd. De BGP-sessie wordt pas weer ingeschakeld als het aantal geadverteerde voorvoegsels minder is dan 4.000.

U kunt de ExpressRoute Premium-invoegtoepassing uitschakelen voor het bestaande circuit met behulp van het volgende voorbeeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>De bandbreedte van het ExpressRoute-circuit bijwerken

Informatie over de ondersteunde bandbreedte-opties voor uw provider vindt u in de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md). U kunt elke grootte kiezen die groter is dan de grootte van uw bestaande circuit.

> [!IMPORTANT]
> Als er onvoldoende capaciteit is op de bestaande poort, moet u het ExpressRoute-circuit mogelijk opnieuw maken. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op de betreffende locatie.
>
> Het is niet mogelijk om de bandbreedte van een ExpressRoute-circuit zonder onderbreking te verminderen. Voor het verminderen van de bandbreedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken, en vervolgens een nieuw ExpressRoute-circuit inrichten.
>

Nadat u hebt bepaald welke grootte u nodig hebt, gebruikt u de volgende opdracht om de grootte van het circuit te wijzigen:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Uw circuit wordt bijgewerkt aan de kant van Microsoft. Vervolgens moet u contact opnemen met uw connectiviteitsprovider om configuraties aan hun zijde bij te werken met deze wijziging. Nadat u deze melding hebt gedaan, wordt u gefactureerd voor de bijgewerkte bandbreedte-optie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>De SKU wijzigen van Datalimiet in Onbeperkte data

U kunt de SKU van een ExpressRoute-circuit wijzigen met behulp van het volgende voorbeeld:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>De toegang tot de klassieke omgeving en de Resource Manager-omgeving beheren

Lees de instructies in het artikel over het [verplaatsen van ExpressRoute-circuits van het klassieke naar het Resource Manager-implementatiemodel](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>De inrichting van een ExpressRoute-circuit ongedaan maken

Als u het inrichten van een ExpressRoute-circuit ongedaan wilt maken om het circuit te verwijderen, is het belangrijk dat u de volgende criteria begrijpt:

* Alle virtuele netwerken moeten worden losgekoppeld van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de serviceprovider van het circuit de inrichtingsstatus **Inrichten** of **Ingericht** heeft, moet u contact opnemen met de serviceprovider om de inrichting van het circuit aan hun zijde ongedaan te maken. We blijven resources reserveren en kosten in rekening brengen, totdat de serviceprovider de inrichting van het circuit helemaal ongedaan heeft gemaakt en ons op de hoogte heeft gesteld.
* Als de serviceprovider dit heeft gedaan (de inrichtingsstatus bij de serviceprovider is ingesteld op **NotProvisioned**), kunt u het circuit verwijderen. De facturering voor het circuit wordt dan gestopt.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt en het circuit is ingericht bij uw provider, gaat u verder met de volgende stap om de peering te configureren:

> [!div class="nextstepaction"]
> [Routering voor uw ExpressRoute-circuit maken en wijzigen](howto-routing-cli.md)
