---
title: 'Azure ExpressRoute: werk stroom voor circuit configuratie'
description: Op deze pagina wordt de werk stroom voor het configureren van ExpressRoute-circuits en peerings weer gegeven
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721732"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-werkstromen voor circuitinrichting en -statussen

Dit artikel begeleidt u bij het inrichten en configureren van werk stromen voor service-instellingen op hoog niveau. De volgende secties bevatten een overzicht van de taken voor het inrichten van een ExpressRoute-circuit end-to-end.

## <a name="workflow-steps"></a>Werk stroom stappen

### <a name="1-prerequisites"></a>1. vereisten

Zorg ervoor dat aan de vereisten wordt voldaan. Zie [vereisten en controle lijst](expressroute-prerequisites.md)voor een volledige lijst.

* Er is een Azure-abonnement gemaakt.
* Fysieke connectiviteit is tot stand gebracht met de ExpressRoute-partner of geconfigureerd via ExpressRoute direct. Bekijk locatie, Zie [locaties en partners](expressroute-locations-providers.md#partners) voor het weer geven van de ExpressRoute-partner en ExpressRoute directe connectiviteit op peering locaties.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. connectiviteit best Ellen of ExpressRoute direct configureren

Volg de verbinding van de service provider of configureer ExpressRoute direct.

#### <a name="expressroute-partner-model"></a>ExpressRoute-partner model

Volg de verbinding van de service provider. Dit proces varieert. Neem contact op met uw connectiviteits provider voor meer informatie over het best Ellen van connectiviteit.

* De ExpressRoute-partner selecteren
* De locatie van de peering selecteren
* De band breedte selecteren
* Het facturerings model selecteren
* Selecteer een standaard-of Premium-invoeg toepassing

#### <a name="expressroute-direct-model"></a>ExpressRoute direct model

* Beschik bare ExpressRoute directe capaciteit op peering-locaties weer geven.
* Reserveer poorten door de ExpressRoute direct-resource te maken in uw Azure-abonnement.
* Vraag de autorisatie brief aan en ontvang deze en bestel de fysieke cross-verbindingen van de provider van de peering locatie.
* Schakel de beheer status in en Bekijk de lichte niveaus en fysieke koppeling met behulp van [Azure monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. een ExpressRoute-circuit maken

#### <a name="expressroute-partner-model"></a>ExpressRoute-partner model

Controleer of de ExpressRoute-partner gereed is om connectiviteit in te richten. Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Gebruik de instructies in [een ExpressRoute-circuit maken](expressroute-howto-circuit-portal-resource-manager.md) om uw circuit te maken.

#### <a name="expressroute-direct-model"></a>ExpressRoute direct model

Zorg ervoor dat de fysieke koppeling en de status van de beheerder op beide koppelingen zijn ingeschakeld. Zie [ExpressRoute direct configureren](how-to-expressroute-direct-portal.md) voor hulp. Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Gebruik de instructies in [een ExpressRoute-circuit maken](expressroute-howto-circuit-portal-resource-manager.md) om uw circuit te maken.

### <a name="4-service-provider-provisions-connectivity"></a>4. connectiviteit van service provider-voorzieningen

Deze sectie is alleen van toepassing op het ExpressRoute partner Connectivity-model:

* Geef de service sleutel (s-Key) op voor de connectiviteits provider.
* Geef aanvullende informatie op die nodig is voor de connectiviteits provider (bijvoorbeeld VPN-ID).
* Als de provider de routerings configuratie beheert, geeft u de benodigde details op.

U kunt ervoor zorgen dat het circuit is ingericht door de inrichtings status van het ExpressRoute-circuit te controleren met behulp van Power shell, de Azure Portal of, CLI.

### <a name="5-configure-routing-domains"></a>5. routerings domeinen configureren

Routerings domeinen configureren. Als uw connectiviteits provider de laag-3 configuratie beheert, wordt route ring voor uw circuit geconfigureerd. Als uw connectiviteits provider alleen Layer 2-services aanbiedt of als u ExpressRoute direct gebruikt, moet u route ring configureren volgens de richt lijnen die worden beschreven in de artikelen [routerings vereisten](expressroute-routing.md) en [routerings configuratie](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>Voor persoonlijke Azure-peering

Schakel privé-peering in om verbinding te maken met Vm's en Cloud Services die zijn geïmplementeerd in het virtuele Azure-netwerk.

* IPv4-subnetten:
    * Peering-subnet voor pad 1 (/30)
    * Peering-subnet voor pad 2 (/30)
* IPv6-subnetten (optioneel):
    * Peering-subnet voor pad 1 (/126)
    * Peering-subnet voor pad 2 (/126)
* VLAN-ID voor peering
* ASN voor peering
* ExpressRoute ASN = 12076
* MD5-hash (optioneel)

#### <a name="for-microsoft-peering"></a>Voor micro soft-peering

Schakel dit in om toegang te krijgen tot micro soft onlineservices, zoals Microsoft 365. Daarnaast zijn alle Azure PaaS-services toegankelijk via micro soft-peering. U moet ervoor zorgen dat u een afzonderlijke proxy/Edge gebruikt om verbinding te maken met micro soft dan het account dat u voor Internet gebruikt. Het gebruik van dezelfde rand voor zowel ExpressRoute als het internet zorgt voor asymmetrische route ring en veroorzaakt connectiviteits storingen voor uw netwerk.

* IPv4-subnetten:
    * Peering-subnet voor pad 1 (/30)-moet openbaar IP-adres zijn
    * Peering-subnet voor pad 2 (/30)-moet openbaar IP-adres zijn
* IPv6-subnetten (optioneel):
    * Peering-subnet voor pad 1 (/126): dit moet een openbaar IP-adres zijn
    * Peering-subnet voor pad 2 (/126)-moet openbaar IP-adres zijn
* VLAN-ID voor peering
* ASN voor peering
* Aangekondigde voor voegsels-moeten open bare IP-voor voegsels zijn
* Klant-ASN (optioneel als deze verschilt van peering ASN)
* De-of IR-verificatie voor IP-en ASN-validatie
* ExpressRoute ASN = 12076
* MD5-hash (optioneel)

### <a name="6-start-using-the-expressroute-circuit"></a>6. begin met het gebruik van het ExpressRoute-circuit

* U kunt virtuele Azure-netwerken koppelen aan uw ExpressRoute-circuit om connectiviteit van on-premises naar het virtuele Azure-netwerk in te scha kelen. Raadpleeg het artikel [een VNet koppelen aan een circuit](expressroute-howto-linkvnet-arm.md) voor hulp. Deze VNets kunnen zich in hetzelfde Azure-abonnement bevindt als het ExpressRoute-circuit of zich in een ander abonnement bevindt.
* Maak verbinding met Azure-Services en micro soft-Cloud Services via micro soft-peering.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Inrichtings statussen ExpressRoute partner circuit

De volgende sectie bevat een overzicht van de verschillende ExpressRoute-circuit statussen voor het ExpressRoute partner Connectivity-model.
Elk ExpressRoute-partner circuit heeft twee statussen:

* **ServiceProviderProvisioningState** vertegenwoordigt de status van de zijde van de connectiviteits provider. De naam kan *NotProvisioned*, *Provisioning* of *ingericht* zijn. Het ExpressRoute-circuit moet een ingerichte status hebben om peering te kunnen configureren. **Deze status heeft alleen betrekking op ExpressRoute-partner circuits en wordt niet weer gegeven in de eigenschappen van een ExpressRoute direct-circuit**.

* **Status** staat voor de inrichtings status van micro soft. Deze eigenschap wordt ingesteld op ingeschakeld wanneer u een ExpressRoute-circuit maakt

### <a name="possible-states-of-an-expressroute-circuit"></a>Mogelijke statussen van een ExpressRoute-circuit

In deze sectie wordt een overzicht gegeven van de mogelijke statussen van een ExpressRoute-circuit dat is gemaakt onder het verbindings model van de ExpressRoute-partner.

**Tijdstip van aanmaak**

Het ExpressRoute-circuit rapporteert de volgende statussen bij het maken van resources.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Wanneer de connectiviteits provider bezig is met het inrichten van het circuit**

Het ExpressRoute-circuit rapporteert de volgende statussen wanneer de connectiviteits provider bezig is met het inrichten van het circuit.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Wanneer het inrichtings proces is voltooid door de connectiviteits provider**

Het ExpressRoute-circuit rapporteert de volgende statussen zodra de connectiviteits provider het circuit heeft ingericht.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Wanneer de inrichting van de connectiviteits provider het circuit ongedaan maakt**

Als het ExpressRoute-circuit moet worden verwijderd, zal het circuit de volgende statussen rapporteren zodra de service provider het ongedaan maken van de inrichting heeft voltooid.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

U kunt ervoor kiezen om deze indien nodig opnieuw in te scha kelen of Power shell-cmdlets uit te voeren om het circuit te verwijderen.  

> [!IMPORTANT]
> Een circuit kan niet worden verwijderd wanneer de ServiceProviderProvisioningState wordt ingericht of ingericht. De connectiviteits provider moet het circuit ongedaan maken voordat deze kan worden verwijderd. Micro soft blijft het circuit factureren totdat de ExpressRoute-circuit resource in azure wordt verwijderd.
> 

## <a name="routing-session-configuration-state"></a>Configuratie status van routerings sessie

De BGP-inrichtings status rapporten als de BGP-sessie is ingeschakeld op de micro soft Edge. De status moet zijn ingeschakeld om persoonlijke of micro soft-peering te kunnen gebruiken.

Het is belang rijk om de status van de BGP-sessie te controleren met name voor micro soft-peering. Naast de BGP-inrichtings status is er een andere status genaamd *aangekondigde open bare voor voegsels*. De status van de geadverteerde open bare voor voegsels moet in de *geconfigureerde* status zijn, zowel voor de BGP-sessie als voor uw route ring om de eind-tot-eind te kunnen uitvoeren. 

Als de status van de aangekondigde open bare voor voegsel is ingesteld op de status *validatie is vereist* , is de BGP-sessie niet ingeschakeld, omdat de geadverteerde voor voegsels niet overeenkomen met het as-nummer in een van de routerings registers.

> [!IMPORTANT]
> Als de status aangekondigde open bare voor voegsels zich in *hand matige validatie* status bevindt, moet u een ondersteunings ticket openen met [micro soft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en aantonen dat u beschikt over de IP-adressen die zijn geadverteerd samen met het gekoppelde autonome systeem nummer.
> 
> 

## <a name="next-steps"></a>Volgende stappen

* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
