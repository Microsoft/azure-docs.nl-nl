---
title: Overzicht van Azure Resource Health
description: Meer informatie over hoe Azure Resource Health u helpt bij het vaststellen en verkrijgen van ondersteuning voor service problemen die van invloed zijn op uw Azure-resources.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90967869"
---
# <a name="resource-health-overview"></a>Overzicht van Resource Health
 
Azure Resource Health helpt u bij het vaststellen en verkrijgen van ondersteuning voor service problemen die van invloed zijn op uw Azure-resources. Het rapporteert over de huidige en eerdere status van uw resources.

[Azure-status](https://status.azure.com) rapporten over Service problemen die van invloed zijn op een groot aantal Azure-klanten. Resource Health geeft u een aangepast dash board van de status van uw resources. Resource Health toont de tijden dat uw resources niet beschikbaar zijn vanwege Azure-Service problemen. Met deze gegevens kunt u eenvoudig zien of een SLA is geschonden.

## <a name="resource-definition-and-health-assessment"></a>Resource definitie en status evaluatie

Een *resource* is een specifiek exemplaar van een Azure-service, zoals een virtuele machine, Web-app of SQL database. Resource Health is afhankelijk van signalen van verschillende Azure-Services om te beoordelen of een resource in orde is. Als een resource een slechte status heeft, Resource Health analyseert u aanvullende informatie om de oorzaak van het probleem vast te stellen. Er wordt ook gerapporteerd over acties die micro soft onderneemt om het probleem op te lossen en identificeert dingen die u kunt doen om deze te verhelpen.

Zie de lijst met resource typen en status controles op [Azure resource Health](resource-health-checks-resource-types.md)voor meer informatie over de beoordeling van de status.

## <a name="health-status"></a>Algemene status

De status van een resource wordt weer gegeven als een van de volgende statussen.

### <a name="available"></a>Beschikbaar

*Beschikbaar* betekent dat er geen gebeurtenissen zijn gedetecteerd die van invloed zijn op de status van de resource. In gevallen waarin de resource is hersteld tijdens de afgelopen 24 uur van ongeplande downtime, wordt de melding ' onlangs opgelost ' weer geven.

![Status van * beschikbaar * voor een virtuele machine met een melding die onlangs is opgelost](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niet beschikbaar

*Niet beschikbaar* betekent dat de service een voortdurende platform of een niet-platform gebeurtenis heeft gedetecteerd die van invloed is op de status van de resource.

#### <a name="platform-events"></a>Platformgebeurtenissen

Platform gebeurtenissen worden geactiveerd door meerdere onderdelen van de Azure-infra structuur. Ze omvatten zowel geplande acties (bijvoorbeeld gepland onderhoud) als onverwachte incidenten (bijvoorbeeld een niet-geplande host opnieuw opstarten of gedegradeerde host-hardware die wordt voor speld na een opgegeven tijd venster).

Resource Health biedt aanvullende informatie over de gebeurtenis en het herstel proces. U kunt ook contact opnemen met Microsoft Ondersteuning, zelfs als u geen actieve ondersteunings overeenkomst hebt.

![Status van * niet beschikbaar * voor een virtuele machine vanwege een platform gebeurtenis](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Niet-platform gebeurtenissen

Niet-platform gebeurtenissen worden geactiveerd door gebruikers acties. Voor beelden hiervan zijn het stoppen van een virtuele machine of het bereiken van het maximum aantal verbindingen met Azure cache voor redis.

![Status van ' niet beschikbaar ' voor een virtuele machine vanwege een niet-platform gebeurtenis](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Onbekend

*Onbekend* betekent dat resource Health meer dan tien minuten geen informatie over de resource heeft ontvangen. Hoewel deze status geen definitieve indicatie is van de status van de resource, is het een belang rijk gegevens punt voor het oplossen van problemen.

Als de resource op de verwachte manier wordt uitgevoerd, wordt de status van de resource na een paar minuten gewijzigd in *beschikbaar* .

Als u problemen ondervindt met de resource, kan de *onbekende* status ervan betekenen dat een gebeurtenis in het platform van invloed is op de resource.

![Status van * Unknown * voor een virtuele machine](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Verminderd beschikbaar

*Gedegradeerd* betekent dat uw resource een verlies van prestaties heeft gedetecteerd, hoewel het nog steeds beschikbaar is voor gebruik.

Verschillende resources hebben hun eigen criteria voor wanneer ze rapporteren dat ze worden gedegradeerd.

![Status van * gedegradeerd * voor een virtuele machine](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Rapportage van een onjuiste status

Als u denkt dat de huidige status onjuist is, kunt u ons vertellen door de **onjuiste status** van het rapport te selecteren. In gevallen waarin een Azure-probleem van invloed is op u, raden we u aan om contact op te nemen met de ondersteuning van Resource Health.

![Formulier voor het verzenden van informatie over een onjuiste status](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Geschiedenis informatie

U kunt Maxi maal 30 dagen aan geschiedenis bekijken in het gedeelte **status geschiedenis** van resource Health.

![Lijst met Resource Health gebeurtenissen in de afgelopen twee weken](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Aan de slag

Resource Health openen voor een resource:

1. Meld u aan bij Azure Portal.
2. Blader naar de resource.
3. Selecteer in het menu resource in het linkerdeel venster de optie **resource status**.

![Resource Health openen vanuit de resource weergave](./media/resource-health-overview/from-resource-blade.png)

U kunt ook toegang krijgen tot Resource Health door **alle services** te selecteren en de **resource status** te typen in het tekstvak filter. Selecteer [resource status](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)in het deel venster **Help en ondersteuning** .

![Resource Health openen vanuit alle services](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk deze verwijzingen voor meer informatie over Resource Health:
-  [Resource typen en status controles in Azure Resource Health](resource-health-checks-resource-types.md)
-  [Veelgestelde vragen over Azure Resource Health](resource-health-faq.md)
