---
title: In-memory technologieën
description: In-Memory technologieën worden de prestaties van transactionele en analytische workloads in Azure SQL Database en Azure SQL Managed instance aanzienlijk verbeterd.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779948"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Optimaliseer de prestaties met behulp van in-Memory technologieën in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Met technologieën in het geheugen kunt u de prestaties van uw toepassing verbeteren en mogelijk de kosten van uw data base verlagen.

## <a name="when-to-use-in-memory-technologies"></a>Wanneer u in-Memory technologieën wilt gebruiken

Door gebruik te maken van in-Memory technologieën, kunt u prestatie verbeteringen aantreffen met verschillende workloads:

- **Transactionele** (online transactionele verwerking (OLTP)) waarbij de meeste aanvragen een kleinere set gegevens lezen of bijwerken (bijvoorbeeld ruwe bewerkingen).
- **Analytisch** (online ANALYTICAL processing (OLAP)) waarbij de meeste query's complexe berekeningen hebben voor de rapportage doeleinden, met een bepaald aantal query's waarmee gegevens worden geladen en toegevoegd aan de bestaande tabellen (dus bulksgewijs laden genoemd) of de gegevens uit de tabellen worden verwijderd.
- **Gemengde** (hybride trans actie/Analytical Processing (HTAP)) waarbij zowel OLTP-als OLAP-query's worden uitgevoerd op dezelfde set gegevens.

In-Memory technologieën kunnen de prestaties van deze werk belastingen verbeteren door de gegevens die moeten worden verwerkt in het geheugen te bewaren, met behulp van systeem eigen compilatie van de query's of geavanceerde verwerking, zoals batch verwerking en SIMD-instructies die beschikbaar zijn op de onderliggende hardware.

## <a name="overview"></a>Overzicht

Azure SQL Database en Azure SQL Managed instance hebben de volgende in-Memory technologieën:

- *[In-Memory OLTP neemt het](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aantal trans acties per seconde toe en vermindert de latentie voor de verwerking van trans acties. Scenario's die van In-Memory OLTP profiteren, zijn: transactie verwerking met hoge door Voer, zoals handel en gaming, gegevens opname van gebeurtenissen of IoT-apparaten, caching, gegevens belasting en tijdelijke scenario's voor tabel-en tabel variabelen.
- *Geclusterde column Store-indexen* verminderen uw opslag ruimte (Maxi maal 10 keer) en verbetert de prestaties voor rapportage-en analyse query's. U kunt het gebruiken met feiten tabellen in uw data bases voor het aanpassen van meer gegevens in uw gegevens bron en de prestaties te verbeteren. Daarnaast kunt u het gebruiken met historische gegevens in uw operationele data base om te archiveren en om Maxi maal tien keer meer gegevens op te vragen.
- Niet- *geclusterde column Store-indexen* voor HTAP helpen u real-time inzicht te krijgen in uw bedrijf door rechtstreeks query's uit te voeren op de operationele data base, zonder dat u een kostbaar proces voor extra heren, transformeren en laden (ETL) moet uitvoeren en moet wachten tot het Data Warehouse is gevuld. Met niet-geclusterde column Store-indexen kunnen analyse query's snel worden uitgevoerd op de OLTP-data base, en wordt de impact op de operationele werk belasting verminderd.
- Met *geheugen geoptimaliseerde column Store-indexen* voor HTAP kunt u snelle transactie verwerking uitvoeren, en *tegelijkertijd* analyse query's op dezelfde gegevens uitvoeren.

Zowel column Store-indexen als In-Memory OLTP zijn deel van het SQL Server product sinds 2012 en 2014. Azure SQL Database, Azure SQL Managed instance en SQL Server dezelfde implementatie van in-Memory technologieën te delen.

## <a name="benefits-of-in-memory-technology"></a>Voor delen van de technologie in het geheugen

Vanwege de efficiëntere query-en transactie verwerking, kunt u in-Memory technologieën ook de kosten verlagen. Normaal gesp roken hoeft u de prijs categorie van de data base niet bij te werken om de prestaties te verbeteren. In sommige gevallen is het mogelijk dat u de prijs categorie ook kunt verlagen, terwijl de prestatie verbeteringen met in-Memory technologieën nog steeds worden weer gegeven.

Hier volgen twee voor beelden van hoe In-Memory OLTP de prestaties aanzienlijk kan verbeteren:

- Met behulp van In-Memory OLTP konden [quorum bedrijfs oplossingen hun werk belasting verdubbelen terwijl dtu's met 70% wordt verbeterd](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- In de volgende video ziet u een aanzienlijke verbetering in het gebruik van resources met een voor beeld van een werk belasting: [in-Memory OLTP-video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Zie het blog bericht: [in-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (Engelstalig) voor meer informatie.

> [!NOTE]  
> In-Memory technologieën zijn beschikbaar in de Premium-en Bedrijfskritiek-laag.

In de volgende video worden mogelijke prestatie verbeteringen met in-Memory technologieën uitgelegd. Houd er rekening mee dat de prestatie verbetering die u altijd ziet, afhankelijk is van een groot aantal factoren, zoals de aard van de werk belasting en de gegevens, het toegangs patroon van de data base, enzovoort.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

In dit artikel worden de aspecten beschreven van In-Memory OLTP-en column Store-indexen die specifiek zijn voor Azure SQL Database en Azure SQL Managed instance en bevatten ook voor beelden:

- U ziet de impact van deze technologieën op opslag-en gegevens grootte limieten.
- U ziet hoe u de verplaatsing beheert van data bases die gebruikmaken van deze technologieën tussen de verschillende prijs categorieën.
- U ziet twee voor beelden die het gebruik van In-Memory OLTP en column Store-indexen illustreren.

Zie voor meer informatie over in-memory in SQL Server:

- [Overzicht van in-Memory OLTP en gebruiksscenario's](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (bevat verwijzingen naar casestudy's van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Gids columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hybride transactionele/analytische verwerking (HTAP), ook wel bekend onder de naam [real-time operationele analyses](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>In-Memory OLTP

In-Memory OLTP-technologie biedt extreem snelle bewerkingen voor gegevens toegang door alle gegevens in het geheugen te bewaren. Het maakt ook gebruik van gespecialiseerde indexen, systeem eigen compilatie van query's en vrije gegevens toegang om de prestaties van de OLTP-werk belasting te verbeteren. Er zijn twee manieren om de In-Memory OLTP-gegevens in te delen:

- Voor het **geheugen geoptimaliseerde rowstore** -indeling, waarbij elke rij een afzonderlijk geheugen object is. Dit is een klassieke In-Memory OLTP-indeling die is geoptimaliseerd voor de hoge prestaties van OLTP-workloads. Er zijn twee typen tabellen die zijn geoptimaliseerd voor geheugen die kunnen worden gebruikt in de rowstore-indeling die is geoptimaliseerd voor geheugen:

  - *Duurzame tabellen* (SCHEMA_AND_DATA) waarbij de rijen in het geheugen worden bewaard na het opnieuw opstarten van de server. Dit type tabellen gedraagt zich als een traditionele rowstore-tabel met de extra voor delen van in-Memory optimalisaties.
  - *Niet-duurzame tabellen* (SCHEMA_ONLY) waarin de rijen niet worden bewaard na opnieuw opstarten. Dit type tabel is bedoeld voor tijdelijke gegevens (bijvoorbeeld vervangen van tijdelijke tabellen) of tabellen waarin u snel gegevens moet laden voordat u deze naar een andere persistente tabel verplaatst (dat wil zeggen faserings tabellen genoemd).

- **Met geheugen geoptimaliseerde column Store-** indeling waarbij gegevens in een kolom indeling worden ingedeeld. Deze structuur is ontworpen voor HTAP-scenario's waarin u analytische query's moet uitvoeren op dezelfde gegevens structuur waar de OLTP-werk belasting wordt uitgevoerd.

> [!Note]
> In-Memory OLTP-technologie is ontworpen voor de gegevens structuren die volledig in het geheugen kunnen worden opgeslagen. Omdat de in-Memory gegevens niet naar de schijf kunnen worden geoffload, moet u ervoor zorgen dat u de data base gebruikt die voldoende geheugen heeft. Zie [gegevens grootte en opslag limiet voor In-Memory OLTP](#data-size-and-storage-cap-for-in-memory-oltp) voor meer informatie.

Een snelle primer op In-Memory OLTP: [Snelstartgids 1: In-Memory OLTP-technologieën voor snellere T-SQL-prestaties](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (een ander artikel om u te helpen aan de slag)

Uitgebreide Video's over de technologieën:

- [In-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (dit bevat een demo van de prestatie voordelen en stappen om deze resultaten zelf te reproduceren)
- [In-Memory OLTP-Video's: wat is het en wanneer/hoe u deze kunt gebruiken](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

Er is een programmatische manier om te begrijpen of een bepaalde data base In-Memory OLTP ondersteunt. U kunt de volgende Transact-SQL-query uitvoeren:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Als de query **1** retourneert, wordt In-Memory OLTP ondersteund in deze data base. Met de volgende query's worden alle objecten geïdentificeerd die moeten worden verwijderd voordat een Data Base kan worden gedowngraded naar Algemeen, Standard of Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Gegevens grootte en opslag limiet voor In-Memory OLTP

In-Memory OLTP bevat tabellen die zijn geoptimaliseerd voor geheugen, die worden gebruikt voor het opslaan van gebruikers gegevens. Deze tabellen moeten in het geheugen passen. Omdat u het geheugen rechtstreeks beheert in SQL Database, hebben we het concept van een quotum voor gebruikers gegevens. Dit wordt aangeduid als *OLTP-opslag in het geheugen*.

Elke ondersteunde prijs categorie voor één data base en elke prijs categorie voor elastische Pools bevat een bepaalde hoeveelheid In-Memory OLTP-opslag.

- [Op DTU gebaseerde resource limieten-één data base](database/resource-limits-dtu-single-databases.md)
- [Op DTU gebaseerde resource limieten-elastische Pools](database/resource-limits-dtu-elastic-pools.md)
- [resource limieten op basis van vCore-afzonderlijke data bases](database/resource-limits-vcore-single-databases.md)
- [vCore-gebaseerde resource limieten: elastische Pools](database/resource-limits-vcore-elastic-pools.md)
- [op vCore gebaseerde resource limieten-beheerd exemplaar](managed-instance/resource-limits.md)

De volgende items tellen bij uw In-Memory OLTP-opslag limiet:

- Gegevens rijen van actieve gebruikers in tabellen die zijn geoptimaliseerd voor geheugen en tabel variabelen. De oude rijkoppen tellen niet mee voor het kapje.
- Indexen op tabellen die zijn geoptimaliseerd voor geheugen.
- Operationele overhead van ALTER TABLE-bewerkingen.

Als u het kapje bereikt, ontvangt u een fout melding met de melding dat u geen gegevens meer kunt invoegen of bijwerken. Als u deze fout wilt verhelpen, verwijdert u de gegevens of verhoogt u de prijs categorie van de data base of groep.

Zie [in-Memory-opslag controleren](in-memory-oltp-monitor-space.md)voor meer informatie over het controleren van het gebruik van In-Memory OLTP-opslag en het configureren van waarschuwingen wanneer u bijna de limiet bereikt.

#### <a name="about-elastic-pools"></a>Over elastische Pools

Met elastische Pools wordt de In-Memory OLTP-opslag gedeeld in alle data bases in de groep. Het gebruik in de ene data base kan daarom van invloed zijn op andere data bases. Er zijn twee oplossingen voor dit:

- Configureer een `Max-eDTU` of `MaxvCore` voor data bases die kleiner zijn dan het aantal eDTU-of vCore voor de groep als geheel. Dit maximum is het gebruik van de In-Memory OLTP-opslag, in elke data base in de pool, tot de grootte die overeenkomt met het aantal eDTU.
- Configureer een `Min-eDTU` of `MinvCore` dat groter is dan 0. Dit minimum garandeert dat elke data base in de pool de hoeveelheid beschik bare In-Memory OLTP-opslag heeft die overeenkomt met het geconfigureerde `Min-eDTU` of `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Service lagen wijzigen van data bases die gebruikmaken van In-Memory OLTP-technologieën

U kunt uw data base of exemplaar altijd upgraden naar een hogere laag, bijvoorbeeld van Algemeen naar Bedrijfskritiek (of Standard to Premium). De beschik bare functionaliteit en bronnen worden alleen verhoogd.

Het downgradeen van de laag kan echter een negatieve invloed hebben op uw data base. De impact is vooral duidelijk wanneer u een downgrade van Bedrijfskritiek naar Algemeen (of Premium to Standard of Basic) wanneer uw data base In-Memory OLTP-objecten bevat. Tabellen die zijn geoptimaliseerd voor geheugen, zijn niet beschikbaar na de downgrade (zelfs als ze zichtbaar blijven). Deze overwegingen zijn van toepassing wanneer u de prijs categorie van een elastische pool verlaagt of een Data Base met in-Memory technologieën verplaatst naar een elastische Algemeen, Standard of Basic.

> [!Important]
> In-Memory OLTP wordt niet ondersteund in de laag Algemeen, Standard of Basic. Daarom is het niet mogelijk om een Data Base met een In-Memory OLTP-objecten naar een van deze lagen te verplaatsen.

Voordat u de data base downgradet naar Algemeen, Standard of Basic, verwijdert u alle tabellen en tabel typen die zijn geoptimaliseerd voor geheugen en van alle systeem eigen, gecompileerde T-SQL-modules.

*Resources in bedrijfskritiek laag schalen*: gegevens in tabellen die zijn geoptimaliseerd voor geheugen, moeten passen binnen de In-Memory OLTP-opslag die is gekoppeld aan de laag van de data base of het beheerde exemplaar of beschikbaar is in de elastische pool. Als u probeert de laag te schalen of de data base te verplaatsen naar een pool die niet voldoende beschik bare In-Memory OLTP-opslag heeft, mislukt de bewerking.

## <a name="in-memory-columnstore"></a>In-Memory column Store

Met de column Store-technologie in het geheugen kunt u een grote hoeveelheid gegevens in de tabellen opslaan en er query's op uitvoeren. Column Store-technologie maakt gebruik van op kolommen gebaseerde gegevens opslag indeling en batch query verwerking om Maxi maal tien keer de query prestaties in OLAP-workloads te verkrijgen via traditionele rij-georiënteerde opslag. U kunt ook Maxi maal tien keer de gegevens compressie van de niet-gecomprimeerde gegevens grootte behaalt.
Er zijn twee soorten column Store-modellen die u kunt gebruiken om uw gegevens te organiseren:

- **Geclusterde column Store** waarbij alle gegevens in de tabel in de kolom indeling zijn ingedeeld. In dit model worden alle rijen in de tabel in de kolom indeling geplaatst, waardoor de gegevens zeer worden gecomprimeerd en kunt u snelle analytische query's en rapporten op de tabel uitvoeren. Afhankelijk van de aard van uw gegevens, is de omvang van uw gegevens mogelijk onverminderd 10x-100x. Met een geclusterd column Store-model kunt u ook snel een grote hoeveelheid gegevens (bulksgewijs laden) maken, aangezien grote batches gegevens die groter zijn dan 100.000-rijen, worden gecomprimeerd voordat ze op schijf worden opgeslagen. Dit model is een goede keuze voor de klassieke data warehouse-scenario's.
- **Niet-geclusterde column Store** waarin de gegevens worden opgeslagen in een traditionele rowstore-tabel en er een index is in de column Store-indeling die wordt gebruikt voor de analytische query's. Dit model maakt hybride Transactional-Analytic verwerking mogelijk (HTAP): de mogelijkheid om uitvoering van een real-time analyse uit te voeren op een transactionele werk belasting. OLTP-query's worden uitgevoerd op rowstore-tabel die is geoptimaliseerd voor toegang tot een kleine set rijen, terwijl OLAP-query's worden uitgevoerd op column store-index die beter geschikt is voor scans en analyses. Met de query optimalisatie wordt de rowstore-of column Store-indeling dynamisch gekozen op basis van de query. Niet-geclusterde column Store-indexen verminderen de grootte van de gegevens omdat de oorspronkelijke gegevensset in de oorspronkelijke rowstore-tabel zonder enige wijziging wordt bewaard. De grootte van de aanvullende column store-index moet echter kleiner zijn dan de equivalente index van de B-structuur.

> [!Note]
> In-Memory column Store-technologie bewaart alleen de gegevens die nodig zijn voor verwerking in het geheugen, terwijl de gegevens die niet in het geheugen passen, op schijf worden opgeslagen. Daarom kan de hoeveelheid gegevens in column Store-structuren in het geheugen groter zijn dan de hoeveelheid beschikbaar geheugen.

Diep gaande video over de technologie:

- [Column store-index: in-Memory Analytics Video's van Ignite 2016](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Gegevens grootte en-opslag voor column Store-indexen

Column Store-indexen hoeven niet in het geheugen te passen. Daarom is de enige limiet voor de grootte van de indexen de maximale totale database grootte, die wordt beschreven in het [op DTU gebaseerde aankoop model](database/service-tiers-dtu.md) en [op vCore gebaseerde inkoop model](database/service-tiers-vcore.md) artikelen.

Wanneer u geclusterde column Store-indexen gebruikt, wordt kolom compressie gebruikt voor de basis tabel opslag. Deze compressie kan de opslag capaciteit van uw gebruikers gegevens aanzienlijk verminderen. Dit betekent dat u meer gegevens in de Data Base kunt aanpassen. En de compressie kan verder worden verhoogd met de [kolom archivering compressie](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression). De hoeveelheid compressie die u kunt behaalt, is afhankelijk van de aard van de gegevens, maar tien keer de compressie is niet ongebruikelijk.

Als u bijvoorbeeld een Data Base hebt met een maximale grootte van 1 terabyte (TB) en u de compressie 10 keer behaalt met behulp van Column Store-indexen, kunt u in totaal 10 TB aan gebruikers gegevens in de Data Base gebruiken.

Wanneer u niet-geclusterde column Store-indexen gebruikt, wordt de basis tabel nog steeds opgeslagen in de traditionele rowstore-indeling. Daarom zijn de opslag besparingen niet zo belang rijk als bij geclusterde column Store-indexen. Als u echter een aantal traditionele, niet-geclusterde indexen wilt vervangen door een enkele column store-index, kunt u nog steeds een totale besparing zien in de opslag ruimte voor de tabel.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Service lagen van data bases met column Store-indexen wijzigen

Het *downgradeen van één Data Base naar Basic of Standard* is mogelijk niet mogelijk als uw doellaag lager is dan S3. Column Store-indexen worden alleen ondersteund in de prijs categorie Bedrijfskritiek/Premium en op de Standard-laag, S3 en hoger, en niet op de laag Basic. Wanneer u uw data base downgradet naar een niet-ondersteunde laag of niveau, wordt uw column store-index niet meer beschikbaar. Het systeem behoudt uw column store-index, maar maakt geen gebruik van de index. Als u later een upgrade naar een ondersteunde laag of een ondersteund niveau uitvoert, is uw column store-index onmiddellijk gereed om opnieuw te worden gebruikt.

Als u een **geclusterde** column store-index hebt, wordt de hele tabel na de downgrade niet meer beschikbaar. Daarom wordt u aangeraden alle *geclusterde* column Store-indexen weg te halen voordat u de data base downgradet naar een niet-ondersteunde laag of een niet-ondersteund niveau.

> [!Note]
> Een door SQL beheerd exemplaar ondersteunt column Store-indexen in alle lagen.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Volgende stappen

- [Snelstart 1: In-Memory OLTP voor snellere T-SQL-prestaties](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](in-memory-oltp-configure.md)
- [In-Memory OLTP-opslag controleren](in-memory-oltp-monitor-space.md) voor In-Memory OLTP
- [Functies in het geheugen proberen](in-memory-sample.md)

## <a name="additional-resources"></a>Aanvullende bronnen

### <a name="deeper-information"></a>Meer gedetailleerde informatie

- [Meer informatie over hoe quorum de werk belasting van de Key Data Base verdubbelt tijdens het verlagen van DTU met 70% met In-Memory OLTP in SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [In-Memory OLTP-blog bericht](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Meer informatie over In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Meer informatie over columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [Meer informatie over realtime operationele analyses](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- Zie [Veelvoorkomende workloadpatronen en overwegingen bij migratie](/previous-versions/dn673538(v=msdn.10)) (waarin wordt beschreven welke de workloadpatronen zijn waarbij In-Memory OLTP vaak significante prestatiewinsten biedt)

### <a name="application-design"></a>Toepassingsontwerp

- [OLTP in het geheugen (in-Memory optimalisatie)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](in-memory-oltp-configure.md)

### <a name="tools"></a>Hulpprogramma's

- [Azure-portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)