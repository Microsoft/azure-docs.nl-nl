---
title: Problemen met toewijzing van Cloud service (klassiek) oplossen | Microsoft Docs
description: Los een toewijzings fout op wanneer u Azure Cloud Services implementeert. Meer informatie over hoe de toewijzing werkt en waarom de toewijzing kan mislukken.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738307"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Toewijzings fouten oplossen wanneer u Cloud Services (klassiek) in azure implementeert

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.


## <a name="summary"></a>Samenvatting

Bij het implementeren van exemplaren in een Cloud service of het toevoegen van nieuwe web-of worker-rolinstanties, Microsoft Azure het toewijzen van reken resources. U kunt af en toe fouten ontvangen tijdens het uitvoeren van deze bewerkingen, zelfs voordat u de limieten voor Azure-abonnementen bereikt. In dit artikel worden de oorzaken beschreven van enkele algemene toewijzings fouten en suggesties voor mogelijke herstel bewerkingen. De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Achtergrond: hoe de toewijzing werkt

De servers in Azure-datacenters worden in clusters gepartitioneerd. Er is een nieuwe aanvraag voor Cloud Service toewijzing in meerdere clusters geprobeerd. Wanneer de eerste instantie wordt geïmplementeerd in een Cloud service (in staging of productie), wordt die Cloud service vastgemaakt aan een cluster. Eventuele verdere implementaties voor de Cloud service worden uitgevoerd in hetzelfde cluster. In dit artikel wordt dit als ' vastgemaakt aan een cluster ' genoemd. Diagram 1 hieronder illustreert het geval van een normale toewijzing die wordt geprobeerd in meerdere clusters; Diagram 2 illustreert het geval van een toewijzing die is vastgemaakt aan cluster 2 omdat de bestaande Cloud service CS_1 wordt gehost.

![Toewijzings diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Waarom toewijzings fout optreedt

Wanneer een toewijzings aanvraag is vastgemaakt aan een cluster, is er een hogere kans op het vinden van vrije bronnen omdat de beschik bare resource groep beperkt is tot een cluster. Als uw toewijzings aanvraag is vastgemaakt aan een cluster maar het type van de aangevraagde resource niet wordt ondersteund door dat cluster, mislukt de aanvraag zelfs als het cluster een gratis bron heeft. Diagram 3 hieronder illustreert het geval waarin een vastgemaakte toewijzing mislukt omdat het enige kandidaat-cluster geen vrije resources heeft. Diagram 4 illustreert het geval dat een vastgemaakte toewijzing mislukt omdat het enige kandidaat-cluster de aangevraagde VM-grootte niet ondersteunt, zelfs als het cluster vrije bronnen heeft.

![Fout bij vastgemaakte toewijzing](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Toewijzings fouten voor Cloud Services oplossen

### <a name="error-message"></a>Foutbericht

Ga in Azure Portal naar uw Cloud service en selecteer in de zijbalk *bewerkings Logboeken (klassiek)* om de logboeken weer te geven.

Zie verdere oplossingen voor de onderstaande uitzonde ringen:

|Uitzonderings type  |Foutbericht  |Oplossing  |
|---------|---------|---------|
|FabricInternalServerError     |De bewerking is mislukt met fout code ' InternalError ' en errorMessage ' er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.|[Problemen met FabricInternalServerError oplossen](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |De bewerking is mislukt met fout code ' InternalError ' en errorMessage ' er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.|[Problemen met ServiceAllocationFailure oplossen](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |De bewerking `{Operation ID}` is mislukt: de aangevraagde VM-laag is momenteel niet beschikbaar in de regio ( `{Region ID}` ) voor dit abonnement. Probeer een andere laag of implementeer deze op een andere locatie.|[Problemen met LocationNotFoundForRoleSize oplossen](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |De Azure-bewerking `{Operation ID}` is mislukt met de code compute. ConstrainedAllocationFailed. Details: toewijzing is mislukt; kan niet voldoen aan de beperkingen in de aanvraag. De aangevraagde nieuwe service-implementatie is gebonden aan een affiniteitsgroep, is gericht op een virtueel netwerk of er is een bestaande implementatie onder deze gehoste service. Met elk van deze voor waarden wordt de nieuwe implementatie beperkt tot specifieke Azure-resources. Probeer het later opnieuw of verklein de VM-grootte of het aantal rolinstanties. Indien mogelijk kunt u ook de hierboven genoemde beperkingen opheffen of naar een andere regio implementeren.|[Problemen met ConstrainedAllocationFailed oplossen](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |De VM-grootte (of combi natie van VM-grootten) die voor deze implementatie vereist is, kan niet worden ingericht vanwege beperkingen van de implementatie aanvraag. Probeer, indien mogelijk, afwijkende beperkingen, zoals virtuele netwerk bindingen, te implementeren naar een gehoste service zonder andere implementatie hierin en naar een andere affiniteits groep of zonder affiniteits groep, of probeer te implementeren naar een andere regio.|[Problemen met OverconstrainedAllocationRequest oplossen](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Voor beeld van fout bericht:

> De bewerking {Operation-id} van Azure is mislukt met de code compute. ConstrainedAllocationFailed. Details: toewijzing is mislukt; kan niet voldoen aan de beperkingen in de aanvraag. De aangevraagde nieuwe service-implementatie is gebonden aan een affiniteitsgroep, is gericht op een virtueel netwerk of er is een bestaande implementatie onder deze gehoste service. Met elk van deze voor waarden wordt de nieuwe implementatie beperkt tot specifieke Azure-resources. Probeer het later opnieuw of verklein de VM-grootte of het aantal rolinstanties. Indien mogelijk verwijdert u de bovengenoemde beperkingen of probeert u te implementeren in een andere regio. "

### <a name="common-issues"></a>Veelvoorkomende problemen

Hier volgen de algemene toewijzings scenario's die ervoor zorgen dat een toewijzings aanvraag wordt vastgemaakt aan één cluster.

* Implementeren naar Faserings sleuf: als een Cloud service in een van de sleuven een implementatie heeft, wordt de volledige Cloud service vastgemaakt aan een specifiek cluster.  Dit betekent dat als een implementatie al in de productiesite bestaat, dan kan een nieuwe faseringsimplementatie alleen worden toegewezen in hetzelfde cluster als de productiesite. Als de capaciteit van het cluster bijna is bereikt, kan de aanvraag mislukken.
* Schalen: wanneer nieuwe exemplaren aan een bestaande cloudservice worden toegevoegd, moet de toewijzing in hetzelfde cluster gebeuren.  Kleine schaalaanvragen kunnen doorgaans worden toegewezen, maar niet altijd. Als de capaciteit van het cluster bijna is bereikt, kan de aanvraag mislukken.
* Affiniteits groep: een nieuwe implementatie naar een lege Cloud service kan worden toegewezen door de infra structuur in een cluster in die regio, tenzij de Cloud service is vastgemaakt aan een affiniteits groep. Implementaties voor dezelfde affiniteits groep worden geprobeerd op hetzelfde cluster. Als de capaciteit van het cluster bijna is bereikt, kan de aanvraag mislukken.
* Het vNet-oudere virtuele netwerken van de affiniteits groep zijn gekoppeld aan affiniteits groepen in plaats van regio's, en Cloud Services in deze virtuele netwerken worden vastgemaakt aan het affiniteits groeps cluster. Implementaties van dit type virtuele netwerk worden geprobeerd op het vastgemaakte cluster. Als de capaciteit van het cluster bijna is bereikt, kan de aanvraag mislukken.

## <a name="solutions"></a>Oplossingen

1. Opnieuw implementeren naar een nieuwe Cloud service: deze oplossing is waarschijnlijk het meest succesvol omdat het platform kan kiezen uit alle clusters in die regio.

   * De werk belasting implementeren in een nieuwe Cloud service  
   * De CNAME-of A-record bijwerken om verkeer naar de nieuwe Cloud service te verwijzen
   * Als er geen verkeer naar de oude site wordt verzonden, kunt u de oude Cloud service verwijderen. Deze oplossing moet een downtime van nul tot gevolg hebben.
2. Productie-en staging-sleuven verwijderen: met deze oplossing blijft uw bestaande DNS-naam behouden, maar wordt uw toepassing downtime.

   * Verwijder de productie-en staging-sleuven van een bestaande Cloud service, zodat de Cloud service leeg is en
   * Maak een nieuwe implementatie in de bestaande Cloud service. Hiermee wordt opnieuw geprobeerd de toewijzing uit te voeren voor alle clusters in de regio. Zorg ervoor dat de Cloud service niet is gekoppeld aan een affiniteits groep.
3. Gereserveerd IP: deze oplossing behoudt uw bestaande IP-adres, maar veroorzaakt uitval tijd voor uw toepassing.  

   * Een ReservedIP maken voor uw bestaande implementatie met behulp van Power shell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Volg de bovenstaande #2 en zorg ervoor dat u de nieuwe ReservedIP opgeeft in het CSCFG-service.
4. Affiniteits groep voor nieuwe implementaties verwijderen-Affiniteits groepen worden niet meer aanbevolen. Volg de stappen voor 1 hierboven om een nieuwe cloudservice te implementeren. Zorg ervoor dat de Cloud service zich niet in een affiniteits groep bevindt.
5. Converteren naar een regionaal Virtual Network-Zie [How to migrate from Affinity groups to a regional Virtual Network (VNet) (Engelstalig)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
