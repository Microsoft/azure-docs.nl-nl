---
title: Wat is de grootschalige?
description: In dit artikel wordt de grootschalige in het op vCore gebaseerde aankoop model beschreven in Azure SQL Database en wordt uitgelegd hoe dit verschilt van de Algemeen en Bedrijfskritiek service lagen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: 2e03b6fe189f11b6f8e855137438859360df686d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505396"
---
# <a name="hyperscale-service-tier"></a>Hyperscale-servicelaag

Azure SQL Database is gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99% Beschik baarheid te garanderen, zelfs in het geval van infrastructuur fouten. Er zijn drie architectuur modellen die worden gebruikt in Azure SQL Database:

- Algemeen/standaard
- Hyperscale
- Bedrijfskritiek/Premium

De grootschalige van de servicelaag in Azure SQL Database is de nieuwste servicelaag in het op vCore gebaseerde aankoop model. Deze servicelaag is een zeer schaal bare prestatie-laag voor opslag en reken kracht die gebruikmaakt van de Azure-architectuur om de opslag-en reken resources te schalen voor een Azure SQL Database die groter is dan de limieten die beschikbaar zijn voor de Algemeen en Bedrijfskritiek service lagen.

> [!NOTE]
>
> - Zie [Algemeen](service-tier-general-purpose.md) en [bedrijfskritiek](service-tier-business-critical.md) service lagen voor meer informatie over de service lagen algemeen en bedrijfskritiek in het op vCore gebaseerde aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](purchasing-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.
> - De grootschalige is momenteel alleen beschikbaar voor Azure SQL Database en niet voor Azure SQL Managed instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Wat zijn de grootschalige-mogelijkheden?

De grootschalige van de service tier in Azure SQL Database biedt de volgende aanvullende mogelijkheden:

- Ondersteuning voor Maxi maal 100 TB aan database grootte
- Bijna momentane database back-ups (op basis van moment opnamen van bestanden die zijn opgeslagen in Azure Blob Storage), ongeacht de grootte zonder IO-impact op reken bronnen  
- Snel terugzetten van de data base (op basis van moment opnamen van bestanden) in minuten in plaats van uren of dagen (geen grootte van de gegevens bewerking)
- Betere prestaties als gevolg van een betere logboek doorvoer en snellere doorvoer tijden voor trans acties, ongeacht gegevens volumes
- Snel uitschalen: u kunt een of meer alleen-lezen knoop punten inrichten voor het offloaden van uw Lees werk belasting en voor gebruik als hot stand-by
- Snel omhoog schalen: u kunt in constant tijd uw reken resources zo schalen dat er zware werk belastingen worden gemaakt wanneer dat nodig is, en vervolgens de reken bronnen weer lager instellen wanneer ze niet nodig zijn.

De grootschalige-service laag verwijdert veel van de praktische limieten die traditioneel in Cloud databases worden weer gegeven. Wanneer de meeste andere data bases worden beperkt door de resources die beschikbaar zijn in één knoop punt, hebben de data bases in de grootschalige-servicelaag geen limieten. Met de flexibele opslag architectuur neemt opslag toe als dat nodig is. In werkelijkheid worden grootschalige-data bases niet gemaakt met een gedefinieerde maximale grootte. Een grootschalige-data base groeit zo nodig en u wordt alleen gefactureerd voor de capaciteit die u gebruikt. Voor lees-intensieve workloads biedt de grootschalige-servicelaag snel uitschalen door extra Lees replica's in te richten als dat nodig is voor het offloaden van Lees werkbelastingen.

Daarnaast is de tijd die nodig is voor het maken van database back-ups of om omhoog of omlaag te schalen niet meer gekoppeld aan het volume van de gegevens in de data base. U kunt vrijwel onmiddellijk back-ups maken van grootschalige-data bases. U kunt in slechts enkele minuten een Data Base schalen in tien tallen of meer. Met deze functie kunt u zich zorgen maken over de mogelijkheden van de eerste configuratie opties.

Zie [kenmerken](service-tiers-vcore.md#service-tiers)van de grootschalige voor meer informatie over de berekenings grootten voor de servicelaag.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wie moet rekening houden met de grootschalige-servicelaag

De grootschalige is bedoeld voor de meeste bedrijfs werkbelastingen, omdat deze zeer flexibel en hoge prestaties biedt met onafhankelijk schaal bare reken-en opslag resources. Met de mogelijkheid om opslag Maxi maal 100 TB automatisch te schalen, is het een uitstekende keuze voor klanten die:

- Een grote data base on-premises hebben en hun toepassingen willen moderniseren door over te stappen op de Cloud
- Bevinden zich al in de Cloud en worden beperkt door de maximale database grootte beperkingen van andere service lagen (1-4 TB)
- Hebben kleinere data bases, maar vereisen snelle verticale en horizontale reken schalen, hoge prestaties, directe back-ups en snelle database herstel.

De grootschalige-servicelaag ondersteunt een breed scala aan SQL Server workloads, van zuivere OLTP tot zuivere analyses, maar is hoofd zakelijk geoptimaliseerd voor OLTP-en Hybrid Trans Action-en Analytical Processing-workloads (HTAP).

> [!IMPORTANT]
> Elastische Pools bieden geen ondersteuning voor de grootschalige-servicelaag.

## <a name="hyperscale-pricing-model"></a>Grootschalige-prijs model

Grootschalige is alleen beschikbaar in het [vCore-model](service-tiers-vcore.md). Als u wilt uitlijnen met de nieuwe architectuur, is het prijs model iets anders dan Algemeen of Bedrijfskritiek service lagen:

- **Berekenen**:

  De grootschalige reken eenheids prijs is per replica. De [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) prijs wordt toegepast om automatisch schaal replica's te lezen. Er wordt standaard een primaire replica en één alleen-lezen replica per grootschalige-data base gemaakt.  Gebruikers kunnen het totaal aantal replica's aanpassen, met inbegrip van de primaire van 1-5.

- **Opslag**:

  U hoeft niet de maximale gegevens grootte op te geven bij het configureren van een grootschalige-data base. In de grootschalige-laag worden er kosten in rekening gebracht voor de opslag voor uw Data Base op basis van de werkelijke toewijzing. Opslag wordt automatisch toegewezen tussen 40 GB en 100 TB, in stappen van 10 GB. Meerdere gegevens bestanden kunnen op hetzelfde moment worden uitgebreid als dat nodig is. Een grootschalige-data base wordt gemaakt met een begin grootte van 10 GB en deze wordt met 10 GB elke 10 minuten verg root totdat de grootte van 40 GB wordt bereikt.

Zie [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over prijzen voor grootschalige

## <a name="distributed-functions-architecture"></a>Architectuur van gedistribueerde functies

In tegens telling tot traditionele data base-engines die alle gegevens beheer functies op één locatie/proces hebben gecentraliseerd (ook wel gedistribueerde data bases in productie hebben, hebben meerdere exemplaren van een monolithische-gegevens engine), is een grootschalige-data base gescheiden van de engine voor de verwerking van query's, waarbij de semantiek van verschillende gegevens engines afwijkt, van de onderdelen die opslag Op deze manier kan de opslag capaciteit zo ver mogelijk naar behoefte worden geschaald (het eerste doel is 100 TB). Alleen-lezen replica's delen dezelfde opslag onderdelen, zodat er geen gegevens kopie vereist is om een nieuwe Lees bare replica te maken.

In het volgende diagram ziet u de verschillende typen knoop punten in een grootschalige-Data Base:

![architectuur](./media/service-tier-hyperscale/hyperscale-architecture.png)

Een grootschalige-Data Base bevat de volgende verschillende soorten onderdelen:

### <a name="compute"></a>Compute

Het reken knooppunt is de locatie waar de relationele engine zich bevindt. Dit is waar taal-, query-en transactie verwerking plaatsvindt. Alle gebruikers interacties met een grootschalige-data base vinden plaats via deze reken knooppunten. Reken knooppunten hebben op SSD gebaseerde caches in het voor gaande diagram om het aantal netwerk round trips dat nodig is om een pagina met gegevens op te halen, zo klein mogelijk te houden. Er is één primair reken knooppunt waar alle werk belastingen en trans acties voor lezen/schrijven worden verwerkt. Er zijn een of meer secundaire reken knooppunten die fungeren als hot standby-knoop punten voor failover-doel einden, en fungeren als alleen-lezen Compute-knoop punten voor het offloaden van Lees werkbelastingen (als deze functionaliteit gewenst is).

De data base-engine die op grootschalige Compute-knoop punten wordt uitgevoerd, is hetzelfde als in andere Azure SQL Database Service lagen. Wanneer gebruikers communiceren met de data base-engine op grootschalige Compute-knoop punten, zijn de ondersteunde surface area en Engine gedrag hetzelfde als in andere service lagen, met uitzonde ring van [bekende beperkingen](#known-limitations).

### <a name="page-server"></a>Pagina Server

Pagina servers zijn systemen die een scale-out opslag engine vertegenwoordigen.  Elke pagina Server is verantwoordelijk voor een subset van de pagina's in de data base.  De meeste pagina's van de pagina bepalen ten opzichte van Maxi maal 128 GB of Maxi maal 1 TB aan gegevens. Er worden geen gegevens gedeeld op meer dan één pagina Server (buiten de pagina replica's die worden bewaard voor redundantie en beschik baarheid). De taak van een pagina Server is het uitvoeren van database pagina's naar de reken knooppunten op aanvraag en om te voor komen dat de pagina's worden bijgewerkt als update gegevens van trans acties. Pagina servers worden bijgewerkt door logboek records van de logboek service af te spelen. Pagina servers onderhouden ook de caches op basis van SSD voor betere prestaties. Lange termijn opslag van gegevens pagina's wordt opgeslagen in Azure Storage voor extra betrouw baarheid.

### <a name="log-service"></a>Logboek service

De logboek service accepteert logboek records van de primaire Compute-replica, slaat ze op in een duurzame cache en stuurt de logboek records door naar de rest van reken replica's (zodat ze hun caches kunnen bijwerken) en de relevante pagina ('s), zodat de gegevens daar kunnen worden bijgewerkt. Op deze manier worden alle gegevens wijzigingen van de primaire Compute-replica door de logboek service door gegeven aan alle secundaire reken replica's en pagina servers. Ten slotte worden de logboek records gepusht naar lange termijn opslag in Azure Storage. Dit is een bijna oneindige opslag opslagplaats. Dit mechanisme verwijdert de nood zaak van een veelvuldige afkap ping van het logboek. De logboek service heeft ook lokale geheugen-en SSD-caches waarmee de toegang tot logboek records kan worden versneld.

### <a name="azure-storage"></a>Azure Storage

Azure Storage bevat alle gegevens bestanden in een Data Base. Pagina servers houden gegevens bestanden in Azure Storage up-to-date. Deze opslag wordt gebruikt voor back-updoeleinden en voor replicatie tussen Azure-regio's. Back-ups worden geïmplementeerd met behulp van opslag momentopnamen van gegevens bestanden. Herstel bewerkingen met behulp van moment opnamen zijn snel, ongeacht de grootte van de gegevens. Gegevens kunnen worden hersteld naar elk gewenst moment binnen de Bewaar periode van de back-up van de data base.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Back-ups zijn gebaseerd op bestands momentopnamen en zijn daarom bijna direct. Met opslag-en Compute-schei ding kunt u de bewerking voor back-up/herstel naar de opslaglaag beperken om de verwerkings belasting van de primaire Compute replica te verminderen. Als gevolg hiervan heeft de back-up van de data base geen invloed op de prestaties van het primaire reken knooppunt. Op dezelfde manier wordt Point-in-time herstel (PITR) uitgevoerd door terug te keren naar moment opnamen van bestanden. Dit is geen grootte van de gegevens bewerking. Het herstellen van een grootschalige-data base in dezelfde Azure-regio is een constante tijd en zelfs meerdere terabyte-data bases kunnen in enkele minuten worden hersteld in plaats van uren of dagen. Het maken van nieuwe data bases door het herstellen van een bestaande back-up heeft ook voor deel van deze functie: het maken van database kopieën voor ontwikkelings-of test doeleinden, zelfs multi-terabyte-data bases, is doable binnen enkele minuten.

Zie [een grootschalige-data base herstellen naar een andere regio](#restoring-a-hyperscale-database-to-a-different-region)voor het geo-herstel van grootschalige-data bases.

## <a name="scale-and-performance-advantages"></a>Schaal-en prestatie voordelen

Met de mogelijkheid om snel extra alleen-lezen Compute-knoop punten in te zetten/te verlagen, biedt de grootschalige-architectuur aanzienlijke mogelijkheden voor lees schaal en kan het primaire reken knooppunt worden vrijgemaakt voor het uitvoeren van meer schrijf aanvragen. Ook kunnen de reken knooppunten snel omhoog of omlaag worden geschaald als gevolg van de architectuur van de grootschalige-architectuur voor gedeeld opslag.

## <a name="create-a-hyperscale-database"></a>Een grootschalige-data base maken

Een grootschalige-data base kan worden gemaakt met behulp van de [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [Power shell](/powershell/module/azurerm.sql/new-azurermsqldatabase)of [cli](/cli/azure/sql/db#az-sql-db-create). Grootschalige-data bases zijn alleen beschikbaar via het [op vCore gebaseerde aankoop model](service-tiers-vcore.md).

Met de volgende T-SQL-opdracht maakt u een grootschalige-data base. U moet zowel de editie als de service doelstelling opgeven in de `CREATE DATABASE` instructie. Raadpleeg de [resource limieten](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) voor een lijst met geldige service doelstellingen.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Hiermee maakt u een grootschalige-Data Base op GEN5-hardware met vier kernen.

## <a name="upgrade-existing-database-to-hyperscale"></a>Bestaande data base bijwerken naar grootschalige

U kunt uw bestaande data bases in Azure SQL Database verplaatsen naar grootschalige met behulp van de [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [Power shell](/powershell/module/azurerm.sql/set-azurermsqldatabase)of [cli](/cli/azure/sql/db#az-sql-db-update). Op dit moment is dit een eenrichtings migratie. U kunt geen data bases van grootschalige naar een andere servicelaag verplaatsen, met uitzonde ring van gegevens exporteren en importeren. Voor testen van concept (POCs) wordt u aangeraden een kopie te maken van uw productie databases en de kopie te migreren naar grootschalige. Het migreren van een bestaande data base in Azure SQL Database naar de grootschalige-laag is een grootte van de gegevens bewerking.

Met de volgende T-SQL-opdracht wordt een Data Base verplaatst naar de servicelaag grootschalige. U moet zowel de editie als de service doelstelling opgeven in de `ALTER DATABASE` instructie.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Verbinding maken met een lees bare replica van een grootschalige-data base

In grootschalige-data bases `ApplicationIntent` bepaalt het argument in de Connection String dat door de client wordt gegeven, of de verbinding wordt doorgestuurd naar de schrijf replica of naar een secundaire alleen-lezen replica. Als de `ApplicationIntent` set naar `READONLY` en de data base heeft geen secundaire replica, wordt de verbinding doorgestuurd naar de primaire replica en wordt de standaard `ReadWrite` instelling gebruikt.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Grootschalige secundaire replica's zijn allemaal identiek, met dezelfde serviceniveau doelstelling als de primaire replica. Als er meer dan één secundaire replica aanwezig is, wordt de werk belasting gedistribueerd over alle beschik bare zones. Elke secundaire replica wordt onafhankelijk bijgewerkt. Het is dus mogelijk dat verschillende replica's een andere gegevens latentie hebben ten opzichte van de primaire replica.

## <a name="database-high-availability-in-hyperscale"></a>Hoge Beschik baarheid van data base in grootschalige

Net als bij alle andere service lagen garandeert grootschalige gegevens duurzaamheid voor vastgelegde trans acties, ongeacht de beschik baarheid van reken replica's. De mate van uitval tijd, omdat de primaire replica niet beschikbaar is, is afhankelijk van het type failover (gepland versus niet-gepland) en op de aanwezigheid van ten minste één secundaire replica. In een geplande failover (d.w.z. een onderhouds gebeurtenis) maakt het systeem de nieuwe primaire replica voordat een failover wordt gestart, of wordt een bestaande secundaire replica als failover-doel gebruikt. In een niet-geplande failover (d.w.z. een hardwarestoring op de primaire replica), gebruikt het systeem een secundaire replica als failover-doel als er een bestaat, of maakt een nieuwe primaire replica van de groep beschik bare reken capaciteit. In het laatste geval is de duur van de downtime langer vanwege extra stappen die nodig zijn om de nieuwe primaire replica te maken.

Zie [Sla voor Azure SQL database](https://azure.microsoft.com/support/legal/sla/sql-database/)voor grootschalige sla.

## <a name="disaster-recovery-for-hyperscale-databases"></a>Herstel na nood geval voor grootschalige-data bases

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Een grootschalige-data base herstellen naar een andere regio

Als u een grootschalige-data base in Azure SQL Database moet herstellen naar een andere regio dan die die momenteel wordt gehost in, als onderdeel van een nood herstel bewerking of een analyse, herlocatie of een andere reden, is de primaire methode een geografisch herstel van de data base. Dit omvat precies dezelfde stappen als wat u zou gebruiken om een andere data base in SQL Database naar een andere regio te herstellen:

1. Maak een [Server](logical-servers.md) in de doel regio als u er nog geen geschikte server hebt.  Deze server moet eigendom zijn van hetzelfde abonnement als de oorspronkelijke server (bron).
2. Volg de instructies in het onderwerp [geo-Restore](./recovery-using-backups.md#geo-restore) van de pagina voor het herstellen van een data base in Azure SQL database van automatische back-ups.

> [!NOTE]
> Omdat de bron en het doel zich in verschillende regio's bevinden, kan de data base geen momentopname opslag delen met de bron database als in niet-geografische herstel bewerkingen, die snel worden voltooid, ongeacht de grootte van de data base. In het geval van een geo-Restore van een grootschalige-data base is het een grootte-of-Data-bewerking, zelfs als het doel zich in het gekoppelde gebied van de geo-gerepliceerde opslag bevindt. Een geo-herstel bewerking heeft daarom enige tijd in verhouding tot de grootte van de data base die wordt hersteld. Als het doel zich in het gekoppelde gebied bevindt, is de gegevens overdracht binnen een regio, die aanzienlijk sneller verloopt dan een gegevens overdracht voor meerdere regio's, maar dit is nog steeds een bewerking voor de grootte van gegevens.

## <a name="available-regions"></a><a name=regions></a>Beschikbare regio's

De laag Azure SQL Database grootschalige is beschikbaar in alle regio's, maar is standaard ingeschakeld in de volgende regio's die hieronder worden weer gegeven. Als u een grootschalige-Data Base wilt maken in een regio waarin grootschalige niet standaard is ingeschakeld, kunt u een aanvraag voor onboarding verzenden via Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](quota-increase-request.md) voor instructies voor instructies. Gebruik de volgende richt lijnen bij het verzenden van uw aanvraag:

- Gebruik het quotum type [toegang voor regio's](quota-increase-request.md#region) SQL database.
- Voeg in de beschrijving de reken-SKU/totaal kernen toe, inclusief Lees bare replica's, en geef aan dat u grootschalige-capaciteit aanvraagt.
- Geef ook een projectie op van de totale grootte van alle data bases in de loop van de tijd in TB.

Ingeschakelde regio's:
- Australië - oost
- Australië - zuidoost
- Australië - centraal
- Brazilië - zuid
- Canada - midden
- Canada - oost
- VS - centraal
- China - oost 2
- China - noord 2
- Azië - oost
- VS - oost
- VS-Oost 2
- Frankrijk - centraal
- Duitsland - west-centraal
- Japan - oost
- Japan - west
- Korea - centraal
- Korea - zuid
- VS - noord-centraal
- Europa - noord
- Noorwegen - oost
- Noorwegen - west
- Zuid-Afrika - noord
- VS - zuid-centraal
- Azië - zuidoost
- Zwitserland - west
- Verenigd Koninkrijk Zuid
- Verenigd Koninkrijk West
- US DoD Central
- US DoD East
- VS Govt Arizona
- VS Govt Texas
- VS - west-centraal
- Europa -west
- VS - west
- VS - west 2

## <a name="known-limitations"></a>Bekende beperkingen

Dit zijn de huidige beperkingen voor het grootschalige van de service tier.  We werken actief om zoveel mogelijk van deze beperkingen te verwijderen.

| Probleem | Beschrijving |
| :---- | :--------- |
| In het deel venster Back-ups beheren voor een server worden geen grootschalige-data bases weer gegeven. Deze worden gefilterd op basis van de weer gave.  | Grootschalige heeft een afzonderlijke methode voor het beheren van back-ups, zodat de Long-Term retentie en tijdstippen voor het bewaren van back-ups niet van toepassing zijn. Daarom worden grootschalige-data bases niet weer gegeven in het deel venster back-up beheren.<br><br>Voor data bases die zijn gemigreerd naar grootschalige van andere Azure SQL Database Service lagen, worden back-ups vóór de migratie bewaard voor de duur van de [back-](automated-backups-overview.md#backup-retention) upperiode van de bron database. Deze back-ups kunnen worden gebruikt om de bron database te [herstellen](recovery-using-backups.md#programmatic-recovery-using-automated-backups) naar een tijdstip vóór de migratie.|
| Terugzetten naar eerder tijdstip | Een niet-grootschalige-data base kan niet worden hersteld als een grootschalige-data base en een grootschalige-data base kan niet worden hersteld als een niet-grootschalige-data base. Voor een niet-grootschalige-data base die is gemigreerd naar grootschalige door de servicelaag te wijzigen, herstelt u een tijdstip voordat de migratie en binnen de Bewaar periode voor back-ups van de Data Base [programmatisch](recovery-using-backups.md#programmatic-recovery-using-automated-backups)worden ondersteund. De herstelde data base is niet-grootschalige. |
| Als Azure SQL Database servicelaag wijzigt in grootschalige, mislukt de bewerking als de data base gegevens bestanden heeft die groter zijn dan 1 TB | In sommige gevallen [is het mogelijk](file-space-manage.md#shrinking-data-files) om dit probleem te omzeilen door de grote bestanden kleiner te maken dan 1 TB voordat u probeert de servicelaag te wijzigen in grootschalige. Gebruik de volgende query om de huidige grootte van database bestanden te bepalen. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | Azure SQL Managed instance wordt momenteel niet ondersteund met grootschalige-data bases. |
| Elastische pools |  Elastische Pools worden momenteel niet ondersteund met grootschalige.|
| Migratie naar grootschalige is momenteel een eenrichtings bewerking | Wanneer een Data Base wordt gemigreerd naar grootschalige, kan deze niet rechtstreeks worden gemigreerd naar een niet-grootschalige. Op dit moment is de enige manier om een Data Base te migreren van grootschalige naar een niet-grootschalige, het exporteren/importeren met behulp van een Bacpac-bestand of andere technologieën voor gegevens verplaatsing (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS, enzovoort). Bacpac exporteren/importeren uit Azure Portal, vanuit Power shell met [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) of [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), vanuit Azure CLI met [AZ SQL DB export](/cli/azure/sql/db#az-sql-db-export) en [az SQL DB import](/cli/azure/sql/db#az-sql-db-import), en van [rest API](/rest/api/sql/) wordt niet ondersteund. Bacpac import/export voor kleinere grootschalige-data bases (tot 200 GB) wordt ondersteund met behulp van SSMS en [SqlPackage](/sql/tools/sqlpackage) versie 18,4 of hoger. Voor grotere data bases kan het maken van een Bacpac-export/-import enige tijd duren en kan om verschillende redenen mislukken.|
| Migratie van data bases met In-Memory OLTP-objecten | Grootschalige ondersteunt een subset van In-Memory OLTP-objecten, waaronder door het geheugen geoptimaliseerde tabel typen, tabel variabelen en systeem eigen, gecompileerde modules. Wanneer echter een soort In-Memory OLTP-objecten aanwezig zijn in de data base die wordt gemigreerd, wordt de migratie van Premium-en Bedrijfskritiek-service lagen naar grootschalige niet ondersteund. Als u een dergelijke Data Base wilt migreren naar grootschalige, moeten alle In-Memory OLTP-objecten en de bijbehorende afhankelijkheden worden verwijderd. Nadat de data base is gemigreerd, kunnen deze objecten opnieuw worden gemaakt. Duurzame en niet-duurzame tabellen die zijn geoptimaliseerd voor geheugen, worden momenteel niet ondersteund in grootschalige en moeten worden gewijzigd in schijf tabellen.|
| Geo-replicatie  | U kunt geo-replicatie voor Azure SQL Database grootschalige nog niet configureren. |
| Data base kopiëren | Het kopiëren van de Data Base op grootschalige is nu beschikbaar als open bare preview. |
| Intelligente database functies | Met uitzonde ring van de optie ' plan forceren ' worden alle andere opties voor automatisch afstemmen nog niet ondersteund op grootschalige: mogelijk lijkt het alsof de opties zijn ingeschakeld, maar zijn er geen aanbevelingen of acties gedaan. |
| Inzicht in queryprestaties | Query performance Insights wordt momenteel niet ondersteund voor grootschalige-data bases. |
| Data base verkleinen | DBCC SHRINKDATABASE of DBCC SHRINKFILE wordt momenteel niet ondersteund voor grootschalige-data bases. |
| Integriteits controle van data base | DBCC CHECKDB wordt momenteel niet ondersteund voor grootschalige-data bases. DBCC CHECKFILEGROUP en DBCC CHECKTABLE kunnen worden gebruikt als tijdelijke oplossing. Zie [gegevens integriteit in Azure SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) voor meer informatie over het beheer van gegevens integriteit in Azure SQL database. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Veelgestelde vragen over grootschalige](service-tier-hyperscale-frequently-asked-questions-faq.md)voor een veelgestelde vragen over grootschalige.
- Zie [service lagen](purchasing-models.md) voor meer informatie over service lagen.
- Zie [overzicht van resource limieten op een server](resource-limits-logical-server.md) voor informatie over limieten op het niveau van de server en het abonnement.
- Zie [Azure SQL database op vCore gebaseerde inkoop model limieten voor één data](resource-limits-vcore-single-databases.md)Base voor meer informatie over het aankoop model voor één data base.
- Zie [Veelvoorkomende SQL-functies](features-comparison.md) voor een lijst met functies en vergelijkingen.
