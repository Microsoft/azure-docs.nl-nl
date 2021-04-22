---
title: Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een cloudservice (klassiek) in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een ConstrainedAllocationFailed-uitzondering kunt oplossen bij het implementeren van een cloudservice (klassiek) in Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876594"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een cloudservice (klassiek) in Azure

In dit artikel gaat u toewijzingsfouten oplossen waarbij Azure Cloud-services (klassiek) niet kunnen worden geïmplementeerd vanwege toewijzingsbeperkingen.

Wanneer u exemplaren implementeert in een cloudservice (klassiek) of nieuwe web- of werkrol-exemplaren toevoegt, Microsoft Azure rekenbronnen toegewezen.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor het Azure-abonnement bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Navigeer Azure Portal naar uw cloudservice (klassiek) en selecteer bewerkingslogboek *(klassiek)* in de zijbalk om de logboeken te bekijken.

![Afbeelding van de blade Bewerkingslogboek (klassiek).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw cloudservice (klassiek) inspecteert, ziet u de volgende uitzondering:

|Uitzonderingstype  |Foutbericht  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure-bewerking `{Operation ID}` ' ' is mislukt met code Compute.ConstrainedAllocationFailed. Details: Toewijzing is mislukt; kan niet voldoen aan de beperkingen in de aanvraag. De aangevraagde nieuwe service-implementatie is gebonden aan een affiniteitsgroep, is gericht op een virtueel netwerk of er is een bestaande implementatie onder deze gehoste service. Elk van deze voorwaarden beperkt de nieuwe implementatie tot specifieke Azure-resources. Probeer het later opnieuw of probeer de VM-grootte of het aantal rol-exemplaren te verkleinen. Indien mogelijk kunt u ook de hierboven genoemde beperkingen opheffen of naar een andere regio implementeren.|

## <a name="cause"></a>Oorzaak

Wanneer het eerste exemplaar wordt geïmplementeerd in een cloudservice (in fasering of productie), wordt die cloudservice vastgemaakt aan een cluster.

Na een periode kunnen de resources in dit cluster volledig worden gebruikt. Als een cloudservice (klassiek) een toewijzingsaanvraag indient voor meer resources wanneer er onvoldoende resources beschikbaar zijn in het vastgemaakte cluster, resulteert de aanvraag in een toewijzingsfout. Zie veelvoorkomende problemen met [toewijzingsfout voor meer informatie.](cloud-services-allocation-failures.md#common-issues)

## <a name="solution"></a>Oplossing

Bestaande cloudservices worden *vastgemaakt* aan een cluster. Eventuele verdere implementaties voor de cloudservice (klassiek) worden in hetzelfde cluster plaatsvinden.

Wanneer er een toewijzingsfout in dit scenario wordt weergegeven, is de aanbevolen aanpak om opnieuw te wordenployn naar een nieuwe cloudservice (klassiek) (en de CNAME bij *te werken).*

> [!TIP]
> Deze oplossing is waarschijnlijk het meest succesvol omdat het platform een keuze kan maken uit alle clusters in die regio.

> [!NOTE]
> Voor deze oplossing is geen downtime nodig.

1. Implementeer de workload in een nieuwe cloudservice (klassiek).
    - Zie de handleiding Een cloudservice maken en implementeren [(klassiek)](cloud-services-how-to-create-deploy-portal.md) voor verdere instructies.

    > [!WARNING]
    > Als u het IP-adres dat is gekoppeld aan deze implementatiesleuf niet wilt verliezen, kunt u Oplossing [3 gebruiken: het IP-adres behouden.](cloud-services-allocation-failures.md#solutions)

1. Werk de *CNAME-* of *A-record* bij om verkeer naar de nieuwe cloudservice (klassiek) te laten wijzen.
    - Zie de [handleiding Configuring a custom domain name for an Azure Cloud service (classic) (Een](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) aangepaste domeinnaam configureren voor een Azure Cloud-service (klassiek) voor verdere instructies.

1. Zodra er geen verkeer naar de oude site gaat, kunt u de oude cloudservice (klassiek) verwijderen.
    - Zie de [handleiding Implementaties verwijderen en een cloudservice (klassiek)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) voor verdere instructies.
    - Zie Introduction to Cloud service (classic) monitoring (Inleiding tot de bewaking van cloudservices (klassiek) als u het netwerkverkeer in uw cloudservice [(klassiek) wilt zien.](cloud-services-how-to-monitor.md)

Zie [Problemen met toewijzingsfouten in de cloudservice (klassiek) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) voor verdere herstelstappen.

## <a name="next-steps"></a>Volgende stappen

Voor meer oplossingen voor toewijzingsfout en achtergrondinformatie:

> [!div class="nextstepaction"]
> [Toewijzingsfouten - Cloudservice (klassiek)](cloud-services-allocation-failures.md)

Als uw Azure-probleem niet wordt opgelost in dit artikel, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een Azure-ondersteuningsaanvraag indienen. Als u een ondersteuningsaanvraag wilt indienen, selecteert u op de pagina [Azure-ondersteuning](https://azure.microsoft.com/support/options/) *Ondersteuning krijgen*.