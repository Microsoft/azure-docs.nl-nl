---
title: Zorg voor bedrijfs continuïteit & herstel na nood geval met behulp van gekoppelde Azure-regio's
description: Toepassings tolerantie garanderen met Azure regionale koppeling
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.custom: references_regions
ms.openlocfilehash: d42eabe3afeb738b0cbb011881678839fe0ba2d7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95539053"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Bedrijfscontinuïteit en herstel na noodgeval (BCDR): gekoppelde Azure-regio's

## <a name="what-are-paired-regions"></a>Wat zijn gekoppelde regio's?

Een Azure-regio bestaat uit een set data centers die zijn geïmplementeerd binnen een latentie definitie en verbonden zijn via een toegewezen netwerk met lage latentie.  Dit zorgt ervoor dat Azure-Services binnen een Azure-regio de best mogelijke prestaties en beveiliging bieden.  

Een geografie van Azure definieert een deel van de wereld waarin zich ten minste één Azure-regio bevindt. Met geografi wordt een aparte markt gedefinieerd, meestal met twee of meer regio's, waarmee gegevens locatie en nalevings grenzen behouden blijven.  Meer informatie over de globale infra structuur van Azure vindt u [hier](https://azure.microsoft.com/global-infrastructure/regions/)

Een regionaal paar bestaat uit twee regio's binnen dezelfde geografie. Azure serializt platform updates (gepland onderhoud) over regionale paren, zodat er slechts één regio per keer in elk paar wordt bijgewerkt. Als er sprake is van een storing in meerdere regio's, wordt voor het herstel ten minste één regio in elk paar een prioriteit gegeven.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Sommige Azure-Services hebben meer voor deel van gekoppelde regio's om de bedrijfs continuïteit te waarborgen en om te voor komen dat gegevens verloren gaan.  Azure biedt verschillende [opslag oplossingen](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) die gebruikmaken van gekoppelde regio's om de beschik baarheid van gegevens te garanderen. Zo repliceren [Azure geo-redundante opslag](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) automatisch gegevens naar een secundaire regio, zodat de gegevens duurzaam zijn, zelfs in het geval dat de primaire regio niet kan worden hersteld. 

Houd er rekening mee dat niet alle Azure-Services gegevens automatisch repliceren, en dat alle Azure-Services automatisch van een defecte regio naar het bijbehorende paar vallen.  In dergelijke gevallen moet het herstel en de replicatie worden geconfigureerd door de klant.

## <a name="can-i-select-my-regional-pairs"></a>Kan ik mijn regionale paren selecteren?

Nee. Sommige Azure-Services zijn afhankelijk van regionale paren, zoals de [redundante opslag](./storage/common/storage-redundancy.md)van Azure. Met deze services kunt u geen nieuwe regionale koppeling maken.  En omdat Azure gepland onderhoud en prioriteiten voor herstel van regionale paren beheert, kunt u uw eigen regionale paren niet definiëren om te profiteren van deze services. U kunt echter uw eigen oplossing voor herstel na nood gevallen maken door services in een wille keurig aantal regio's te bouwen en Azure-Services te gebruiken om ze te koppelen. 

U kunt bijvoorbeeld Azure-Services, zoals [AzCopy](./storage/common/storage-use-azcopy-v10.md) , gebruiken voor het plannen van back-ups van gegevens naar een opslag account in een andere regio.  Met behulp van [Azure DNS en Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)kunnen klanten een robuuste architectuur ontwerpen voor hun toepassingen die het verlies van de primaire regio in de hand hebben.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Ben ik beperkt tot het gebruik van services binnen mijn regionale paren?

Nee. Hoewel een bepaalde Azure-service afhankelijk is van een regionale combi natie, kunt u uw andere services hosten in elke regio die voldoet aan uw bedrijfs behoeften.  Een Azure GRS-opslag oplossing kan gegevens in Canada-centraal koppelen aan een peer in Canada-oost terwijl u reken resources gebruikt die zich in VS-Oost bevinden.  

## <a name="must-i-use-azure-regional-pairs"></a>Moet ik regionale paren van Azure gebruiken?

Nee. Klanten kunnen Azure-Services gebruiken voor het ontwikkelen van een flexibele service zonder dat dit afhankelijk is van de regionale paren van Azure.  We raden u echter aan om bedrijfs continuïteit nood herstel (BCDR) over regionale paren te configureren om te profiteren van [isolatie](./security/fundamentals/isolation-choices.md) en de [Beschik baarheid](./availability-zones/az-overview.md)te verbeteren. We adviseren voor toepassingen die meerdere actieve regio’s ondersteunen om, waar mogelijk, beide regio’s in een regiopaar te gebruiken. Dit zorgt voor optimale Beschik baarheid van toepassingen en een geminimaliseerde herstel tijd bij een nood geval. Als dat mogelijk is, kunt u uw toepassing ontwerpen voor een [maximale tolerantie](/azure/architecture/framework/resiliency/overview) en een gemakkelijke [nood herstel](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Azure-regionale paren

| Geografie | Regionaal paar A | Regionaal paar B  |
|:--- |:--- |:--- |
| Asia-Pacific |Azië-oost (Hongkong) | Zuidoost-Azië (Singapore) |
| Australië |Australië - oost |Australië - zuidoost |
| Australië |Australië - centraal |Australië - centraal 2 |
| Brazilië |Brazil South |VS - zuid-centraal |
| Canada |Canada - midden |Canada - oost |
| China |China - noord |China East|
| China |China - noord 2 |China - oost 2|
| Europa |Europa - noord (Ierland) |Europa - west (Nederland) |
| Frankrijk |Frankrijk - centraal|Frankrijk - zuid|
| Duitsland |Duitsland - centraal |Duitsland - noordoost |
| India |India - centraal |India - zuid |
| India |India - west |India - zuid |
| Japan |Japan - oost |Japan - west |
| Korea |Korea - centraal |Korea - zuid |
| Noord-Amerika |VS - oost |VS - west |
| Noord-Amerika |VS - oost 2 |Central US |
| Noord-Amerika |VS - noord-centraal |VS - zuid-centraal |
| Noord-Amerika |VS - west 2 |VS - west-centraal |
| Noorwegen | Noorwegen - oost | Noorwegen - west |
| Zuid-Afrika | Zuid-Afrika - noord |Zuid-Afrika - west |
| Zwitserland | Zwitserland - noord |Zwitserland - west |
| VK |Verenigd Koninkrijk West |Verenigd Koninkrijk Zuid |
| Verenigde Arabische Emiraten | VAE - noord | UAE - centraal
| US Department of Defense |US DoD East |US DoD Central |
| Amerikaanse overheid |VS (overheid) - Arizona |VS (overheid) - Texas |
| Amerikaanse overheid |US Gov - Iowa |VS (overheid) - Virginia |
| Amerikaanse overheid |VS (overheid) - Virginia |VS (overheid) - Texas |

> [!Important]
> - West-India is in één richting gekoppeld. De secundaire regio van West-India is India-zuid, maar India-zuid secundaire regio is centraal India.
> - Brazilië-zuid is uniek omdat het is gekoppeld aan een regio buiten de geografie. De secundaire regio van Brazilië-zuid is Zuid-Centraal vs. De secundaire regio van Zuid-Centraal (VS) is niet Brazilië-zuid.


## <a name="an-example-of-paired-regions"></a>Een voorbeeld van gekoppelde regio’s
In de onderstaande afbeelding ziet u een hypothetische toepassing die gebruikmaakt van het regionale paar voor nood herstel. De groene aantallen markeren de activiteiten in meerdere regio's van drie Azure-Services (Azure compute, Storage en data base) en hoe ze zijn geconfigureerd voor replicatie in verschillende regio's. De unieke voor delen van de implementatie in gekoppelde regio's worden gemarkeerd met de Oranje cijfers.

![Overzicht van de voor delen van gekoppelde regio's](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Afbeelding 2: hypothetisch Azure regionaal paar

## <a name="cross-region-activities"></a>Activiteiten tussen regio's
Zoals bedoeld in afbeelding 2.

1. **Azure Compute (IaaS)** : u moet extra reken resources vooraf inrichten om ervoor te zorgen dat resources beschikbaar zijn in een andere regio tijdens een nood geval. Zie voor meer informatie [Azure tolerante technische richt lijnen](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** : als u beheerde schijven gebruikt, kunt u meer informatie vinden over [Cross-Region-back-ups](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) met Azure backup en [virtuele machines](./site-recovery/azure-to-azure-tutorial-enable-replication.md) van de ene naar de andere regio repliceren met Azure site Recovery. Als u opslag accounts gebruikt, wordt de geo-redundante opslag (GRS) standaard geconfigureerd wanneer een Azure Storage-account wordt gemaakt. Met GRS worden uw gegevens automatisch drie keer binnen de primaire regio gerepliceerd, en drie keer in de gekoppelde regio. Zie [Azure Storage redundantie opties](storage/common/storage-redundancy.md)voor meer informatie.

3. **Azure SQL database** – met Azure SQL database geo-replicatie kunt u asynchrone replicatie van trans acties naar elke regio in de wereld configureren. We raden u echter aan deze bronnen in een gekoppelde regio te implementeren voor de meeste scenario's voor herstel na nood gevallen. Zie [geo-replicatie in Azure SQL database](./azure-sql/database/auto-failover-group-overview.md)voor meer informatie.

4. **Azure Resource Manager**: Resource Manager biedt inherent logische isolatie van componenten binnen regio’s. Dit betekent dat logische fouten in één regio minder waarschijnlijk van invloed zijn op de andere.

## <a name="benefits-of-paired-regions"></a>Voor delen van gekoppelde regio's

5. **Fysieke isolatie** : als dat mogelijk is, geeft Azure de voor keur aan een schei ding van ten minste 300 mijl tussen data centers in een regionale combi natie, hoewel dit niet praktisch of mogelijk is in alle geografische gebieden. Fysieke datacenter schei ding vermindert de kans op natuur rampen, burgerlijke, stroom storingen of fysieke netwerk storingen die beide regio's tegelijkertijd beïnvloeden. Voor isolatie gelden de beperkingen binnen de Geografie (geografische grootte, Beschik baarheid van de infra structuur, voeding, netwerk infrastructuur, enz.).  

6. Door het **platform geleverde replicatie** : sommige services, zoals Geo-Redundant opslag, bieden automatische replicatie naar de gekoppelde regio.

7. **Volg orde van regio herstel** : in het geval van een grote storing wordt het herstellen van één regio voor elk paar voor rang gegeven. Toepassingen die binnen gekoppelde regio’s worden geïmplementeerd hebben de garantie dat een van de regio’s met prioriteit wordt hersteld. Als een toepassing wordt geïmplementeerd in meerdere niet-gekoppelde regio's, kan het herstel worden vertraagd: in het ergste geval kunnen de gekozen regio's de laatste twee zijn om te worden hersteld.

8. **Sequentiële updates** : geplande Azure-systeem updates worden sequentieel (niet op hetzelfde moment) op gekoppelde regio's geïmplementeerd om de downtime te minimaliseren, het effect van fouten en logische fouten in zeldzame gevallen van een ongeldige update.

9. **Data locatie** : een regio bevindt zich in dezelfde geografie als het bijbehorende paar (met uitzonde ring van Brazilië-Zuid) om te voldoen aan de vereisten van gegevens locatie voor de jurisdictie van belasting en rechts afdwinging.