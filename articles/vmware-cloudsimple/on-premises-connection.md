---
title: Azure VMware-oplossing op CloudSimple-on-premises-verbinding met behulp van ExpressRoute
description: Hierin wordt beschreven hoe u een on-premises verbinding aanvraagt via ExpressRoute vanuit CloudSimple Region Network
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899232"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute

Als u al een Azure ExpressRoute-verbinding hebt vanaf een externe locatie (zoals on-premises) naar Azure, kunt u deze verbinding maken met uw CloudSimple-omgeving. U kunt dit doen via een Azure-functie waarmee twee ExpressRoute-circuits met elkaar kunnen worden verbonden. Met deze methode wordt een veilige, persoonlijke, hoge band breedte en lage latentie verbinding tot stand gebracht tussen de twee omgevingen.

[![On-premises ExpressRoute-verbinding-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Voordat u begint

Een **/29-** netwerk adres blok is vereist voor het tot stand brengen van Global Reach verbinding van on-premises.  De/29-adres ruimte wordt gebruikt voor het tussenliggende netwerk tussen ExpressRoute-circuits.  Het tussenliggende netwerk mag niet overlappen met een van uw virtuele Azure-netwerken, on-premises netwerken of CloudSimple-particuliere Cloud netwerken.

## <a name="prerequisites"></a>Vereisten

* Er is een Azure ExpressRoute-circuit vereist voordat u verbinding kunt maken tussen het circuit en de particuliere Cloud netwerken van CloudSimple.
* Een gebruiker is verplicht met bevoegdheden om autorisatie sleutels te maken op een ExpressRoute-circuit.

## <a name="scenarios"></a>Scenario's

Door uw on-premises netwerk te verbinden met uw particuliere cloud netwerk, kunt u de Privécloud op verschillende manieren gebruiken, met inbegrip van de volgende scenario's:

* Toegang tot uw particuliere cloud netwerk zonder een site-naar-site-VPN-verbinding te maken.
* Gebruik uw on-premises Active Directory als een id-bron in uw Privécloud.
* Migreer virtuele machines die on-premises worden uitgevoerd naar uw Privécloud.
* Gebruik uw Privécloud als onderdeel van een oplossing voor herstel na nood gevallen.
* Gebruik on-premises resources op uw virtuele machines uit de werk belasting van uw privécloud.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-circuits verbinden

Om de ExpressRoute-verbinding tot stand te brengen, moet u een autorisatie maken op uw ExpressRoute-circuit en de autorisatie gegevens opgeven voor CloudSimple.


### <a name="create-expressroute-authorization"></a>ExpressRoute-autorisatie maken

1. Meld u aan bij Azure Portal.

2. Zoek in de bovenste zoek balk naar **ExpressRoute-circuit** en klik op **ExpressRoute-circuits** onder **Services**.
    [![ExpressRoute-circuits](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecteer het ExpressRoute-circuit dat u van plan bent om verbinding te maken met uw CloudSimple-netwerk.

4. Op de pagina ExpressRoute klikt u op **autorisaties**, voert u een naam in voor de autorisatie en klikt u op **Opslaan**.
    [![ExpressRoute-circuit autorisatie](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopieer de resource-ID en autorisatie sleutel door te klikken op het pictogram kopiëren. Plak de ID en de sleutel in een tekst bestand.
    [![Kopie van ExpressRoute-circuit autorisatie](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > De **resource-id** moet worden gekopieerd uit de gebruikers interface en moet de indeling hebben ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` Als u deze wilt ondersteunen.

6. Een ticket met <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuning</a> voor het maken van de verbinding.
    * Probleem type: **technisch**
    * Abonnement: het **abonnement waar de CloudSimple-service is geïmplementeerd**
    * Service: **VMware-oplossing per CloudSimple**
    * Probleem type: **service aanvraag**
    * Subtype van probleem: **ExpressRoute-verbinding maken met on-premises**
    * Geef de bron-ID en de autorisatie sleutel op die u hebt gekopieerd en opgeslagen in het detail venster.
    * Geef een/29-netwerk adres ruimte op voor het tussenliggende netwerk.
    * Verzendt u de standaard route via ExpressRoute?
    * Moet het verkeer van de Privécloud gebruikmaken van de standaard route die via ExpressRoute wordt verzonden?

    > [!IMPORTANT]
    > Als u een standaard route verzendt, kunt u al het Internet verkeer vanuit een Privécloud verzenden met behulp van uw on-premises Internet verbinding.  Als u de standaard route die in de Privécloud is geconfigureerd wilt uitschakelen en de standaard route voor on-premises verbindingen wilt gebruiken, geeft u de details op in het ondersteunings ticket.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure-netwerk verbindingen](cloudsimple-azure-network-connection.md)  
