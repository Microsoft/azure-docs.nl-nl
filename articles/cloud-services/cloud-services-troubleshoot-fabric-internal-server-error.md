---
title: Problemen met FabricInternalServerError of ServiceAllocationFailure oplossen bij het implementeren van een cloudservice (klassiek) in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een uitzondering FabricInternalServerError of ServiceAllocationFailure kunt oplossen bij het implementeren van een cloudservice (klassiek) in Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0883178779179df2e531123b8a500c62d42530e4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877440"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met FabricInternalServerError of ServiceAllocationFailure oplossen bij het implementeren van een cloudservice (klassiek) in Azure

In dit artikel gaat u toewijzingsfouten oplossen waarbij de fabriccontroller geen toewijzing kan uitvoeren bij het implementeren van een Azure Cloud-service (klassiek).

Bij het implementeren van instanties in een cloudservice of het toevoegen van nieuwe web- of werkrolinstanties, worden met Microsoft Azure rekenresources toegewezen.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor het Azure-abonnement bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Navigeer Azure Portal cloudservice (klassiek) en selecteer in de zijbalk Bewerkingslogboek *(klassiek) om* de logboeken te bekijken.

![Afbeelding van de blade Bewerkingslogboek (klassiek).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw cloudservice (klassiek) inspecteert, ziet u de volgende uitzondering:

|Uitzondering  |Foutbericht  |
|---------|---------|
FabricInternalServerError     |Bewerking is mislukt met foutcode 'InternalError' en errorMessage ' De server heeft een interne fout aangetroffen. Voer de aanvraag opnieuw uit.'.|
|ServiceAllocationFailure     |Bewerking is mislukt met foutcode 'InternalError' en errorMessage ' De server heeft een interne fout aangetroffen. Voer de aanvraag opnieuw uit.'.|

## <a name="cause"></a>Oorzaak

*FabricInternalServerError* en *ServiceAllocationFailure* zijn uitzonderingen die kunnen optreden wanneer de fabriccontroller geen exemplaren in het cluster kan toewijzen. De hoofdoorzaak varieert als de cloudservice is **vastgemaakt** of **niet is vastgemaakt.**

- [**Niet vastgemaakt:** Fouten bij een eerste implementatie van een nieuwe cloudservice](#not-pinned-to-a-cluster)
- [**Vastgemaakt:** Fouten van bestaande cloudservices](#pinned-to-a-cluster)

> [!NOTE]
> Wanneer het eerste exemplaar wordt geïmplementeerd in een cloudservice (in fasering of productie), wordt die cloudservice vastgemaakt aan een cluster.
>
> Na een periode kunnen de resources in deze resourcegroep volledig worden gebruikt. Als een cloudservice een toewijzingsaanvraag indient voor extra resources wanneer er onvoldoende resources beschikbaar zijn in de vastgemaakte resourcegroep, resulteert de aanvraag in een [toewijzingsfout.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Oplossing

Volg de richtlijnen voor toewijzingsfouten in de volgende scenario's.

### <a name="not-pinned-to-a-cluster"></a>Niet vastgemaakt aan een cluster

De eerste keer dat u een cloudservice (klassiek) implementeert, is het cluster nog niet geselecteerd en is de cloudservice dus *niet vastgemaakt.* Azure kan een implementatiefout hebben vanwege:

- U hebt een bepaalde grootte geselecteerd die niet beschikbaar is in de regio.
- De combinatie van grootten die nodig zijn voor verschillende rollen is niet beschikbaar in de regio.

Wanneer er in dit scenario een toewijzingsfout wordt weergegeven, is het raadzaam om de beschikbare grootten in de regio te controleren en de grootte te wijzigen die u eerder hebt opgegeven.

1. U kunt de grootten controleren die beschikbaar zijn in een regio op de [pagina Cloudservice (klassiek) producten.](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)

    > [!NOTE]
    > Op *de* pagina Producten wordt de beschikbare capaciteit niet weergegeven. Voor elke nieuwe toewijzing moet Azure op dat moment het optimale cluster in uw regio kunnen kiezen.

1. Werk het servicedefinitiebestand voor uw cloudservice (klassiek) bij om een andere [productgrootte dan](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) uw regio op te geven.

### <a name="pinned-to-a-cluster"></a>Vastgemaakt aan een cluster

Bestaande cloudservices worden *vastgemaakt* aan een cluster. Eventuele verdere implementaties voor de cloudservice (klassiek) worden in hetzelfde cluster plaatsvinden.

Wanneer er een toewijzingsfout in dit scenario wordt weergegeven, is de aanbevolen aanpak om opnieuw te wordenploy naar een nieuwe cloudservice (klassiek) (en de CNAME bij *te werken).*

> [!TIP]
> Deze oplossing is waarschijnlijk het meest succesvol omdat het platform een keuze kan maken uit alle clusters in die regio.

> [!NOTE]
> Deze oplossing zou geen downtime moeten hebben.

1. Implementeer de workload in een nieuwe cloudservice (klassiek).
    - Zie de [handleiding Een cloudservice (klassiek)](cloud-services-how-to-create-deploy-portal.md) maken en implementeren voor verdere instructies.

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
