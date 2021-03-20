---
title: Het afhandelen van een onderbreking van de Azure-service die gevolgen heeft voor Azure Cloud Services (klassiek)
description: Meer informatie over wat u moet doen in het geval van een onderbreking van de Azure-service die gevolgen heeft voor Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742162"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Wat te doen in het geval van een onderbreking van de Azure-service die invloed heeft op de Azure-Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Bij micro soft werken we hard om ervoor te zorgen dat onze services altijd beschikbaar zijn wanneer u ze nodig hebt. Forceren meer dan ons besturings element is soms van invloed op de manier waarop ongeplande service onderbrekingen ontstaan.

Micro soft biedt een Service Level Agreement (SLA) voor zijn services als een toezeg ging voor de uptime en connectiviteit. De SLA voor afzonderlijke Azure-Services vindt u op [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure biedt al veel ingebouwde platform functies die ondersteuning bieden voor Maxi maal beschik bare toepassingen. Lees [nood herstel en hoge Beschik baarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)voor meer informatie over deze services.

In dit artikel wordt een scenario voor herstel na nood gevallen beschreven, wanneer een hele regio een storing veroorzaakt door een grote natuur ramp of een uitgebreide service onderbreking. Dit zijn zeldzame gevallen, maar u moet er wel voor bereid zijn dat er een storing optreedt in een hele regio. Als een hele regio een onderbreking van de service ondervindt, zijn de lokaal redundante kopieën van uw gegevens tijdelijk niet beschikbaar. Als u geo-replicatie hebt ingeschakeld, worden er drie extra kopieën van uw Azure Storage-blobs en-tabellen in een andere regio opgeslagen. In het geval van een volledige regionale onderbreking of nood gevallen waarin de primaire regio niet kan worden hersteld, wijst Azure alle DNS-vermeldingen toe aan de regio met geo-replicatie.

> [!NOTE]
> Houd er rekening mee dat u geen controle hebt over dit proces en dat dit alleen geldt voor onderbrekingen van de service voor het hele Data Center. Daarom moet u ook vertrouwen op andere toepassingsspecifieke back-upstrategieen om het hoogste niveau van Beschik baarheid te krijgen. Zie [herstel na nood gevallen en hoge Beschik baarheid voor toepassingen die zijn gebouwd op Microsoft Azure](/azure/architecture/framework/resiliency/backup-and-recovery)voor meer informatie. Als u van invloed wilt zijn op uw eigen failover, kunt u het gebruik van [geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-redundancy.md)overwegen om een alleen-lezen kopie van uw gegevens in een andere regio te maken.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Optie 1: een back-upimplementatie gebruiken via Azure Traffic Manager
De meest robuuste oplossing voor herstel na nood gevallen omvat het onderhoud van meerdere implementaties van uw toepassing in verschillende regio's, waarna [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) wordt gebruikt om het verkeer tussen de toepassingen te omleiden. Azure Traffic Manager biedt meerdere [routerings methoden](../traffic-manager/traffic-manager-routing-methods.md), zodat u kunt kiezen of u uw implementaties wilt beheren met behulp van een primair/back-upmodel of dat u het verkeer ertussen wilt splitsen.

![Azure Cloud Services verdelen over regio's met Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Voor de snelste reactie op het verlies van een regio is het belang rijk dat u de [eindpunt bewaking](../traffic-manager/traffic-manager-monitoring.md)van Traffic Manager configureert.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Optie 2: de toepassing implementeren in een nieuwe regio
Het onderhoud van meerdere actieve implementaties zoals beschreven in de vorige optie brengt extra lopende kosten met zich mee. Als uw beoogde herstel tijd (RTO) flexibel genoeg is en u de oorspronkelijke code of het gecompileerde Cloud Services-pakket hebt, kunt u een nieuw exemplaar van uw toepassing in een andere regio maken en uw DNS-records bijwerken zodat deze naar de nieuwe implementatie verwijzen.

Zie [een Cloud service maken en implementeren](cloud-services-how-to-create-deploy-portal.md)voor meer informatie over het maken en implementeren van een Cloud service toepassing.

Afhankelijk van de gegevens bronnen van uw toepassing, moet u mogelijk de herstel procedures voor de gegevens bron van uw toepassing controleren.

* Zie [Azure Storage redundantie](../storage/common/storage-redundancy.md) voor het controleren van de beschik bare opties op basis van het gekozen redundantie model voor uw toepassing voor Azure Storage gegevens bronnen.
* Lees overzicht voor SQL Database bronnen [: bedrijfs continuïteit in de Cloud en herstel na een Data Base met SQL database](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) om de opties te controleren die beschikbaar zijn op basis van het gekozen replicatie model voor uw toepassing.


## <a name="option-3-wait-for-recovery"></a>Optie 3: wachten op herstel
In dit geval is er geen actie voor uw onderdeel vereist, maar uw service is niet beschikbaar totdat de regio is hersteld. U kunt de huidige status van de service zien op het [Azure service Health dash board](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Volgende stappen
Zie [herstel na nood gevallen en hoge Beschik baarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)voor meer informatie over het implementeren van een strategie voor herstel na nood gevallen en hoge Beschik baarheid.

Zie [technische richt lijnen voor Azure](/azure/architecture/checklist/resiliency-per-service)voor meer informatie over het ontwikkelen van een gedetailleerd technisch inzicht in de mogelijkheden van een Cloud platform.