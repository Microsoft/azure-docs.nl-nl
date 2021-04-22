---
title: Problemen met OverconstrainedAllocationRequest oplossen bij het implementeren van een cloudservice (klassiek) in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een OverconstrainedAllocationRequest-uitzondering kunt oplossen bij het implementeren van een cloudservice (klassiek) in Azure.
services: cloud-services
documentationcenter: ''
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1a5880107aaa414da42fe5e36e0cb3315071d8a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877422"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Problemen met OverconstrainedAllocationRequest oplossen bij het implementeren van cloudservices (klassiek) in Azure

In dit artikel gaat u problemen oplossen met beperkte toewijzingsfouten die de implementatie van Azure Cloud Services (klassiek) voorkomen.

Bij het implementeren van instanties in een cloudservice of het toevoegen van nieuwe web- of werkrolinstanties, worden met Microsoft Azure rekenresources toegewezen.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor het Azure-abonnement bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

![Afbeelding van de blade Bewerkingslogboek (klassiek).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Uitzonderingstype  |Foutbericht  |
|---------|---------|
|OverconstrainedAllocationRequest |De VM-grootte (of combinatie van VM-grootten) die voor deze implementatie is vereist, kan niet worden ingericht vanwege beperkingen voor implementatieverzoeken. Vereenig zo mogelijk beperkingen zoals virtuele netwerkbindingen, implementatie naar een gehoste service zonder andere implementatie en naar een andere affiniteitsgroep of zonder affiniteitsgroep, of probeer te implementeren in een andere regio.|

## <a name="cause"></a>Oorzaak

De hoofdoorzaak varieert als de cloudservice is **vastgemaakt** of **niet is vastgemaakt.**

- [**Niet vastgemaakt:** Fouten bij een eerste implementatie van een nieuwe cloudservice (klassiek)](#not-pinned-to-a-cluster)
- [**Vastgemaakt:** Fouten van een bestaande cloudservice (klassiek)](#pinned-to-a-cluster)

> [!NOTE]
> Wanneer het eerste exemplaar wordt geïmplementeerd in een cloudservice (in fasering of productie), wordt die cloudservice vastgemaakt aan een cluster.
>
> Na een periode kunnen de resources in het cluster volledig worden gebruikt. Als een cloudservice (klassiek) een toewijzingsaanvraag indient voor extra resources wanneer er onvoldoende resources beschikbaar zijn in het vastgemaakte cluster, resulteert de aanvraag in een [toewijzingsfout.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Oplossing

Volg de richtlijnen voor toewijzingsfouten in de volgende scenario's.

### <a name="not-pinned-to-a-cluster"></a>Niet vastgemaakt aan een cluster

De eerste keer dat u een cloudservice (klassiek) implementeert, is het cluster nog niet geselecteerd en is de cloudservice dus *niet vastgemaakt.* Azure kan een implementatiefout hebben vanwege:

- U hebt een bepaalde grootte geselecteerd die niet beschikbaar is in de regio.
- De combinatie van grootten die voor verschillende rollen nodig zijn, is niet beschikbaar in de regio.

Wanneer er in dit scenario een toewijzingsfout wordt weergegeven, is het raadzaam om de beschikbare grootten in de regio te controleren en de grootte te wijzigen die u eerder hebt opgegeven.

1. U kunt de grootten controleren die beschikbaar zijn in een regio op de [pagina Cloudservice (klassiek) producten.](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)

    > [!NOTE]
    > Op *de* pagina Producten wordt de beschikbare capaciteit niet weergegeven. Voor elke nieuwe toewijzing moet Azure op dat moment het optimale cluster in uw regio kunnen kiezen.

1. Werk het servicedefinitiebestand voor uw cloudservice (klassiek) bij om een andere [productgrootte dan uw](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) regio op te geven.

### <a name="pinned-to-a-cluster"></a>Vastgemaakt aan een cluster

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
