---
title: SaaS-patronen met meerdere tenants
description: Meer informatie over de vereisten en algemene gegevens architectuur patronen voor SaaS-database toepassingen (multi tenant software as a Service) die worden uitgevoerd in de Azure-cloud omgeving.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793225"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Multitenancy-patronen voor SaaS-data base met meerdere tenants
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel worden de verschillende pacht modellen beschreven die beschikbaar zijn voor een SaaS-toepassing met meerdere tenants.

Bij het ontwerpen van een multi tenant SaaS-toepassing moet u zorgvuldig het pacht-model kiezen dat het beste past bij de behoeften van uw toepassing.  Een pacht model bepaalt hoe de gegevens van elke Tenant worden toegewezen aan de opslag.  Uw keuze van het pacht-model is van invloed op het ontwerp en beheer van toepassingen.  Het is ook mogelijk dat u later overschakelt naar een ander model.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-concepten en terminologie

In het SaaS-model (Software as a Service) verkoopt uw bedrijf geen *licenties* voor uw software. In plaats daarvan brengt elke klant rente betaling aan uw bedrijf en maakt elke klant een *Tenant* van uw bedrijf.

Bij het retour neren van huren ontvangt elke Tenant toegang tot uw SaaS-toepassings onderdelen en zijn de gegevens opgeslagen in het SaaS-systeem.

Het term *pacht-model* verwijst naar de manier waarop tenants opgeslagen gegevens zijn ingedeeld:

- *Enkel pacht:* &nbsp; Elke Data Base slaat gegevens op uit slechts één Tenant.
- Multitenancy *:* &nbsp; Elke Data Base slaat gegevens op uit meerdere afzonderlijke tenants (met mechanismen voor het beveiligen van de privacy van gegevens).
- Er zijn ook Hybrid pacht-modellen beschikbaar.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Het juiste pacht-model kiezen

In het algemeen heeft het pacht-model geen invloed op de werking van een toepassing, maar dit is waarschijnlijk van invloed op andere aspecten van de algemene oplossing.  De volgende criteria worden gebruikt voor het evalueren van elk model:

- **Schaal baarheid**
    - Aantal tenants.
    - Opslag per Tenant.
    - Opslag in samen voeging.
    - Werklast.

- **Tenant isolatie:** &nbsp; Gegevens isolatie en prestaties (of de werk belasting van een Tenant invloed heeft op andere).

- **Kosten per Tenant:** &nbsp; Database kosten.

- **Complexiteit van de ontwikkeling:**
    - Wijzigingen in het schema.
    - Wijzigingen in query's (vereist voor het patroon).

- **Operationele complexiteit:**
    - Prestaties bewaken en beheren.
    - Schema beheer.
    - Een Tenant herstellen.
    - Herstel na noodgevallen.

- **Aanpassings mogelijkheden:** &nbsp; Eenvoudige ondersteuning van schema aanpassingen die specifiek zijn voor een Tenant of Tenant.

De pacht-discussie is gericht op de *gegevenslaag.*  Houd echter rekening met de laag van de *toepassingslaag* .  De toepassingslaag wordt behandeld als een monolithische-entiteit.  Als u de toepassing verdeelt in veel kleine onderdelen, kan uw keuze van het pacht-model veranderen.  U kunt sommige onderdelen anders behandelen dan andere met betrekking tot de pacht en de opslag technologie of het gebruikte platform.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Zelfstandige app met één Tenant met een Data Base met één Tenant

#### <a name="application-level-isolation"></a>Isolatie op toepassings niveau

In dit model wordt de hele toepassing herhaaldelijk geïnstalleerd en eenmaal voor elke Tenant.  Elk exemplaar van de app is een zelfstandig exemplaar, dus het werkt nooit met een wille keurig ander zelfstandig exemplaar.  Elk exemplaar van de app heeft slechts één Tenant en heeft daarom slechts één data base nodig.  De Tenant heeft de data base allemaal aan zichzelf.

![Ontwerp van een zelfstandige app met precies één Tenant database.][image-standalone-app-st-db-111a]

Elk exemplaar van de app wordt geïnstalleerd in een afzonderlijke Azure-resource groep.  De resource groep kan behoren tot een abonnement dat eigendom is van de software leverancier of de Tenant.  In beide gevallen kan de leverancier de software voor de Tenant beheren.  Elk toepassings exemplaar is geconfigureerd om verbinding te maken met de bijbehorende data base.

Elke Tenant database wordt als één data base geïmplementeerd.  Dit model biedt de grootste database isolatie.  Maar de isolatie vereist dat er voldoende resources worden toegewezen aan elke Data Base om de piek belastingen te kunnen afhandelen.  Hier ziet u dat elastische Pools niet kunnen worden gebruikt voor data bases die in verschillende resource groepen of op verschillende abonnementen worden geïmplementeerd.  Deze beperking maakt deze zelfstandige app voor één Tenant de meest dure oplossing van een algemeen data base-kosten perspectief.

#### <a name="vendor-management"></a>Leveranciers beheer

De leverancier heeft toegang tot alle data bases in alle zelfstandige app-instanties, zelfs als de app-exemplaren in verschillende Tenant abonnementen zijn geïnstalleerd.  De toegang wordt bereikt via SQL-verbindingen.  Deze cross-instance Access kan de leverancier in staat stellen schema beheer en query's voor meerdere data bases te centraliseren voor rapportage-of analyse doeleinden.  Als dit soort gecentraliseerd beheer gewenst is, moet er een catalogus worden geïmplementeerd waarmee Tenant-id's worden toegewezen aan data base-Uri's.  Azure SQL Database biedt een sharding-bibliotheek die samen wordt gebruikt om een catalogus te leveren.  De sharding-bibliotheek heeft formeel de naam van de [Elastic database-client bibliotheek][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Multi tenant-app met data base per Tenant

Dit volgende patroon maakt gebruik van een toepassing met meerdere tenants met veel data bases, allemaal data bases met één Tenant.  Er wordt een nieuwe data base ingericht voor elke nieuwe Tenant.  De toepassingslaag wordt verticaal *omhoog* geschaald door meer resources per knoop punt toe te voegen.  Of de app *is horizon taal* geschaald door meer knoop punten toe te voegen.  De schaal is gebaseerd op de werk belasting en is onafhankelijk van het aantal of de schaal van de afzonderlijke data bases.

![Ontwerp van een app met meerdere tenants met data base-per-Tenant.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Aanpassen voor een Tenant

Net als bij het patroon van de zelfstandige app biedt het gebruik van data bases met één Tenant een sterke isolatie van tenants.  In een app waarvan het model alleen single tenant-data bases bevat, kan het schema voor een wille keurige Data Base worden aangepast en geoptimaliseerd voor de Tenant.  Deze aanpassing heeft geen invloed op andere tenants in de app. Misschien heeft een Tenant mogelijk gegevens nodig die groter zijn dan de basis gegevens velden die alle tenants nodig hebben.  Het is ook mogelijk dat het extra gegevens veld een index vereist.

Met data base-per-Tenant kan het schema voor een of meer afzonderlijke tenants eenvoudig worden gerealiseerd.  De leverancier van de toepassing moet procedures ontwerpen voor het zorgvuldig beheren van schema aanpassingen op schaal.

#### <a name="elastic-pools"></a>Pools voor Elastic Database

Wanneer data bases in dezelfde resource groep worden geïmplementeerd, kunnen ze worden gegroepeerd in elastische Pools.  De groepen bieden een kosteneffectieve manier om resources te delen in veel data bases.  Deze groeps optie is goed koper dan vereist dat elke Data Base groot genoeg is om te voldoen aan de gebruiks pieken die het ondervindt.  Hoewel gepoolde data bases toegang tot resources delen, kunnen ze nog steeds een hoge mate van prestaties isoleren.

![Ontwerp van een app met meerdere tenants met een Data Base per Tenant, met behulp van elastische pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database biedt de hulpprogram ma's die nodig zijn voor het configureren, bewaken en beheren van het delen.  De metrische gegevens voor de prestaties op groeps niveau en op database niveau zijn beschikbaar in het Azure Portal en via Azure Monitor-Logboeken.  De metrische gegevens kunnen zeer inzicht geven in zowel geaggregeerde als Tenant-specifieke prestaties.  Afzonderlijke data bases kunnen tussen Pools worden verplaatst om gereserveerde resources te bieden aan een specifieke Tenant.  Met deze hulpprogram ma's kunt u op een rendabele manier goede prestaties garanderen.

#### <a name="operations-scale-for-database-per-tenant"></a>Bewerkings schaal voor data base per Tenant

Azure SQL Database heeft veel beheer functies die zijn ontworpen om grote aantallen data bases op schaal te beheren, zoals ruim meer dan 100.000 data bases.  Deze functies maken het plausible van de data base per Tenant.

Stel bijvoorbeeld dat een systeem een Data Base van 1000-Tenant heeft als één data base.  De data base kan 20 indexen bevatten.  Als het systeem wordt geconverteerd naar data bases met één Tenant van 1000, wordt het aantal indexen tot 20.000.  In Azure SQL Database als onderdeel van [automatisch afstemmen][docu-sql-db-automatic-tuning-771a], zijn de functies voor automatisch indexeren standaard ingeschakeld.  Met automatisch indexeren worden alle 20.000 indexen en de huidige optimalisaties voor maken en verwijderen beheerd.  Deze geautomatiseerde acties vinden plaats in een afzonderlijke data base en ze worden niet gecoördineerd of beperkt door soort gelijke acties in andere data bases.  Bij automatische indexering worden indexen anders in een bezette data base verwerkt dan in een Data Base die minder bezet is.  Dit type aanpassing van index beheer zou niet praktisch kunnen zijn bij het schalen van de data base per Tenant als deze enorme beheer taak hand matig moet worden uitgevoerd.

Andere beheer functies die goed schaalbaar zijn, zijn als volgt:

- Ingebouwde back-ups.
- Hoge beschikbaarheid.
- Versleuteling op schijf.
- Telemetrie van prestaties.

#### <a name="automation"></a>Automation

De beheer bewerkingen kunnen worden gescripteerd en aangeboden via een [devops][http-visual-studio-devops-485m] -model.  De bewerkingen kunnen zelfs geautomatiseerd en beschikbaar zijn in de toepassing.

U kunt bijvoorbeeld het herstel van één Tenant naar een eerder tijdstip automatiseren.  Het herstel hoeft alleen maar één Tenant database te herstellen waarin de Tenant wordt opgeslagen.  Deze herstel bewerking heeft geen invloed op andere tenants, waarmee wordt bevestigd dat beheer bewerkingen op het nauw keurigste niveau van elke afzonderlijke Tenant.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Multi tenant-app met multi tenant-data bases

Een ander beschikbaar patroon is het opslaan van veel tenants in een Data Base met meerdere tenants.  Het toepassings exemplaar kan een wille keurig aantal multi tenant-data bases hebben.  Het schema van een Data Base met meerdere tenants moet een of meer Tenant-id-kolommen hebben, zodat de gegevens van een bepaalde Tenant selectief kunnen worden opgehaald.  Het schema kan verder een aantal tabellen of kolommen vereisen die alleen worden gebruikt door een subset van tenants.  Statische code en referentie gegevens worden echter slechts één keer opgeslagen en worden gedeeld door alle tenants.

#### <a name="tenant-isolation-is-sacrificed"></a>Tenant isolatie wordt gedood

*Gegevens:* &nbsp; Een multi tenant-data base bewaart per onechte Tenant isolatie.  De gegevens van meerdere tenants worden samen in één data base opgeslagen.  Zorg er tijdens de ontwikkeling voor dat query's nooit gegevens van meer dan één Tenant beschikbaar maken.  SQL Database ondersteunt [beveiliging op rijniveau][docu-sql-svr-db-row-level-security-947w], wat kan afdwingen dat de gegevens die door een query zijn geretourneerd, binnen het bereik van één Tenant vallen.

*Verwerken:* &nbsp; Een multi tenant-data base deelt reken-en opslag resources over alle tenants.  De hele data base kan worden bewaakt om ervoor te zorgen dat deze acceptabel wordt uitgevoerd.  Het Azure-systeem heeft echter geen ingebouwde manier om het gebruik van deze resources door een afzonderlijke Tenant te controleren of te beheren.  Daarom heeft de multi tenant-Data Base een groter risico op het ontstaan van ruiserende neighbors, waarbij de werk belasting van een overactieve Tenant invloed heeft op de prestatie ervaring van andere tenants in dezelfde data base.  Extra bewaking op toepassings niveau kan prestaties op Tenant niveau bewaken.

#### <a name="lower-cost"></a>Lagere kosten

In het algemeen hebben multi tenant-data bases de laagste kosten per Tenant.  De resource kosten voor één Data Base zijn lager dan voor de elastische pool met een vergelijk bare grootte.  Voor scenario's waarbij tenants alleen maar beperkte opslag nodig hebben, kunnen mogelijk miljoenen tenants worden opgeslagen in één data base.  Geen elastische pool kan miljoenen data bases bevatten.  Een oplossing met 1000-data bases per pool, met 1000-groepen, kan echter de schaal van miljoenen bereiken tegen het risico dat u het beheer onoverzichtelijk maakt.

Twee varianten van een model voor multi tenant-data bases worden in de volgende stappen beschreven, waarbij het Shard multi tenant-model het meest flexibel en schaalbaar is.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Multi tenant-app met één multi tenant-data base

Het eenvoudigste multi tenant-database patroon maakt gebruik van één Data Base voor het hosten van gegevens voor alle tenants.  Naarmate er meer tenants worden toegevoegd, wordt de data base omhoog geschaald met meer opslag-en reken resources.  Deze schaal kan allemaal nodig zijn, hoewel er altijd een limiet voor een ultieme schaal is.  Lang voordat deze limiet is bereikt, wordt de data base lastig te beheren.

Beheer bewerkingen die zijn gericht op afzonderlijke tenants zijn complexer om te worden geïmplementeerd in een Data Base met meerdere tenants.  En op schaal kunnen deze bewerkingen onaanvaardbaar langzaam worden.  Een voor beeld hiervan is een herstel bewerking van de gegevens voor slechts één Tenant.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Multi tenant-app met Shard multi tenant-data bases

De meeste SaaS-toepassingen hebben per keer toegang tot de gegevens van slechts één Tenant.  Met dit toegangs patroon kunnen Tenant gegevens worden gedistribueerd over meerdere data bases of Shards, waarbij alle gegevens voor een Tenant in één Shard zijn opgenomen.  In combi natie met een database patroon met meerdere tenants kan een Shard-model bijna onbeperkte schalen.

![Ontwerp van een multi tenant-app met Shard multi tenant-data bases.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Shards beheren

Sharding voegt complexiteit toe aan het ontwerp en operationeel beheer.  Er is een catalogus vereist waarin u de toewijzing tussen tenants en data bases kunt onderhouden.  Daarnaast zijn er beheer procedures vereist voor het beheren van de Shards en de Tenant populatie.  Procedures moeten bijvoorbeeld zijn ontworpen om Shards toe te voegen en te verwijderen en om Tenant gegevens tussen Shards te verplaatsen.  U kunt ook schalen door een nieuwe Shard toe te voegen en deze te vullen met nieuwe tenants.  Op andere momenten kunt u een Shard met een hoge dichtheid splitsen in twee minder weinig Shards gevulde gegevens.  Nadat er meerdere tenants zijn verplaatst of uit gebruik zijn genomen, kunt u de Shards met sparse gevuld samen voegen.  Het samen voegen zou leiden tot een rendabeler resource gebruik.  Tenants kunnen ook tussen Shards worden verplaatst om de werk belasting te verdelen.

SQL Database biedt een hulp programma voor splitsen en samen voegen dat werkt in combi natie met de sharding-bibliotheek en de catalogus database.  De gegeven app kan Shards splitsen en samen voegen, en kan de Tenant gegevens verplaatsen tussen Shards.  De app houdt ook de catalogus tijdens deze bewerkingen bij, waarbij de betrokken tenants als offline worden gemarkeerd voordat ze worden verplaatst.  Na de verplaatsing werkt de app de catalogus opnieuw bij met de nieuwe toewijzing en wordt de Tenant als weer online gemarkeerd.

#### <a name="smaller-databases-more-easily-managed"></a>Kleinere data bases eenvoudiger te beheren

Door tenants over meerdere data bases te distribueren, resulteert de Shard multi tenant-oplossing in kleinere data bases die eenvoudiger te beheren zijn.  Als u bijvoorbeeld een specifieke Tenant naar een eerder tijdstip herstelt, moet u nu één kleinere data base herstellen vanaf een back-up in plaats van een grotere data base met alle tenants. De grootte van de data base en het aantal tenants per data base kunnen worden gekozen om de werk belasting en de beheer taken te verdelen.

#### <a name="tenant-identifier-in-the-schema"></a>Tenant-id in het schema

Afhankelijk van de gebruikte sharding-aanpak kunnen er aanvullende beperkingen worden opgelegd voor het database schema.  De SQL Database splitsen/samen voegen-toepassing vereist dat het schema de sharding-sleutel bevat. Dit is meestal de Tenant-id.  De Tenant-id is het voorloop element in de primaire sleutel van alle Shard-tabellen.  Met de Tenant-id kan de toepassing splitsen/samen voegen snel gegevens vinden en verplaatsen die zijn gekoppeld aan een specifieke Tenant.

#### <a name="elastic-pool-for-shards"></a>Elastische pool voor Shards

Shard multi tenant-data bases kunnen in elastische Pools worden geplaatst.  Over het algemeen is het zo dat veel tenants met één Tenant in een pool rendabel zijn, omdat er in een paar data bases met meerdere tenants een aantal pachters zijn.  Multi tenant-data bases zijn handig wanneer er sprake is van een groot aantal relatief inactieve tenants.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>HxBxD. Model voor de multi tenant-data base hybride Shard

In het hybride model hebben alle data bases de Tenant-id in hun schema.  De data bases zijn allemaal geschikt om meer dan één Tenant op te slaan en de data bases kunnen worden Shard.  In het schema gevoel zijn ze dus alle data bases met meerdere tenants.  In de praktijk kunnen sommige van deze data bases slechts één Tenant bevatten.  Ongeacht het aantal tenants dat in een bepaalde data base is opgeslagen, heeft geen invloed op het database schema.

#### <a name="move-tenants-around"></a>Tenants verplaatsen

U kunt op elk gewenst moment een bepaalde Tenant verplaatsen naar een eigen multi tenant-data base.  U kunt op elk gewenst moment van gedachten veranderen en de Tenant weer verplaatsen naar een Data Base die meerdere tenants bevat.  U kunt ook een Tenant toewijzen aan een nieuwe Data Base met één Tenant wanneer u de nieuwe data base inricht.

Het hybride model schijnt wanneer er grote verschillen zijn tussen de resource behoeften van Identificeer bare groepen tenants.  Stel bijvoorbeeld dat tenants die deel uitmaken van een gratis proef versie, niet kunnen worden gegarandeerd op hetzelfde hoge prestatie niveau als bij het abonneren van tenants.  Het beleid kan voor tenants in de gratis proef versie worden opgeslagen in een multi tenant-data base die wordt gedeeld door alle tenants van de gratis proef versie.  Wanneer een gratis proef versie van een Tenant zich abonneert op de Basic-servicelaag, kan de Tenant worden verplaatst naar een andere data base met meerdere tenants die mogelijk minder tenants hebben.  Een abonnee die betaalt voor de Premium-servicelaag, kan worden verplaatst naar de eigen nieuwe Data Base met één Tenant.

#### <a name="pools"></a>Pools

In dit hybride model kunnen de data bases met één Tenant voor Subscriber-tenants in resource groepen worden geplaatst om de database kosten per Tenant te verlagen.  Dit gebeurt ook in het model data base-per Tenant.

## <a name="i-tenancy-models-compared"></a>I. Pacht modellen vergeleken

De volgende tabel bevat een overzicht van de verschillen tussen de belangrijkste pacht modellen.

| Meting | Zelfstandige app | Data base per Tenant | Shard multi tenant |
| :---------- | :------------- | :------------------ | :------------------- |
| Schalen | Normaal<br />1-100 | Zeer hoog <br />1-100, per 10.000 | Onbeperkt<br />1-1, 000, per 10.000 |
| Isolatie van tenants | Zeer hoog  | Hoog | Gebrek behalve voor één Tenant (die zich alleen in een MT-Data Base bevindt). |
| Database kosten per Tenant | Hogesnelheidsnet het formaat voor pieken. | Gebrek gebruikte groepen. | Laagst, voor kleine tenants in MT Db's. |
| Prestaties bewaken en beheren | Alleen per Tenant | Aggregatie + per Tenant | Vatting Hoewel dit per Tenant geldt, is dat alleen voor singles. |
| Complexiteit van ontwikkeling | Beperkt | Beperkt | Drager vanwege sharding. |
| Operationele complexiteit | Laag-hoog. Afzonderlijk eenvoudig, complex op schaal. | Laag medium. Patronen herkennen de complexiteit op schaal. | Laag-hoog. Het beheer van afzonderlijke tenants is complex. |
| &nbsp; ||||

## <a name="next-steps"></a>Volgende stappen

- [Implementeer en verken een multi tenant Wingtip-toepassing die gebruikmaakt van het SaaS-model data base-per-Tenant-Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Welkom bij de Wingtip-tickets voor beeld van SaaS Azure SQL Database pacht-app][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Ontwerp van een zelfstandige app met precies één Tenant database."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Ontwerp van een app met meerdere tenants met data base-per-Tenant."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Ontwerp van een app met meerdere tenants met een Data Base per Tenant, met behulp van elastische pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Ontwerp van een multi tenant-app met Shard multi tenant-data bases."