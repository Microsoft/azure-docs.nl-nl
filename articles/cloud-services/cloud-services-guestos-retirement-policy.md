---
title: Gids voor ondersteunings-en buitengebruikstellings beleid voor Azure-gast besturingssysteem | Microsoft Docs
description: Bevat informatie over wat micro soft ondersteunt ten aanzien van het Azure-gast besturingssysteem dat door Cloud Services wordt gebruikt.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945454"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Ondersteunings beleid voor Azure-gast besturingssystemen
De informatie op deze pagina is gekoppeld aan het Azure-gast besturingssysteem ([gast besturingssysteem](cloud-services-guestos-update-matrix.md)) voor Cloud Services Worker en Web roles (PaaS). Het is niet van toepassing op Virtual Machines (IaaS).

Micro soft heeft een gepubliceerd [ondersteunings beleid voor het gast besturingssysteem](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Op de pagina die u leest, wordt beschreven hoe het beleid wordt geïmplementeerd.

Het beleid is

1. Micro soft biedt ondersteuning voor **ten minste de nieuwste twee families van het gast besturingssysteem**. Wanneer een familie buiten gebruik wordt gesteld, hebben klanten 12 maanden na de officiële pensioen datum een update naar een nieuwere ondersteunde gast besturingssysteem familie.
2. Micro soft ondersteunt **ten minste de nieuwste twee versies van de ondersteunde gast besturingssysteem families**.
3. Micro soft ondersteunt **ten minste de nieuwste twee versies van de Azure SDK**. Wanneer een versie van de SDK buiten gebruik wordt gesteld, hebben klanten 12 maanden na de officiële pensioen datum een update naar een nieuwere versie.

Het kan zijn dat er meer dan twee families of releases worden ondersteund. De officiële ondersteunings informatie voor het gast besturingssysteem wordt weer gegeven in de [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Wanneer een gast besturingssysteem versie buiten gebruik wordt gesteld
Nieuwe **versies** van gast besturingssystemen worden over elke maand geïntroduceerd om de meest recente MSRC-updates op te nemen. Vanwege de reguliere maandelijkse updates wordt een gast besturingssysteem versie normaal gesp roken ongeveer 60 dagen na de release uitgeschakeld. Deze activiteit houdt ten minste twee gast besturingssysteem versies voor elke familie die beschikbaar zijn voor gebruik.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces tijdens een uittreding van een gast besturingssysteem familie
Zodra de buiten gebruiks telling is aangekondigd, hebben klanten een ' overgangs periode van 12 maanden ' voordat de oudere familie officieel uit de service wordt verwijderd. Deze overgangs tijd kan worden uitgebreid naar de keuze van micro soft. Updates worden gepubliceerd in de [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

Een geleidelijk buitengebruikstellings proces begint zes (6) maanden in de overgangs periode. Gedurende deze tijd:

1. Micro soft brengt klanten op de hoogte van de buiten gebruiks telling.
2. De nieuwere versie van de Azure SDK biedt geen ondersteuning voor de buiten gebruik gestelde gast besturingssysteem-familie.
3. Nieuwe implementaties en herimplementaties van Cloud Services zijn niet toegestaan voor de buiten gebruik gestelde familie

Micro soft blijft nieuwe versie van het gast besturingssysteem introduceren met de laatste MSRC-updates tot de laatste dag van de overgangs periode, ook wel de ' verval datum ' genoemd. Cloud Services nog steeds wordt uitgevoerd op de verval datum, wordt niet ondersteund onder de SLA van Azure. Micro soft heeft de keuze om de upgrade af te dwingen, de Services na die datum te verwijderen of te stoppen.

### <a name="process-during-a-guest-os-version-retirement"></a>Verwerken tijdens een uittreding van een gast besturingssysteem versie
Als klanten hun gast besturingssysteem zo instellen dat ze automatisch worden bijgewerkt, hoeven ze zich nooit zorgen te maken over de versies van het gast besturingssysteem. Ze gebruiken altijd de meest recente versie van het gast besturingssysteem.

De versies van het gast besturingssysteem worden elke maand uitgebracht. Vanwege de frequentie van normale releases heeft elke versie een vaste levens duur.

Op 60 dagen in de levens duur is een versie '*uitgeschakeld*'. ' Uitgeschakeld ' betekent dat de versie is verwijderd uit de portal. De versie kan niet meer worden ingesteld vanuit het CSCFG-configuratie bestand. Bestaande implementaties blijven actief. Nieuwe implementaties en code-en configuratie-updates voor bestaande implementaties worden echter niet toegestaan.

Na ' uitgeschakeld ' is de versie van het gast besturingssysteem ' Expires ' en worden alle installaties met de verlopen versie weer gegeven aan beveiligings-en beveiligings problemen. Over het algemeen wordt de verval datum uitgevoerd in batches, zodat de periode van uitschakeling tot verval datum kan verschillen.

Klanten die hun services zo configureren dat ze het gast besturingssysteem hand matig bijwerken, moeten ervoor zorgen dat hun services worden uitgevoerd op een ondersteund gast besturingssysteem. Als een service is geconfigureerd om het gast besturingssysteem automatisch bij te werken, zorgt het onderliggende platform voor compatibiliteit en wordt er een upgrade uitgevoerd naar het meest recente gast besturingssysteem.

Deze peri Oden kunnen langer worden gemaakt door micro soft om de overgangen van klanten te vereenvoudigen. Eventuele wijzigingen worden door gegeven aan de [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Meldingen tijdens het buiten gebruik stellen
* **Gezins buiten gebruik stellen** <br>Micro soft maakt gebruik van blog berichten en Portal meldingen. Klanten die nog steeds een buiten gebruik gestelde gast besturingssysteem familie gebruiken, worden op de hoogte gebracht via directe communicatie (e-mail adres, portal berichten, telefoon gesprek) aan toegewezen service beheerders. Alle wijzigingen worden geplaatst in de [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).
* **Buiten gebruik stellen** <br>Alle wijzigingen en de datums die ze hebben, worden geplaatst in de [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md), inclusief release, disabled en Expires. Services-beheerders ontvangen e-mail berichten als ze implementaties hebben die worden uitgevoerd op een uitgeschakelde gast besturingssysteem versie of-familie. De timing van deze e-mail berichten kan variëren. Over het algemeen zijn ze ten minste een maand vóór de uitschakeling, hoewel deze timing geen officiële SLA is.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**Hoe kan ik de gevolgen van de migratie beperken?**

U wordt aangeraden de meest recente gast besturingssysteem familie te gebruiken voor het ontwerpen van uw Cloud Services.

1. Begin met het plannen van de migratie naar een nieuwere familie.
2. Stel implementaties van tijdelijke tests in om uw Cloud service te testen die wordt uitgevoerd op de nieuwe serie.
3. Stel de versie van het gast besturingssysteem in op **automatisch** (met de extensie = * in het [cscfg](cloud-services-model-and-package.md#cscfg) -bestand), zodat de migratie naar nieuwe gast besturingssysteem versies automatisch plaatsvindt.

**Wat moet ik doen als mijn webtoepassing een diep gaande integratie met het besturings systeem vereist?**

Als de architectuur van uw webtoepassing afhankelijk is van de onderliggende functies van het besturings systeem, gebruikt u ondersteunde mogelijkheden van het platform, zoals [opstart taken](cloud-services-startup-tasks.md) of andere uitbreidings mechanismen. U kunt ook [Azure virtual machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure as a Service) gebruiken, waarbij u verantwoordelijk bent voor het onderhouden van het onderliggende besturings systeem.

## <a name="next-steps"></a>Volgende stappen
Bekijk de nieuwste [versies van het gast besturingssysteem](cloud-services-guestos-update-matrix.md).
