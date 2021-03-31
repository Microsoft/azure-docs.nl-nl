---
title: Azure Traffic Manager met Azure Site Recovery | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure Traffic Manager gebruikt met Azure Site Recovery voor herstel na nood gevallen en migratie
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134962"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Traffic Manager met Azure Site Recovery

Met Azure Traffic Manager kunt u de distributie van verkeer over de eind punten van uw toepassing beheren. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost.

Traffic Manager gebruikt de Domain Name System (DNS) voor het door sturen van client aanvragen naar het meest geschikte eind punt, op basis van een routerings methode voor verkeer en de status van de eind punten. Traffic Manager biedt een scala aan [routeringsmethoden voor verkeer](../traffic-manager/traffic-manager-routing-methods.md) en [opties voor eindpuntcontrole](../traffic-manager/traffic-manager-monitoring.md) om verschillende toepassingsbehoeften en modellen voor automatische failover mogelijk te kunnen maken. Clients maken rechtstreeks verbinding met het geselecteerde eindpunt. Traffic Manager is geen proxy of gateway en het verkeer wordt niet weer gegeven tussen de client en de service.

In dit artikel wordt beschreven hoe u de intelligente route ring van Azure Traffic Monitor kunt combi neren met de krachtige mogelijkheden voor herstel na nood gevallen en migratie van Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Failover van on-premises naar Azure

Voor het eerste scenario kunt u **bedrijf A** overwegen met alle toepassings infrastructuur in de on-premises omgeving. Voor bedrijfs continuïteit en nalevings redenen besluit **bedrijf A** Azure site Recovery te gebruiken om de toepassingen te beveiligen.

**Bedrijf A** voert toepassingen uit met open bare eind punten en wil de mogelijkheid om verkeer naadloos om te leiden naar Azure in een nood geval. Met de methode voor het routeren van [prioriteits](../traffic-manager/traffic-manager-configure-priority-routing-method.md) verkeer in azure Traffic Manager kan bedrijf A het failovercluster eenvoudig implementeren.

De installatie is als volgt:
- **Bedrijf a** maakt een [Traffic Manager profiel](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Als u de methode voor de **prioriteits** routering gebruikt, maakt **bedrijf A** twee eind punten – **primair** voor on-premises en **failover** voor Azure. **Primaire** is toegewezen aan prioriteit 1 en **failover** krijgt prioriteit 2.
- Omdat het **primaire** eind punt wordt gehost buiten Azure, wordt het eind punt gemaakt als een [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) eind punt.
- Met Azure Site Recovery heeft de Azure-site geen virtuele machines of toepassingen die vóór de failover worden uitgevoerd. Daarom wordt het **failover** -eind punt ook gemaakt als een **extern** eind punt.
- Standaard wordt gebruikers verkeer omgeleid naar de on-premises toepassing, omdat dat eind punt de hoogste prioriteit heeft die eraan is gekoppeld. Er wordt geen verkeer naar Azure geleid als het **primaire** eind punt in orde is.

![On-premises-naar-Azure vóór een failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

In een nood geval kan bedrijf A een [failover](site-recovery-failover.md) naar Azure activeren en de bijbehorende toepassingen op Azure herstellen. Wanneer Azure Traffic Manager detecteert dat het **primaire** eind punt niet meer in orde is, wordt automatisch het **failover** -eind punt gebruikt in het DNS-antwoord en kunnen gebruikers verbinding maken met de toepassing die is hersteld in Azure.

![On-premises-naar-Azure na een failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

Afhankelijk van de bedrijfs vereisten kan **bedrijf a** een hogere of lagere [probing-frequentie](../traffic-manager/traffic-manager-monitoring.md) kiezen om te scha kelen tussen on-premises naar Azure in een nood geval en minimale downtime voor gebruikers te garanderen.

Als de nood situatie zich voordoet, kan **bedrijf A** failback van Azure naar de on-premises omgeving ([VMware](vmware-azure-failback.md) of [Hyper-V](hyper-v-azure-failback.md)) met behulp van Azure site Recovery. Als Traffic Manager detecteert dat het **primaire** eind punt weer in orde is, wordt automatisch het **primaire** eind punt in de DNS-antwoorden gebruikt.

## <a name="on-premises-to-azure-migration"></a>Migratie van on-premises naar Azure

Naast nood herstel kunnen Azure Site Recovery ook [migraties naar Azure](migrate-overview.md). Met behulp van de krachtige functies van de testfailover van Azure Site Recovery kunnen klanten de prestaties van toepassingen op Azure beoordelen zonder dat dit van invloed is op de on-premises omgeving. En wanneer klanten klaar zijn om te migreren, kunnen ze ervoor kiezen om hele werk belastingen samen te migreren of om geleidelijk te migreren en te schalen.

De [gewogen](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) routerings methode van Azure Traffic Manager kan worden gebruikt om een deel van binnenkomend verkeer naar Azure te sturen terwijl de meerderheid wordt doorgestuurd naar de on-premises omgeving. Deze aanpak kan helpen de schaal prestaties te beoordelen, omdat u het gewicht dat aan Azure is toegewezen, kunt verg Roten wanneer u meer en meer van uw workloads naar Azure migreert.

Bijvoorbeeld, **bedrijf B** kiest voor migratie in fasen, waarbij een deel van de toepassings omgeving wordt verplaatst terwijl de rest on-premises blijft. Tijdens de eerste fasen wanneer de meeste omgeving on-premises is, wordt er een groter gewicht toegewezen aan de on-premises omgeving. Traffic Manager retourneert een eind punt op basis van de gewichten die zijn toegewezen aan beschik bare eind punten.

![Migratie van on-premises naar Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Tijdens de migratie worden beide eind punten actief en wordt de meeste verkeer omgeleid naar de on-premises omgeving. Als de migratie wordt uitgevoerd, kan een groter gewicht worden toegewezen aan het eind punt in Azure en tot slot kan het on-premises eind punt worden gedeactiveerd na migratie.

## <a name="azure-to-azure-failover"></a>Failover van Azure naar Azure

Voor dit voor beeld moet u **bedrijf C** hebben met alle toepassings infrastructuren waarop Azure wordt uitgevoerd. Voor bedrijfs continuïteit en nalevings redenen besluit **Company C** Azure site Recovery te gebruiken om de toepassingen te beveiligen.

**Bedrijf C** voert toepassingen uit met open bare eind punten en wil de mogelijkheid om verkeer naadloos om te leiden naar een andere Azure-regio in een nood geval. Met de methode voor de route ring met [prioriteit](../traffic-manager/traffic-manager-configure-priority-routing-method.md) kan **bedrijf C** dit failover-patroon eenvoudig implementeren.

De installatie is als volgt:
- **Bedrijf C** maakt een [Traffic Manager profiel](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Als u de methode voor de **prioriteits** routering gebruikt, maakt **bedrijf C** twee eind punten – **primair** voor de bron regio (Azure Azië-Oost) en de **failover** voor de herstel regio (Azure Zuidoost-Azië). **Primaire** is toegewezen aan prioriteit 1 en **failover** krijgt prioriteit 2.
- Omdat het **primaire** eind punt wordt gehost in azure, kan het eind punt een [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) -eind punt zijn.
- Met Azure Site Recovery heeft de Recovery Azure-site geen virtuele machines of toepassingen die worden uitgevoerd voorafgaand aan de failover. Het **failover** -eind punt kan dus worden gemaakt als een [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) eind punt.
- Standaard wordt gebruikers verkeer omgeleid naar de bron regio-toepassing (Azië-oost), aangezien dat eind punt de hoogste prioriteit heeft die eraan is gekoppeld. Er wordt geen verkeer omgeleid naar de herstel regio als het **primaire** eind punt in orde is.

![Azure-naar-Azure vóór een failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

In een nood geval kan **bedrijfs C** een [failover](azure-to-azure-tutorial-failover-failback.md) activeren en de bijbehorende toepassingen herstellen in de Azure-regio voor herstel. Wanneer Azure Traffic Manager detecteert dat het primaire eind punt niet meer in orde is, wordt automatisch het **failover** -eind punt gebruikt in het DNS-antwoord en kunnen gebruikers verbinding maken met de toepassing die is hersteld in de Azure-regio voor herstel (Zuidoost-Azië).

![Azure-naar-Azure na een failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

Afhankelijk van de bedrijfs vereisten kan **bedrijf C** een hogere of lagere [probing-frequentie](../traffic-manager/traffic-manager-monitoring.md) kiezen om te scha kelen tussen de bron-en herstel regio's en minimale downtime voor gebruikers te garanderen.

Als de nood situatie zich voordoet, kan **bedrijf C** failback uitvoeren van de Azure-regio voor herstel naar de Azure-bron regio met behulp van Azure site Recovery. Als Traffic Manager detecteert dat het **primaire** eind punt weer in orde is, wordt automatisch het **primaire** eind punt in de DNS-antwoorden gebruikt.

## <a name="protecting-multi-region-enterprise-applications"></a>Zakelijke toepassingen met meerdere regio's beveiligen

Wereld wijde ondernemingen verbeteren de ervaring van klanten vaak door hun toepassingen aan te passen aan regionale behoeften. Lokalisatie en latentie reductie kunnen leiden tot de infra structuur van toepassingen die in verschillende regio's kan worden gesplitst. Ondernemingen zijn ook gebonden aan regionale gegevens wetten in bepaalde gebieden en kiezen voor het isoleren van een deel van hun toepassings infrastructuur binnen regionale grenzen.  

Laten we eens kijken naar een voor beeld waarbij **bedrijf D** de eind punten van de toepassing heeft gesplitst zodat deze afzonderlijk Duitsland en de rest van de wereld kunnen behandelen. **Bedrijf D** maakt gebruik van de [geografische](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) routerings methode van Azure Traffic Manager om dit in te stellen. Verkeer dat afkomstig is van Duitsland, wordt omgeleid naar **eind punt 1** en alle verkeer van buiten Duitsland wordt omgeleid naar **eind punt 2**.

Het probleem met deze installatie is dat als **eind punt 1** om de een of andere reden niet meer werkt, het verkeer naar **eind punt 2** niet kan worden omgeleid. Verkeer dat afkomstig is van Duitsland, blijft gericht op **eind punt 1** , ongeacht de status van het eind punt, waardoor Duitse gebruikers zonder toegang tot de toepassing van het **bedrijf D**. Op dezelfde manier wordt het verkeer naar **eind punt 1** niet omgeleid als **eind punt 2** offline gaat.

![Toepassing met meerdere regio's voordat](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Om te voor komen dat dit probleem wordt opgelost en de toepassings tolerantie wordt gegarandeerd, gebruikt **bedrijf D** [geneste Traffic Manager profielen](../traffic-manager/traffic-manager-nested-profiles.md) met Azure site Recovery. In de installatie van een genest profiel wordt verkeer niet naar afzonderlijke eind punten geleid, maar in plaats daarvan naar andere Traffic Manager profielen. Deze instelling werkt als volgt:
- In plaats van geografische route ring met afzonderlijke eind punten te gebruiken, gebruikt **bedrijf D** geografische route ring met Traffic Manager profielen.
- Elk onderliggend Traffic Manager profiel gebruikt **prioriteits** routering met een primair en een herstel eindpunt, en maakt daarom **prioriteits** routering binnen **geografische** route ring.
- Als u toepassings tolerantie wilt inschakelen, gebruikt elke werkbelasting distributie Azure Site Recovery om een failover uit te voeren naar een herstel regio, op basis van een nood geval.
- Wanneer de bovenliggende Traffic Manager een DNS-query ontvangt, wordt deze omgeleid naar de relevante onderliggende Traffic Manager die reageert op de query met een beschik bare eind punt.

![Toepassing met meerdere regio's na](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Als bijvoorbeeld het eind punt in Duitsland-centraal mislukt, kan de toepassing snel worden hersteld naar Duitsland-noordoost. Het nieuwe eind punt verwerkt verkeer dat afkomstig is van Duitsland met minimale downtime voor gebruikers. Een eind punt uitval in Europa-west kan worden verwerkt door de werk belasting van de toepassing te herstellen naar Europa-noord, met Azure Traffic Manager DNS-omleidingen naar het beschik bare eind punt te verwerken.

De bovenstaande configuratie kan worden uitgebreid om te omvatten dat er veel regio's en eindpunt combinaties vereist zijn. Traffic Manager Maxi maal 10 niveaus van geneste profielen toestaat en geen lussen toestaat binnen de geneste configuratie.

## <a name="recovery-time-objective-rto-considerations"></a>Aandachtspunten voor herstel tijd (RTO)

In de meeste organisaties worden het toevoegen of wijzigen van DNS-records verwerkt door een afzonderlijk team of door iemand buiten de organisatie. Dit maakt de taak van het wijzigen van DNS-records zeer lastig. De tijd die nodig is om DNS-records bij te werken door andere teams of organisaties die de DNS-infra structuur beheren, verschilt per organisatie en heeft betrekking op de RTO van de toepassing.

Door Traffic Manager te gebruiken, kunt u het werk frontload dat vereist is voor DNS-updates. Er is geen hand matige of script actie vereist op het moment van de werkelijke failover. Deze benadering helpt bij snelle switching (en daarom het verlagen van RTO), en voor het voor komen van de kost bare DNS-wijzigings fouten in een nood geval. Met Traffic Manager, zelfs de stap voor failback is geautomatiseerd, wat anders zou moeten worden beheerd.

Als u het juiste interval voor het [nabingen](../traffic-manager/traffic-manager-monitoring.md) instelt met behulp van Basic of snelle interval status controles, kunnen de RTO tijdens de failover aanzienlijk worden vertraagd en de downtime voor gebruikers worden verminderd.

U kunt ook de waarde voor de DNS-Time to Live (TTL) voor het Traffic Manager profiel optimaliseren. TTL is de waarde waarvoor een DNS-vermelding door een client in de cache wordt opgeslagen. Voor een record wordt DNS niet twee maal in de levens duur van de TTL opgevraagd. Aan elke DNS-record is een TTL gekoppeld. Het verlagen van deze waarde resulteert in meer DNS-query's voor Traffic Manager, maar kan RTO verminderen door storingen sneller op te sporen.

De TTL van de client neemt ook niet toe als het aantal DNS-resolvers tussen de client en de gezaghebbende DNS-server toeneemt. DNS resolvers ' count ' de TTL en alleen door geven op een TTL-waarde die aangeeft na hoeveel tijd de record is opgeslagen in de cache. Dit zorgt ervoor dat de DNS-record na de TTL wordt vernieuwd op de client, onafhankelijk van het aantal DNS-resolvers in de keten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Traffic Manager [routerings methoden](../traffic-manager/traffic-manager-routing-methods.md).
- Meer informatie over [geneste Traffic Manager profielen](../traffic-manager/traffic-manager-nested-profiles.md).
- Meer informatie over [eindpunt bewaking](../traffic-manager/traffic-manager-monitoring.md).
- Meer informatie over [herstel plannen](site-recovery-create-recovery-plans.md) voor het automatiseren van de failover van de toepassing.
