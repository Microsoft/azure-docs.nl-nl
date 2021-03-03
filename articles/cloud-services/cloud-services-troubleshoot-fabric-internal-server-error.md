---
title: Problemen met FabricInternalServerError of ServiceAllocationFailure oplossen bij het implementeren van een Cloud service (klassiek) naar Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een FabricInternalServerError-of ServiceAllocationFailure-uitzonde ring oplost bij het implementeren van een Cloud service (klassiek) naar Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745727"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met FabricInternalServerError of ServiceAllocationFailure oplossen bij het implementeren van een Cloud service (klassiek) naar Azure

In dit artikel lost u de toewijzings fouten op waarbij de infrastructuur controller niet kan worden toegewezen tijdens de implementatie van een Azure-Cloud service (klassiek).

Bij het implementeren van exemplaren in een Cloud service of het toevoegen van nieuwe web-of worker-rolinstanties, Microsoft Azure het toewijzen van reken resources.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor Azure-abonnementen bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Ga in Azure Portal naar uw Cloud service (klassiek) en selecteer in de zijbalk het *bewerkings logboek (klassiek)* om de logboeken weer te geven.

![Afbeelding toont de Blade bewerkings logboek (klassiek).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw Cloud service (klassiek) inspecteert, ziet u de volgende uitzonde ring:

|Uitzondering  |Foutbericht  |
|---------|---------|
FabricInternalServerError     |De bewerking is mislukt met fout code ' InternalError ' en errorMessage ' er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.|
|ServiceAllocationFailure     |De bewerking is mislukt met fout code ' InternalError ' en errorMessage ' er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.|

## <a name="cause"></a>Oorzaak

*FabricInternalServerError* en *ServiceAllocationFailure* zijn uitzonde ringen die kunnen optreden wanneer de infrastructuur controller geen instanties in het cluster kan toewijzen. De hoofd oorzaak is afhankelijk van het **vastgemaakte** of **niet-vastgemaakte** Cloud service.

- [**Niet vastgemaakt:** Fouten van een eerste implementatie van een nieuwe Cloud service](#not-pinned-to-a-cluster)
- [**Vastgemaakt:** Fouten van bestaande Cloud Services](#pinned-to-a-cluster)

> [!NOTE]
> Wanneer de eerste instantie wordt geïmplementeerd in een Cloud service (in staging of productie), wordt die Cloud service vastgemaakt aan een cluster.
>
> Na verloop van tijd kunnen de resources in deze resource groep volledig worden gebruikt. Als een Cloud service een toewijzings aanvraag voor aanvullende resources maakt wanneer er onvoldoende resources beschikbaar zijn in de vastgemaakte resource groep, leidt de aanvraag tot een [toewijzings fout](cloud-services-allocation-failures.md).

## <a name="solution"></a>Oplossing

Volg de richt lijnen voor toewijzings fouten in de volgende scenario's.

### <a name="not-pinned-to-a-cluster"></a>Niet vastgemaakt aan een cluster

De eerste keer dat u een Cloud service (klassiek) implementeert, is het cluster nog niet geselecteerd, dus is de Cloud service niet *vastgemaakt*. Azure kan een implementatie fout veroorzaken omdat:

- U hebt een bepaalde grootte geselecteerd die niet beschikbaar is in de regio.
- De combi natie van groottes die nodig zijn voor verschillende rollen is niet beschikbaar in de regio.

Wanneer u een toewijzings fout in dit scenario ondervindt, is het raadzaam om de beschik bare grootten in de regio te controleren en de grootte te wijzigen die u eerder hebt opgegeven.

1. U kunt de beschik bare grootten controleren in een regio op de pagina met de [Cloud service (klassiek)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > Op de pagina *producten* wordt de beschik bare capaciteit niet weer gegeven. Voor elke nieuwe toewijzing moet Azure het optimale cluster in uw regio op dat moment kunnen kiezen.

1. Werk het service definitie bestand voor uw Cloud service (klassiek) bij om een andere [product grootte](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) op te geven in uw regio.

### <a name="pinned-to-a-cluster"></a>Vastgemaakt aan een cluster

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
