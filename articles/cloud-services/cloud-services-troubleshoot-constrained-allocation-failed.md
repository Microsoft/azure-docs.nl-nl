---
title: Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een Cloud service (klassiek) in azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een ConstrainedAllocationFailed-uitzonde ring oplost bij het implementeren van een Cloud service (klassiek) naar Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738277"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een Cloud service (klassiek) naar Azure

In dit artikel lost u de toewijzings fouten op waarbij Azure Cloud Services (klassiek) niet kan worden geïmplementeerd vanwege toewijzings beperkingen.

Bij het implementeren van exemplaren in een Cloud service (klassiek) of het toevoegen van nieuwe web-of worker-rolinstanties, Microsoft Azure het toewijzen van reken resources.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor Azure-abonnementen bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Ga in Azure Portal naar uw Cloud service (klassiek) en selecteer in de zijbalk het *bewerkings logboek (klassiek)* om de logboeken weer te geven.

![Afbeelding toont de Blade bewerkings logboek (klassiek).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw Cloud service (klassiek) inspecteert, ziet u de volgende uitzonde ring:

|Uitzonderings type  |Foutbericht  |
|---------|---------|
|ConstrainedAllocationFailed     |De Azure-bewerking `{Operation ID}` is mislukt met de code compute. ConstrainedAllocationFailed. Details: toewijzing is mislukt; kan niet voldoen aan de beperkingen in de aanvraag. De aangevraagde nieuwe service-implementatie is gebonden aan een affiniteitsgroep, is gericht op een virtueel netwerk of er is een bestaande implementatie onder deze gehoste service. Met elk van deze voor waarden wordt de nieuwe implementatie beperkt tot specifieke Azure-resources. Probeer het later opnieuw of verklein de VM-grootte of het aantal rolinstanties. Indien mogelijk kunt u ook de hierboven genoemde beperkingen opheffen of naar een andere regio implementeren.|

## <a name="cause"></a>Oorzaak

Wanneer de eerste instantie wordt geïmplementeerd in een Cloud service (in staging of productie), wordt die Cloud service vastgemaakt aan een cluster.

Na verloop van tijd kunnen de bronnen in dit cluster volledig worden gebruikt. Als een Cloud service (klassiek) een toewijzings aanvraag voor meer resources maakt wanneer er onvoldoende bronnen beschikbaar zijn in het vastgemaakte cluster, resulteert de aanvraag in een toewijzings fout. Zie de [algemene problemen toewijzings fout](cloud-services-allocation-failures.md#common-issues)voor meer informatie.

## <a name="solution"></a>Oplossing

Bestaande Cloud Services zijn *vastgemaakt* aan een cluster. Eventuele verdere implementaties voor de Cloud service (klassiek) worden uitgevoerd in hetzelfde cluster.

Wanneer u een toewijzings fout in dit scenario ondervindt, is het raadzaam om opnieuw te implementeren naar een nieuwe Cloud service (klassiek) (en de *CNAME* bij te werken).

> [!TIP]
> Deze oplossing is waarschijnlijk het meest succesvol omdat het platform een keuze kan maken uit alle clusters in die regio.

> [!NOTE]
> Deze oplossing moet een downtime van nul tot gevolg hebben.

1. Implementeer de werk belasting op een nieuwe Cloud service (klassiek).
    - Zie de hand leiding voor het [maken en implementeren van een Cloud service (klassiek)](cloud-services-how-to-create-deploy-portal.md) voor verdere instructies.

    > [!WARNING]
    > Als u het IP-adres dat is gekoppeld aan deze implementatie site niet wilt verliezen, kunt u [het IP-adres van de oplossing 3](cloud-services-allocation-failures.md#solutions)gebruiken.

1. Werk de *CNAME* -of *A* -record bij om verkeer naar de nieuwe Cloud service (klassiek) te verwijzen.
    - Zie de hand leiding [een aangepaste domein naam configureren voor een klassieke Azure-Cloud service (Engelstalig)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) voor verdere instructies.

1. Wanneer het verkeer naar de oude site wordt verzonden, kunt u de oude Cloud service (klassiek) verwijderen.
    - Zie de hand leiding [implementaties en Cloud service verwijderen (klassiek)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) voor verdere instructies.
    - Zie de [bewaking Inleiding tot Cloud service (klassiek)](cloud-services-how-to-monitor.md)om het netwerk verkeer in uw Cloud service (klassiek) te bekijken.

Zie [problemen met de toewijzing van Cloud Services (klassiek) oplossen | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) voor verdere herstel stappen.

## <a name="next-steps"></a>Volgende stappen

Voor meer toewijzings fout oplossingen en achtergrond informatie:

> [!div class="nextstepaction"]
> [Toewijzings fouten-Cloud service (klassiek)](cloud-services-allocation-failures.md)

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een Azure-ondersteuningsaanvraag indienen. Als u een ondersteuningsaanvraag wilt indienen, selecteert u op de pagina [Azure-ondersteuning](https://azure.microsoft.com/support/options/) *Ondersteuning krijgen*.