---
title: Oplossingen voor nood herstel ontwerpen
description: Meer informatie over het ontwerpen van uw Cloud oplossing voor herstel na nood gevallen door het juiste failover-patroon te kiezen.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91357779"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Strategieën voor herstel na nood gevallen voor toepassingen die gebruikmaken van Azure SQL Database elastische Pools
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database biedt verschillende mogelijkheden om de bedrijfs continuïteit van uw toepassing te bieden wanneer er onherstelbare incidenten optreden. [Elastische Pools](elastic-pool-overview.md) en individuele data bases ondersteunen dezelfde soort nood herstel (Dr)-mogelijkheden. In dit artikel worden verschillende DR-strategieën beschreven voor elastische Pools die gebruikmaken van deze Azure SQL Database bedrijfs continuïteits functies.

In dit artikel wordt het volgende canonieke SaaS ISV-toepassings patroon gebruikt:

Een moderne cloud-gebaseerde webtoepassing voorziet in één Data Base voor elke eind gebruiker. De ISV heeft veel klanten en maakt daarom gebruik van veel data bases, ook wel Tenant-data bases genoemd. Omdat de Tenant-data bases doorgaans onvoorspelbare activiteiten patronen hebben, gebruikt de ISV een elastische pool om de data base gedurende langere tijd te kunnen gebruiken. De elastische pool vereenvoudigt ook het prestatie beheer wanneer de gebruikers activiteit piekt. Naast de Tenant databases gebruikt de toepassing ook verschillende data bases voor het beheren van gebruikers profielen, beveiliging, het verzamelen van gebruiks patronen, enzovoort. Beschik baarheid van de afzonderlijke tenants heeft geen invloed op de beschik baarheid van de toepassing als geheel. De beschik baarheid en prestaties van beheer databases zijn echter essentieel voor de functie van de toepassing en als de beheer databases offline zijn, is de volledige toepassing offline.

In dit artikel worden de DR-strategieën besproken die betrekking hebben op verschillende scenario's, van toepassingen met een lagere Beschik baarheid.

> [!NOTE]
> Als u Premium-of Bedrijfskritiek-data bases en elastische Pools gebruikt, kunt u ze voor het maken van regionale storingen gebruiken door ze te converteren naar zone redundante implementatie configuratie. Zie [zone-redundante data bases](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kosten gevoelig voor opstarten

Ik ben een bedrijf voor opstarten en kan zeer kosten gevoelig zijn.  Ik wil de implementatie en het beheer van de toepassing vereenvoudigen en ik kan een beperkte SLA voor individuele klanten hebben. Maar ik wil er zeker van zijn dat de toepassing als geheel nooit offline is.

Om te voldoen aan de eenvoud vereiste, implementeert u alle Tenant databases in één elastische groep in de Azure-regio van uw keuze en implementeert u beheer databases als geo-gerepliceerde single data bases. Gebruik geo-Restore voor nood herstel van tenants. Dit is gratis. Als u de beschik baarheid van de beheer databases wilt garanderen, repliceert u ze naar een andere regio met behulp van een groep voor automatische failover (stap 1). De voortdurende kosten van de configuratie voor herstel na nood gevallen in dit scenario zijn gelijk aan de totale kosten van de secundaire data bases. Deze configuratie wordt geïllustreerd in het volgende diagram.

![Afbeelding 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Als er een storing optreedt in de primaire regio, worden de stappen voor het herstellen van uw toepassing online gedemonstreerd in het volgende diagram.

* De failover-groep initieert automatische failover van de beheer database naar de DR-regio. De toepassing wordt automatisch opnieuw verbonden met de nieuwe primaire en alle nieuwe accounts en Tenant-data bases worden gemaakt in de DR-regio. De bestaande klanten zien hun gegevens tijdelijk niet beschikbaar.
* Maak de elastische pool met dezelfde configuratie als de oorspronkelijke groep (2).
* Gebruik geo-Restore voor het maken van kopieën van de Tenant-data bases (3). U kunt overwegen de afzonderlijke herstel bewerkingen te activeren door de eind gebruiker of door een ander toepassings-specifiek prioriteits schema te gebruiken.

Uw toepassing is op dit moment weer online in de DR-regio, maar sommige klanten hebben vertraging bij het openen van hun gegevens.

![Afbeelding 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Als de storing tijdelijk was, is het mogelijk dat de primaire regio door Azure wordt hersteld voordat alle data base-herstel bewerkingen zijn voltooid in de DR-regio. In dit geval kunt u de toepassing verplaatsen naar de primaire regio. Het proces voert de stappen uit die in het volgende diagram worden geïllustreerd.

* Alle openstaande geo-herstel aanvragen annuleren.
* Failover van de beheer databases naar de primaire regio (5). Na het herstel van de regio worden de oude Primaries automatisch als secundaire zones. Nu wisselen ze rollen opnieuw uit.
* Wijzig de connection string van de toepassing zodat deze terug naar de primaire regio wijst. Nu worden alle nieuwe accounts en Tenant databases in de primaire regio gemaakt. Sommige bestaande klanten zien hun gegevens tijdelijk niet beschikbaar.
* Stel alle data bases in de DR-groep in op alleen-lezen om ervoor te zorgen dat ze niet kunnen worden gewijzigd in de DR-regio (6).
* Voor elke data base in de DR-groep die sinds het herstel is gewijzigd, wijzigt u de naam of verwijdert u de bijbehorende data bases in de primaire groep (7).
* Kopieer de bijgewerkte data bases van de DR-groep naar de primaire groep (8).
* De DR-groep verwijderen (9)

Uw toepassing is op dit moment online in de primaire regio met alle Tenant databases die beschikbaar zijn in de primaire groep.

![Afbeelding 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Voordeel

Het belangrijkste voor deel van deze strategie is een lage continue prijs voor de redundantie van gegevenslaag. Azure SQL Database maakt automatisch back-ups van data bases zonder extra kosten voor het herschrijven van toepassingen. De kosten worden alleen gefactureerd wanneer de elastische data bases worden hersteld. 

### <a name="trade-off"></a>Afhandelen

De afweging is dat het volledige herstel van alle Tenant databases veel tijd in beslag neemt. De tijds duur is afhankelijk van het totale aantal herstel bewerkingen dat u initieert in de DR-regio en de totale grootte van de Tenant-data bases. Zelfs als u de herstel bewerkingen van sommige tenants met een andere prioriteit wilt geven, bent u concurrerend met alle andere herstel bewerkingen die in dezelfde regio worden gestart als de service arbitrates en beperkt u tot een minimale impact op de data bases van de bestaande klanten. Daarnaast kan het herstellen van de Tenant databases pas worden gestart als de nieuwe elastische groep in de DR-regio is gemaakt.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Rijpe toepassing met een gelaagd gelaagde service

Ik ben een rijpe SaaS-toepassing met aanbiedingen voor gelaagde Services en verschillende Sla's voor klanten met een proef versie en voor het betalen van klanten. Voor klanten met een proef versie moeten de kosten zoveel mogelijk worden verminderd. Klanten met een proef versie kunnen downtime duren, maar ik wil de kans verkleinen. Voor de betalende klanten is uitval tijd een vlucht risico. Ik wil er dus zeker van zijn dat betaal klanten altijd toegang hebben tot hun gegevens.

Ter ondersteuning van dit scenario scheidt u de proef tenants van betaalde tenants door ze in afzonderlijke elastische Pools te plaatsen. De proef klanten hebben een lagere eDTU-of vCores per Tenant en een lagere SLA met een langere herstel tijd. De betalende klanten bevinden zich in een pool met meer eDTU-of vCores per Tenant en een hogere SLA. Om de laagste herstel tijd te garanderen, worden de Tenant databases van de betalende klanten geo-repliceerbaar. Deze configuratie wordt geïllustreerd in het volgende diagram.

![In het diagram ziet u een primaire regio en een D R-regio waarbij geo-replicatie tussen de beheer database en de primaire groep van betaalde klanten en de secundaire pool zonder replicatie voor de groep proef klanten wordt gemaakt.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Net als in het eerste scenario zijn de beheer databases vrij actief, zodat u één geo-gerepliceerde Data Base kunt gebruiken (1). Dit zorgt voor de voorspel bare prestaties voor nieuwe klant abonnementen, profiel updates en andere beheer bewerkingen. De regio waarin de Primaries van de beheer databases zich bevinden, is de primaire regio en de regio waarin de secundaire data bases zich bevinden in de DR-regio.

De Tenant databases van de betalende klanten hebben actieve data bases in de groep ' betaald ' die is ingericht in de primaire regio. Richt een secundaire groep met dezelfde naam in de DR-regio in. Elke Tenant wordt geo-gerepliceerd naar de secundaire groep (2). Hierdoor kunnen alle Tenant databases snel worden hersteld met behulp van failover.

Als er een storing optreedt in de primaire regio, worden de stappen voor het herstellen van uw toepassing online gedemonstreerd in het volgende diagram:

![Diagram toont een storing voor de primaire regio, met een failover naar de beheer database, de secundaire pool van de betaalde klant en het maken en herstellen van proef klanten.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Direct failover van de beheer databases naar de DR-regio (3).
* Wijzig de connection string van de toepassing zodat deze verwijst naar de DR-regio. Nu worden alle nieuwe accounts en Tenant databases gemaakt in de DR-regio. De bestaande proef klanten zien hun gegevens tijdelijk niet beschikbaar.
* Failover van de data bases van de betaalde Tenant naar de groep in de DR-regio om hun Beschik baarheid onmiddellijk te herstellen (4). Omdat de failover een snelle wijzigingen in het meta gegevens niveau is, kunt u een optimalisatie overwegen waarbij de afzonderlijke failovers op aanvraag worden geactiveerd door de eind gebruikers verbindingen.
* Als de waarde voor de eDTU-grootte of de vCore van de secundaire groep lager is dan de primaire, omdat de secundaire data bases alleen de capaciteit nodig hebben om de wijzigings logboeken te verwerken tijdens het uitvoeren van de zones, verg Roten de capaciteit van de groep nu direct voor de volledige werk belasting van alle tenants (5).
* Maak de nieuwe elastische pool met dezelfde naam en dezelfde configuratie in de DR-regio voor de data bases van de proef klanten (6).
* Zodra de groep met de klanten van de proef versie is gemaakt, gebruikt u geo-Restore om de afzonderlijke Tenant-data bases van de proef versie terug te zetten in de nieuwe groep (7). U kunt de afzonderlijke herstel bewerkingen voor eind gebruikers activeren of een ander prioriteiten schema voor toepassingen gebruiken.

Uw toepassing is op dit moment weer online in de DR-regio. Alle betalende klanten hebben toegang tot hun gegevens terwijl de proef klanten een vertraging ondervinden bij het openen van hun gegevens.

Wanneer de primaire regio door Azure wordt hersteld *nadat* u de toepassing in de Dr-regio hebt hersteld, kunt u de toepassing in die regio blijven uitvoeren of u kunt kiezen om een failback uit te voeren naar de primaire regio. Als de primaire regio wordt hersteld *voordat* het failoverproces is voltooid, kunt u het beste meteen een failback uitvoeren. De failback neemt de stappen in het volgende diagram:

![Diagram toont de failback-stappen die moeten worden geïmplementeerd na het herstellen van de primaire regio.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Alle openstaande geo-herstel aanvragen annuleren.
* Failover van de beheer databases (8). Na het herstel van de regio wordt de oude primaire primair automatisch de secundaire. Nu wordt het de primaire keer weer.  
* Failover van de betaalde Tenant databases (9). Op dezelfde manier wordt na het herstel van de regio automatisch de secundaire Primaries geworden. Ze worden nu het Primaries.
* Stel de herstelde proef databases in die in de DR-regio zijn gewijzigd in alleen-lezen (10).
* Voor elke data base in de DR-groep met proef gebruikers die sinds het herstel is gewijzigd, wijzigt u de naam of verwijdert u de overeenkomende data base in de primaire groep proef klanten (11).
* Kopieer de bijgewerkte data bases van de DR-groep naar de primaire groep (12).
* De DR-groep verwijderen (13).

> [!NOTE]
> De failoverbewerking is asynchroon. Als u de herstel tijd wilt minimaliseren, is het belang rijk dat u de failover-opdracht van de Tenant-data bases uitvoert in batches van ten minste 20 data bases.

### <a name="benefit"></a>Voordeel

Het belangrijkste voor deel van deze strategie is dat deze de hoogste SLA biedt voor de betalende klanten. Het zorgt er ook voor dat de blok kering van de nieuwe experimenten wordt opgeheven zodra de proef versie van DR-groep wordt gemaakt. 

### <a name="trade-off"></a>Afhandelen

De afweging is dat deze instelling de totale kosten van de Tenant databases verhoogt door de kosten van de secundaire DR-groep voor betaalde klanten. Als de secundaire groep een andere grootte heeft, hebben de betalende klanten bovendien de prestaties na de failover geringt tot de groeps upgrade in de DR-regio is voltooid.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografisch gedistribueerde toepassing met gelaagde service

Ik heb een rijpe SaaS-toepassing met aanbiedingen voor gelaagde Services. Ik wil een zeer agressieve SLA aan mijn betaalde klanten aanbieden en zo het risico van gevolgen voor het geval van storingen beperken, omdat zelfs korte onderbrekingen de klant niet kan afwegen. Het is essentieel dat de betalende klanten altijd toegang hebben tot hun gegevens. De tests zijn gratis en een SLA wordt niet aangeboden tijdens de proef periode.

Gebruik drie afzonderlijke elastische Pools om dit scenario te ondersteunen. Richt twee groepen van gelijke grootte in met hoge Edtu's of vCores per data base in twee verschillende regio's om de Tenant databases van de betaalde klanten te bevatten. De derde pool met de proef tenants kan lagere Edtu's of vCores per data base hebben en in een van de twee regio's worden ingericht.

Om de laagste herstel tijd tijdens storingen te garanderen, zijn de Tenant-data bases van de betalende klanten geo-gerepliceerd met 50% van de primaire data bases in elk van de twee regio's. Op dezelfde manier heeft elke regio 50% van de secundaire data bases. Op deze manier geldt dat als een regio offline is, slechts 50% van de data bases van de betaalde klanten worden beïnvloed en failover moet worden uitgevoerd. De andere data bases blijven intact. Deze configuratie wordt geïllustreerd in het volgende diagram:

![Diagram toont een primaire regio met de naam regio A en secundaire regio regio B, waarbij geo-replicatie tussen de beheer database en de primaire groep van betaalde klanten en een secundaire pool zonder replicatie voor de groep proef klanten wordt gemaakt.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Net zoals in de vorige scenario's, zijn de beheer databases vrij actief, zodat ze kunnen worden geconfigureerd als single geo-gerepliceerde data bases (1). Dit zorgt voor de voorspel bare prestaties van de nieuwe klant abonnementen, profiel updates en andere beheer bewerkingen. Regio A is de primaire regio voor de beheer databases en de regio B wordt gebruikt voor het herstellen van de beheer databases.

De Tenant-data bases van de betalende klanten worden ook geo-gerepliceerd, maar met Primaries en secundaire zones verdeeld over regio A en regio B (2). Op deze manier kan de primaire Tenant-data bases die invloed hebben op de storing, een failover uitvoeren naar de andere regio en beschikbaar worden. De andere helft van de Tenant-data bases wordt niet beïnvloed.

In het volgende diagram ziet u de herstel stappen die moeten worden uitgevoerd als er een storing optreedt in regio A.

![Diagram toont een storing voor de primaire regio, met een failover naar de beheer database, een betaalde secundaire pool van klanten en het maken en herstellen van proef klanten naar regio B.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Direct failover van de beheer databases naar regio B (3).
* Wijzig de connection string van de toepassing zodat deze verwijst naar de beheer databases in regio B. Wijzig de beheer databases om ervoor te zorgen dat de nieuwe accounts en Tenant databases worden gemaakt in regio B en dat ook de bestaande Tenant databases worden gevonden. De bestaande proef klanten zien hun gegevens tijdelijk niet beschikbaar.
* Failover van de data bases van de betaalde Tenant naar Pool 2 in regio B om hun Beschik baarheid onmiddellijk te herstellen (4). Omdat de failover een snelle wijzigingen in het meta gegevens niveau is, kunt u een optimalisatie overwegen waarbij de afzonderlijke failovers op aanvraag worden geactiveerd door de verbindingen van de eind gebruiker.
* Aangezien de groep 2 nu alleen primaire data bases bevat, neemt de totale werk belasting in de pool toe en kan de eDTU-grootte (5) of het aantal vCores onmiddellijk worden verhoogd.
* Maak de nieuwe elastische pool met dezelfde naam en dezelfde configuratie in de regio B voor de data bases van de proef klanten (6).
* Als de groep is gemaakt, gebruikt u geo-Restore om de afzonderlijke Tenant database van de proef versie te herstellen naar de groep (7). U kunt overwegen de afzonderlijke herstel bewerkingen te activeren door de eind gebruiker of door een ander toepassings-specifiek prioriteits schema te gebruiken.

> [!NOTE]
> De failoverbewerking is asynchroon. Als u de herstel tijd wilt minimaliseren, is het belang rijk dat u de failover-opdracht van de Tenant-data bases uitvoert in batches van ten minste 20 data bases.

Uw toepassing is op dit moment weer online in regio B. Alle betalende klanten hebben toegang tot hun gegevens terwijl de proef klanten een vertraging ondervinden bij het openen van hun gegevens.

Wanneer regio A wordt hersteld, moet u beslissen of u regio B wilt gebruiken voor proef klanten of failback naar het gebruik van de proef klanten pool in regio A. Eén criterium kan het% van de door de Tenant-data bases voor de evaluatie versie gewijzigd sinds het herstel. Ongeacht die beslissing moet u de betaalde tenants tussen twee Pools opnieuw verdelen. in het volgende diagram ziet u hoe het proces wordt weer gegeven wanneer de back-updatabase van de proef versie wordt teruggedraaid naar regio A.  

![Diagram toont de failback-stappen die moeten worden geïmplementeerd na het herstellen van regio A.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Alle openstaande geo-herstel aanvragen voor een DR-groep voor de proef versie annuleren.
* Failover van de beheer database (8). Na het herstel van de regio werd de oude primaire automatisch de secundaire. Nu wordt het de primaire keer weer.  
* Selecteer welke betaalde Tenant databases een failback uitvoeren naar pool 1 en failover initiëren naar hun secundaire zones (9). Na het herstel van de regio worden alle data bases in groep 1 automatisch op een secundaire systeem eigen. Nu 50% van deze Primaries opnieuw.
* Verklein de grootte van Pool 2 tot de oorspronkelijke eDTU (10) of het aantal vCores.
* Stel alle herstelde proef databases in de regio B in op alleen-lezen (11).
* Voor elke data base in de DR-groep van de proef versie die sinds het herstel is gewijzigd, wijzigt u de naam of verwijdert u de overeenkomende data base in de primaire groep van de proef versie (12).
* Kopieer de bijgewerkte data bases van de DR-groep naar de primaire groep (13).
* De DR-groep verwijderen (14).

### <a name="benefit"></a>Voordeel

De belangrijkste voor delen van deze strategie zijn:

* Het biedt ondersteuning voor de meest agressieve SLA voor de betalende klanten omdat het ervoor zorgt dat een storing geen invloed kan hebben op meer dan 50% van de Tenant databases.
* Het garandeert dat de blok kering van de nieuwe experimenten wordt opgeheven zodra de Trail DR-groep wordt gemaakt tijdens het herstel.
* Het maakt efficiënter gebruik van de groeps capaciteit als 50% van de secundaire data bases in pool 1 en Pool 2 gegarandeerd minder actief zijn dan de primaire data bases.

### <a name="trade-offs"></a>Afwegingen

De belangrijkste handels transacties zijn:

* De ruwe bewerkingen voor de beheer databases hebben een lagere latentie voor de eind gebruikers die zijn verbonden met regio A dan voor de eind gebruikers die zijn verbonden met regio B, wanneer ze worden uitgevoerd op basis van de primaire beheer databases.
* Het vereist complexere ontwerp van de beheer database. Elk Tenant record heeft bijvoorbeeld een locatie-tag die tijdens de failover en failback moet worden gewijzigd.  
* De betalende klanten kunnen lagere prestaties ondervinden dan gebruikelijk tot de upgrade van de groep in regio B is voltooid.

## <a name="summary"></a>Samenvatting

Dit artikel richt zich op de strategieën voor herstel na nood gevallen voor de data base-laag die wordt gebruikt door een SaaS ISV-toepassing met meerdere tenants. De strategie die u kiest, is gebaseerd op de behoeften van de toepassing, zoals het bedrijfs model, de SLA die u aan uw klanten, budget beperking enzovoort wilt aanbieden. Elke beschreven strategie geeft een overzicht van de voor delen en de trans actie, zodat u een weloverwogen beslissing kunt nemen. Daarnaast bevat uw specifieke toepassing waarschijnlijk andere Azure-onderdelen. Daarom kunt u de richt lijnen voor bedrijfs continuïteit door nemen en het herstel van de database laag met hen organiseren. Raadpleeg [cloud oplossingen ontwerpen voor herstel na nood gevallen](designing-cloud-solutions-for-disaster-recovery.md)voor meer informatie over het beheren van het herstel van database toepassingen in Azure.  

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure SQL database automatische back-ups](automated-backups-overview.md)voor meer informatie over Azure SQL database automatische back-ups.
* Raadpleeg [Overzicht van bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een bedrijfscontinuïteitsoverzicht en bedrijfscontinuïteitsscenario's.
* Zie [een Data Base herstellen vanuit de door de service geïnitieerde back-ups](recovery-using-backups.md)voor meer informatie over het gebruik van automatische back-ups voor herstel.
* Zie [actieve geo-replicatie](active-geo-replication-overview.md) en [groepen voor automatische failover](auto-failover-group-overview.md)voor meer informatie over snellere herstel opties.
* Zie [Data Base Copy](database-copy.md)(Engelstalig) voor meer informatie over het gebruik van automatische back-ups voor archivering.
