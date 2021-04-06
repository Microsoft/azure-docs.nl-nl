---
title: Automatische, geografisch redundante back-ups
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database en Azure SQL Managed instance maken om de paar minuten automatisch een lokale back-up van de data base en gebruiken geografisch redundante opslag met lees toegang voor geografische redundantie.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 03/10/2021
ms.openlocfilehash: 5879c9107a0ab5a2ef150d119e8b5ac8e16ac01d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609920"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatische back-ups-Azure SQL Database & SQL Managed instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Wat is een back-up van de data base?

Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat ze uw gegevens beschermen tegen beschadiging of verwijdering. Deze back-ups maken het herstellen van data bases mogelijk op een bepaald moment binnen de geconfigureerde Bewaar periode. Als uw regels voor gegevens bescherming vereisen dat uw back-ups langere tijd beschikbaar zijn (tot tien jaar), kunt u de [lange termijn retentie](long-term-retention-overview.md) voor zowel één als gepoolde data bases configureren.

### <a name="backup-frequency"></a>Back-upfrequentie

Zowel SQL Database als SQL Managed instance gebruiken SQL Server technologie om elke week [volledige back-ups](/sql/relational-databases/backup-restore/full-database-backups-sql-server) te maken, [differentiële back](/sql/relational-databases/backup-restore/differential-backups-sql-server) -ups elke 12-24 uur en [back-ups van transactie logboeken](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) om de vijf tot tien minuten. De frequentie van back-ups van transactie Logboeken is gebaseerd op de berekenings grootte en de hoeveelheid database activiteit.

Wanneer u een Data Base herstelt, bepaalt de service welke volledige, differentiële en transactie logboek back-ups moeten worden hersteld.

### <a name="backup-storage-redundancy"></a>Opslag redundantie van back-ups

SQL Database en SQL Managed instance slaan gegevens standaard op in geografisch redundante [opslag-blobs](../../storage/common/storage-redundancy.md) die worden gerepliceerd naar een [gekoppelde regio](../../best-practices-availability-paired-regions.md). Zo kunt u zich beschermen tegen storingen die van invloed zijn op de back-upopslag in de primaire regio en kunt u uw server herstellen naar een andere regio in het geval van een ramp. 

De optie voor het configureren van redundantie opslag voor back-ups biedt de flexibiliteit om te kiezen tussen lokaal redundante, zone redundante of geografisch redundante opslag-blobs voor een door SQL beheerd exemplaar of een SQL Database. Om ervoor te zorgen dat uw gegevens binnen dezelfde regio blijven waar uw beheerde instantie of SQL database wordt geïmplementeerd, kunt u de standaard geo-redundante opslag redundantie voor back-ups wijzigen en lokaal redundante of zone-redundante opslag-blobs voor back-ups configureren. Opslag redundantie mechanismen slaan meerdere kopieën van uw gegevens op, zodat deze beschermd zijn tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen of enorme natuur rampen. De geconfigureerde redundantie voor back-upopslag wordt toegepast op de instellingen voor retentie van back-ups op korte termijn die worden gebruikt voor herstel naar een bepaald tijdstip (PITR) en back-ups voor lange termijn retentie die worden gebruikt voor langdurige back-ups (LTR). 

Voor een SQL Database kan de redundantie van de back-upopslag worden geconfigureerd op het moment dat de data base wordt gemaakt of kan worden bijgewerkt voor een bestaande data base. de wijzigingen die zijn aangebracht in een bestaande data base, zijn alleen van toepassing op toekomstige back-ups. Nadat de redundantie van de back-upopslag van een bestaande data base is bijgewerkt, kan het tot 48 uur duren voordat de wijzigingen zijn toegepast. Houd er rekening mee dat geo Restore is uitgeschakeld zodra een Data Base is bijgewerkt voor het gebruik van lokale of zone redundante opslag. 


> [!IMPORTANT]
> De redundantie van back-upopslag configureren tijdens het proces voor het maken van een beheerd exemplaar als de resource is ingericht, is het niet meer mogelijk om de opslag redundantie te wijzigen. 

> [!IMPORTANT]
> Zone-redundante opslag is momenteel alleen beschikbaar in [bepaalde regio's](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> Configureer bare opslag redundantie van back-ups voor Azure SQL Database is momenteel beschikbaar in de preview-versie van Brazilië-zuid en algemeen beschikbaar in de Azure-regio Zuid-Azië. Deze functie is nog niet beschikbaar voor de grootschalige-laag. 

### <a name="backup-usage"></a>Back-upgebruik

U kunt deze back-ups gebruiken om:

- **Herstel naar een bepaald tijdstip van een bestaande data base**  -  [Een bestaande data base herstellen naar een bepaald tijdstip in het verleden](recovery-using-backups.md#point-in-time-restore) binnen de retentie periode met behulp van Azure Portal, Azure PowerShell, Azure CLI of rest API. Voor SQL Database maakt deze bewerking een nieuwe Data Base op dezelfde server als de oorspronkelijke Data Base, maar gebruikt een andere naam om te voor komen dat de oorspronkelijke Data Base wordt overschreven. Nadat het herstellen is voltooid, kunt u de oorspronkelijke data base verwijderen. U kunt ook de naam [van](/sql/relational-databases/databases/rename-a-database) de oorspronkelijke data base wijzigen en vervolgens de naam van de herstelde data base wijzigen in de oorspronkelijke data base. Op dezelfde manier maakt deze bewerking voor een SQL Managed instance een kopie van de Data Base op hetzelfde of een ander beheerd exemplaar in hetzelfde abonnement en dezelfde regio.
- **Herstel naar een bepaald tijdstip van een verwijderde data base**  -  [Een verwijderde data base herstellen naar het tijdstip van de verwijdering](recovery-using-backups.md#deleted-database-restore) of naar een bepaald tijdstip binnen de Bewaar periode. De verwijderde data base kan alleen worden teruggezet op een server of een beheerd exemplaar waarop de oorspronkelijke Data Base is gemaakt. Bij het verwijderen van een Data Base neemt de service een definitieve back-up van transactie logboeken voordat deze wordt verwijderd, om verlies van gegevens te voor komen.
- **Geo-herstel**  -  [Een Data Base herstellen naar een andere geografische regio](recovery-using-backups.md#geo-restore). Met geo-Restore kunt u een geografische nood situatie herstellen wanneer u geen toegang hebt tot uw data base of back-ups in de primaire regio. Er wordt in elke Azure-regio een nieuwe data base gemaakt op een bestaande server of een beheerd exemplaar.
   > [!IMPORTANT]
   > Geo-Restore is alleen beschikbaar voor SQL-data bases of beheerde exemplaren die zijn geconfigureerd met geografisch redundante back-upopslag.
- **Herstellen vanaf back-up**  -  op lange termijn [Een Data Base herstellen op basis van een specifieke lange termijn back-up](long-term-retention-overview.md) van een enkele data base of gepoolde data base, als de data base is geconfigureerd met een lange termijn retentie beleid (LTR). Met LTR kunt u een oude versie van de data base herstellen met behulp van [de Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) of [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) om te voldoen aan een nalevings aanvraag of een oude versie van de toepassing uit te voeren. Zie [Langetermijnretentie](long-term-retention-overview.md) voor meer informatie.

Zie [Data Base terugzetten van back-ups](recovery-using-backups.md)om een herstel bewerking uit te voeren.

> [!NOTE]
> In Azure Storage verwijst de term *replicatie* naar het kopiëren van blobs van de ene locatie naar een andere. In SQL verwijst *database replicatie* naar verschillende technologieën om te voor komen dat meerdere secundaire data bases worden gesynchroniseerd met een primaire data base.

U kunt back-ups van de configuratie-en herstel bewerkingen proberen met de volgende voor beelden:

| Bewerking | Azure Portal | Azure PowerShell |
|---|---|---|
| **Retentie van back-ups wijzigen** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Managed Instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Managed Instance](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Lange termijn retentie van back-ups wijzigen** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Door SQL beheerd exemplaar-N.v.t.  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **Een Data Base vanaf een bepaald moment herstellen** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL Database](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Een verwijderde database herstellen** | [SQL Database](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Een Data Base herstellen vanuit Azure Blob-opslag** | SQL Database-N.v.t. <br/>Door SQL beheerd exemplaar-N.v.t.  | SQL Database-N.v.t. <br/>[SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Back-upplanning

De eerste volledige back-up wordt direct na het maken of herstellen van een nieuwe data base gepland. Deze back-up wordt gewoonlijk binnen 30 minuten voltooid, maar kan langer duren als de data base groot is. De eerste back-up kan bijvoorbeeld langer duren op een herstelde data base of een kopie van een Data Base die normaal gesp roken groter is dan een nieuwe data base. Na de eerste volledige back-up worden alle verdere back-ups automatisch gepland en beheerd. De exacte timing van alle database back-ups wordt bepaald door de SQL Database of de SQL Managed instance service, omdat deze de algehele systeem werk belasting evenwichtig benadert. U kunt het schema van back-uptaken niet wijzigen of uitschakelen.

> [!IMPORTANT]
> Voor een nieuwe, herstelde of gekopieerde data base wordt herstel naar een bepaald tijdstip beschikbaar vanaf het moment dat de eerste back-up van het transactie logboek dat volgt, de eerste volledige back-up wordt gemaakt.

## <a name="backup-storage-consumption"></a>Opslag gebruik back-ups

Met SQL Server-technologie voor back-up en herstel is het herstellen van een Data Base naar een bepaald tijdstip een niet-onderbroken back-upketen die bestaat uit één volledige back-up, eventueel een differentiële back-up en een of meer back-ups van transactie Logboeken. Het back-upschema van SQL Database en SQL Managed instance bevat een volledige back-up elke week. Daarom moet het systeem, om PITR binnen de gehele Bewaar periode in te scha kelen, extra volledige, differentiële en transactie logboek back-ups opslaan gedurende een periode die langer is dan de geconfigureerde Bewaar periode. 

Met andere woorden: voor elk tijdstip tijdens de Bewaar periode moet er een volledige back-up worden gemaakt die ouder is dan de oudste tijd van de retentie periode, evenals een ononderbroken keten van differentiële en transactie logboek back-ups van die volledige back-up tot de volgende volledige back-up.

> [!NOTE]
> Om PITR in te scha kelen, worden extra back-ups gedurende een week langer opgeslagen dan de geconfigureerde Bewaar periode. Back-upopslag wordt in rekening gebracht tegen hetzelfde tarief voor alle back-ups. 

Back-ups die niet meer nodig zijn om de PITR-functionaliteit te bieden, worden automatisch verwijderd. Omdat voor differentiële back-ups en logboek back-ups een eerdere volledige back-up kan worden herstorable, worden alle drie de back-uptypen samen in wekelijkse sets opgeschoond.

Voor alle data bases, inclusief [TDe versleutelde](transparent-data-encryption-tde-overview.md) data bases, worden back-ups gecomprimeerd om compressie van back-upopslag en kosten te verminderen. De gemiddelde compressie ratio van back-ups is 3-4 keer, maar dit kan aanzienlijk lager of hoger zijn, afhankelijk van de aard van de gegevens en of gegevens compressie wordt gebruikt in de-data base.

SQL Database en SQL Managed instance berekenen de totale gebruikte back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te berekenen. Nadat de data base is verwijderd, neemt het verbruik af naarmate back-ups worden verwijderd. Zodra alle back-ups zijn verwijderd en PITR niet meer mogelijk is, wordt de facturering gestopt.
   
> [!IMPORTANT]
> Back-ups van een Data Base worden bewaard om PITR in te scha kelen, zelfs als de data base is verwijderd. Tijdens het verwijderen en opnieuw maken van een Data Base kunnen opslag-en reken kosten worden bespaard, kunnen de kosten voor back-upopslag toenemen omdat de service back-ups voor elke verwijderde data base behoudt, telkens wanneer deze wordt verwijderd. 

### <a name="monitor-consumption"></a>Verbruik bewaken

Voor vCore-data bases wordt de opslag die wordt gebruikt door elk type back-up (volledig, differentieel en logboek) op de Blade database bewaking gerapporteerd als een afzonderlijke metriek. In het volgende diagram ziet u hoe u het verbruik van back-upopslag voor één data base bewaakt. Deze functie is momenteel niet beschikbaar voor beheerde exemplaren.

![Gebruik van database back-ups in de Azure Portal bewaken](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Het gebruik van back-upopslag nauw keuriger instellen

Voor het verbruik van back-upopslag tot de maximale gegevens grootte voor een Data Base worden geen kosten in rekening gebracht. Het verbruik van het back-upopslag is afhankelijk van de werk belasting en de maximale grootte van de afzonderlijke data bases. Bekijk een aantal van de volgende afstemmings technieken om het verbruik van back-upopslag te verminderen:

- Verminder de [Bewaar periode voor back-ups](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) naar de minimale vereisten voor uw behoeften.
- Vermijd grote schrijf bewerkingen, zoals het opnieuw opbouwen van indexen, vaker dan u nodig hebt.
- Overweeg het gebruik van [geclusterde column Store-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview) en de volgende gerelateerde [Aanbevolen procedures](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance), en/of verklein het aantal niet-geclusterde indexen voor bewerkingen voor grote gegevens belasting.
- In de servicelaag Algemeen is de ingerichte gegevens opslag minder duur dan de prijs van de back-upopslag. Als u voortdurend veel overtollige back-upopslagkosten hebt, kunt u overwegen gegevens opslag te verg Roten om op te slaan in de back-upopslag.
- Gebruik TempDB in plaats van permanente tabellen in uw toepassings logica om tijdelijke resultaten en/of tijdelijke gegevens op te slaan.
- Gebruik waar mogelijk lokaal redundante back-upopslag (bijvoorbeeld ontwikkel-en test omgevingen)

## <a name="backup-retention"></a>Back-upretentie

Voor alle nieuwe, herstelde en gekopieerde data bases Azure SQL Database en Azure SQL Managed instance voldoende back-ups behouden zodat PITR in de afgelopen 7 dagen standaard is toegestaan. Met uitzonde ring van grootschalige-en Basic-laag databases, kunt u de [Bewaar periode voor back-ups wijzigen](#change-the-pitr-backup-retention-period) per actieve data base in het bereik van 1-35 dagen. Zoals beschreven in [back-upopslag](#backup-storage-consumption), is het mogelijk dat back-ups die zijn opgeslagen om PITR in te scha kelen, ouder zijn dan de retentie periode. Alleen voor Azure SQL Managed instance is het mogelijk om de retentie frequentie van PITR-back-ups in te stellen wanneer een Data Base binnen het bereik van 0-35 dagen is verwijderd. 

Als u een Data Base verwijdert, houdt het systeem back-ups op dezelfde manier als voor een online-data base met de specifieke Bewaar periode. U kunt de Bewaar periode voor back-ups niet wijzigen voor een verwijderde data base.

> [!IMPORTANT]
> Als u een server of een beheerd exemplaar verwijdert, worden ook alle data bases op die server of het beheerde exemplaar verwijderd en kunnen deze niet worden hersteld. U kunt een verwijderde server of een beheerd exemplaar niet herstellen. Maar als u wel lange termijn retentie (LTR) hebt geconfigureerd voor een Data Base of een beheerd exemplaar, worden back-ups voor lange termijn retentie niet verwijderd en kunnen ze worden gebruikt voor het herstellen van data bases op een andere server of een beheerd exemplaar in hetzelfde abonnement, tot een tijdstip waarop een back-up met lange termijn retentie is gemaakt.

De retentie van back-ups voor PITR in de afgelopen 1-35 dagen wordt soms een retentie voor de korte termijn voor back-ups genoemd. Als u back-ups langer wilt bewaren dan de maximale Bewaar periode voor de korte termijn van 35 dagen, kunt u de [lange termijn retentie](long-term-retention-overview.md)inschakelen.

### <a name="long-term-retention"></a>Lange bewaartermijn

Voor zowel SQL Database als SQL Managed Instance kunt u een volledige back-up voor de lange termijn (LTR) voor de hele periode configureren in Azure Blob-opslag. Nadat het LTR-beleid is geconfigureerd, worden volledige back-ups automatisch wekelijks naar een andere opslag container gekopieerd. Om aan verschillende nalevings vereisten te voldoen, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse volledige back-ups. Het opslag verbruik is afhankelijk van de geselecteerde frequentie en de retentie perioden van V.L.N.R.-back-ups. U kunt de [LTR-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) gebruiken om de kosten van v.l.n.r.-opslag te schatten.

> [!IMPORTANT]
> Het bijwerken van de redundantie van de back-upopslag voor een bestaande Azure SQL Database is alleen van toepassing op de toekomstige back-ups die zijn gemaakt voor de data base. Alle bestaande LTR-back-ups voor de data base blijven aanwezig in de bestaande opslag-Blob en er worden nieuwe back-ups opgeslagen op het aangevraagde type opslag-blob. 

Voor meer informatie over LTR, Zie [lange termijn retentie van back-ups](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Kosten voor back-upopslag

De prijs voor back-upopslag varieert en is afhankelijk van uw aankoop model (DTU of vCore), de gekozen optie voor opslag redundantie van back-ups en ook in uw regio. De back-upopslag wordt in rekening gebracht per GB/maand verbruikt voor prijzen Zie [Azure SQL database prijs](https://azure.microsoft.com/pricing/details/sql-database/single/) pagina en [prijs pagina Azure SQL Managed instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

> [!NOTE]
> In azure factuur wordt alleen de overtollige back-upopslag weer gegeven, niet het volledige verbruik van back-upopslag. Als u bijvoorbeeld in een hypothetisch scenario hebt ingericht 4 TB van gegevens opslag, krijgt u 4 TB aan beschik bare opslag ruimte voor back-ups. Als u de totale hoeveelheid 5,8 TB aan opslag ruimte voor back-ups hebt gebruikt, wordt in azure factuur slechts 1,8 TB weer gegeven, omdat alleen overtollige back-upopslag wordt gefactureerd.

### <a name="dtu-model"></a>DTU-model

In het DTU-model worden geen extra kosten in rekening gebracht voor back-upopslag voor data bases en elastische Pools. De prijs van back-upopslag is onderdeel van de prijs van de data base of groep.

### <a name="vcore-model"></a>vCore-model

Voor afzonderlijke data bases in SQL Database is een back-upopslagwaarde gelijk aan 100 procent van de maximale grootte van de gegevens opslag voor de data base, zonder extra kosten. Voor elastische Pools en beheerde instanties is een back-upopslagwaarde gelijk aan 100 procent van de maximale gegevens opslag voor de pool of de maximale grootte van het exemplaar van de opslag, respectievelijk, zonder extra kosten. 

Voor afzonderlijke data bases wordt deze vergelijking gebruikt voor het berekenen van het totale factureer bare gebruik van back-ups:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Voor gegroepeerde Data bases wordt de totale factureer bare opslag grootte geaggregeerd op het groeps niveau en wordt als volgt berekend:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Voor beheerde instanties wordt de totale factureer bare opslag grootte op het exemplaar niveau geaggregeerd en als volgt berekend:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

De totale factureer bare back-upopslag wordt in GB per maand in rekening gebracht op basis van de frequentie van de gebruikte back-upopslag-redundantie. Dit verbruik van back-upopslag is afhankelijk van de werk belasting en de grootte van afzonderlijke data bases, elastische Pools en beheerde exemplaren. Sterk gewijzigde data bases hebben meer differentiële en logboek back-ups, omdat de grootte van deze back-ups evenredig is met de hoeveelheid gegevens wijzigingen. Daarom zullen dergelijke data bases hogere back-upkosten hebben.

SQL Database en SQL Managed instance berekent uw totale factureer bare back-upopslag als een cumulatieve waarde voor alle back-upbestanden. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, waarmee dit uur gebruik wordt geaggregeerd om het verbruik van back-upopslag aan het einde van elke maand te verkrijgen. Als een Data Base wordt verwijderd, neemt het verbruik van back-upopslag geleidelijk af naarmate oudere back-ups worden verwijderd. Omdat voor differentiële back-ups en logboek back-ups een eerdere volledige back-up kan worden herstorable, worden alle drie de back-uptypen samen in wekelijkse sets opgeschoond. Zodra alle back-ups zijn verwijderd, wordt de facturering gestopt. 

Als een vereenvoudigd voor beeld wordt ervan uitgegaan dat een Data Base 744 GB back-upopslag heeft gecumuleerd en dat deze hoeveelheid gedurende een hele maand constant blijft, omdat de data base volledig niet actief is. Als u dit cumulatieve opslag gebruik wilt converteren naar het uurverbruik, deelt u dit op 744,0 (31 dagen per maand * 24 uur per dag). SQL Database meldt zich aan de Azure-facturerings pijplijn dat de data base elk uur 1 GB PITR-back-up heeft verbruikt, tegen een constant tarief. In azure billing wordt dit verbruik geaggregeerd en wordt een gebruik van 744 GB voor de hele maand weer gegeven. De kosten worden berekend op basis van het percentage van de hoeveelheid/GB per maand in uw regio.

Nu is een complexere voor beeld. Stel dat voor dezelfde niet-actieve Data Base de Bewaar periode van 7 dagen tot 14 dagen in het midden van de maand is verhoogd. Dit verhoogt de resultaten van de totale back-upopslag, verdubbeld tot 1.488 GB. SQL Database zou 1 GB aan gebruik melden voor uren 1 tot en met 372 (de eerste helft van de maand). Hiermee wordt het gebruik gerapporteerd als 2 GB voor uren 373 tot 744 (de tweede helft van de maand). Dit gebruik wordt geaggregeerd naar een voltooide factuur van 1.116 GB/maand.

De daad werkelijke facturerings scenario's voor back-ups zijn complexer. Omdat de frequentie van de wijzigingen in de data base afhankelijk is van de werk belasting en de variabele gedurende een bepaalde periode, is de grootte van elke differentiële back-up en logboek registratie ook afhankelijk, waardoor het verbruik van de back-upopslag per uur dienovereenkomstig wordt geschommeld. Daarnaast bevat elke differentiële back-up alle wijzigingen die zijn aangebracht in de data base sinds de laatste volledige back-up, waardoor de totale grootte van alle differentiële back-ups geleidelijk in de loop van een week toeneemt en vervolgens weer wordt versneld als er een oudere set van volledige, differentiële en logboek back-ups wordt weer gegeven. Als er bijvoorbeeld een zware schrijf activiteit zoals het opnieuw opbouwen van de index is uitgevoerd nadat een volledige back-up is voltooid, worden de wijzigingen die zijn aangebracht door het opnieuw opbouwen van de index opgenomen in de back-ups van de transactie logboeken die zijn gemaakt tijdens het opnieuw opbouwen, in de volgende differentiële back-up en in elke differentiële back-up die tot de volgende volledige back-up wordt Voor het laatste scenario in grotere data bases maakt een optimalisatie in de service een volledige back-up in plaats van een differentiële back-up als een differentiële back-up uitzonderlijk groot zou zijn. Dit beperkt de grootte van alle differentiële back-ups tot de volgende volledige back-up.

U kunt het totale opslag verbruik van back-ups voor elk back-uptype (volledig, Differentieel, transactie logboek) in de loop van de tijd bewaken, zoals beschreven in het [monitor gebruik](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Opslag redundantie van back-ups

Opslag redundantie van back-ups heeft gevolgen voor back-upkosten op de volgende manier:
- lokaal redundante prijs = x
- zone-redundante prijs = 1,25 x
- geografisch redundante prijs = 2x

Ga voor meer informatie over prijzen voor back-upopslag naar [Azure SQL database prijs pagina](https://azure.microsoft.com/pricing/details/sql-database/single/) en de [pagina met prijzen voor Azure SQL Managed instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Configureer bare back-upopslag redundantie voor een SQL Managed instance is beschikbaar in alle Azure-regio's en is momenteel alleen beschikbaar in Zuidoost-Azië Azure-regio voor SQL Database. Voor een beheerd exemplaar kan dit alleen worden opgegeven tijdens het proces voor het maken van een beheerd exemplaar. Zodra de resource is ingericht, kunt u de optie voor opslag redundantie van back-ups niet meer wijzigen.

### <a name="monitor-costs"></a>Kosten bewaken

Als u meer wilt weten over de kosten voor back-upopslag, gaat u naar **Cost Management + facturering** in de Azure Portal, selecteert u **Cost Management** en selecteert u vervolgens **kosten analyse**. Selecteer het gewenste abonnement als **bereik** en filter vervolgens op de periode en service waarop u bent geïnteresseerd.

Voeg een filter toe voor **service naam** en selecteer vervolgens **SQL data base** in de vervolg keuzelijst. Gebruik het filter **meter subcategorie** om de facturerings teller voor uw service te kiezen. Selecteer voor één data base of een pool voor elastische Data Base **één/elastische pool PITR back-upopslag**. Voor een beheerd exemplaar selecteert u **mi PITR Backup Storage**. De subcategorieën voor **opslag** en **berekening** kunnen u ook interesseren, maar ze zijn niet gekoppeld aan back-upopslagkosten.

![Kosten analyse back-upopslag](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Meters zijn alleen zichtbaar voor prestatie meter items die momenteel in gebruik zijn. Als een item niet beschikbaar is, is het waarschijnlijk dat de categorie momenteel niet wordt gebruikt. Managed instance Counters is bijvoorbeeld niet aanwezig voor klanten die geen beheerd exemplaar hebben geïmplementeerd. Opslag tellers zijn ook niet zichtbaar voor bronnen die geen opslag ruimte gebruiken. 

## <a name="encrypted-backups"></a>Versleutelde back-ups

Als uw data base is versleuteld met TDE, worden back-ups automatisch versleuteld op rest, waaronder LTR-back-ups. Alle nieuwe data bases in Azure SQL worden geconfigureerd met TDE standaard ingeschakeld. Zie  [transparent Data Encryption met SQL Database & Managed instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie over TDe.

## <a name="backup-integrity"></a>Back-upintegriteit

Het Azure SQL engineering-team test automatisch het herstellen van automatische database back-ups. (Deze test is momenteel niet beschikbaar in het SQL Managed instance.) Bij herstel naar een bepaald tijdstip ontvangen data bases ook DBCC CHECKDB-integriteits controles.

Problemen die tijdens de integriteits controle worden gevonden, zullen leiden tot een waarschuwing voor het technische team. Zie [gegevens integriteit in SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)voor meer informatie.

Alle database back-ups worden gemaakt met de CONTROLESOM optie om extra back-upintegriteit te bieden.

## <a name="compliance"></a>Naleving

Wanneer u uw data base migreert van een service tier op basis van DTU naar een vCore service tier, blijft de retentie van de PITR bewaard om ervoor te zorgen dat het gegevens herstel beleid van uw toepassing niet wordt aangetast. Als de standaard retentie niet voldoet aan uw nalevings vereisten, kunt u de retentie periode van PITR wijzigen. Zie [de retentie periode voor PITR-back-ups wijzigen](#change-the-pitr-backup-retention-period)voor meer informatie.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>De retentie periode voor PITR-back-ups wijzigen

U kunt de standaard retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal, Power shell of de REST API. In de volgende voor beelden ziet u hoe u de retentie van PITR wijzigt in 28 dagen.

> [!WARNING]
> Als u de huidige retentie periode verlaagt, verliest u de mogelijkheid om te herstellen naar punten die ouder zijn dan de nieuwe Bewaar periode. Back-ups die niet meer nodig zijn voor het leveren van PITR binnen de nieuwe Bewaar periode, worden verwijderd. Als u de huidige retentie periode verhoogt, hebt u niet onmiddellijk de mogelijkheid om binnen de nieuwe Bewaar periode naar oudere tijdstippen te herstellen. U krijgt deze mogelijkheid na verloop van tijd, naarmate het systeem meer back-ups gaat bewaren.

> [!NOTE]
> Deze Api's zijn alleen van invloed op de PITR-retentie periode. Als u LTR hebt geconfigureerd voor uw data base, heeft dit geen invloed op dit probleem. Zie [lange termijn retentie](long-term-retention-overview.md)voor informatie over het wijzigen van v.l.n.r.-Bewaar perioden.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal

Als u de retentie periode voor PITR voor actieve data bases met behulp van de Azure Portal wilt wijzigen, gaat u naar de server of het beheerde exemplaar met de data bases waarvan u de Bewaar periode wilt wijzigen. Selecteer **back-ups** in het linkerdeel venster en selecteer vervolgens het tabblad **Bewaar beleid** . Selecteer de data base (s) waarvoor u de PITR-back-upbewaaring wilt wijzigen. Selecteer vervolgens **Bewaar periode configureren** op de actie balk.



#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

![PITR-retentie, server niveau wijzigen](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

![PITR-retentie, beheerd exemplaar wijzigen](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell AzureRM-module wordt nog steeds ondersteund door SQL Database en SQL Managed instance, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](/powershell/module/AzureRM.Sql/)voor meer informatie. De argumenten voor de opdrachten in de module AZ zijn vrijwel identiek aan die in de AzureRm-modules.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Gebruik het volgende Power shell-voor beeld om de PITR-retentie voor actieve Azure SQL-data bases te wijzigen.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Gebruik het volgende Power shell-voor beeld om de PITR-back-up te wijzigen voor een **afzonderlijke actieve** SQL Managed instance-data base.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Gebruik het volgende Power shell-voor beeld om de PITR-retentie voor **alle actieve** SQL Managed instance-data bases te wijzigen.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Gebruik het volgende Power shell-voor beeld om de retentie van de PITR voor een **afzonderlijke verwijderde** SQL Managed instance data base te wijzigen.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Gebruik het volgende Power shell-voor beeld om de retentie van de PITR voor **alle verwijderde** data bases van SQL Managed instances te wijzigen.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Bij een Bewaar periode van nul (0) wordt aangegeven dat de back-up onmiddellijk wordt verwijderd en niet langer wordt bewaard voor een verwijderde data base.
Zodra PITR back-up is gereduceerd voor een verwijderde data base, kan deze niet meer worden verhoogd.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de REST API

#### <a name="sample-request"></a>Voorbeeld aanvraag

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Voorbeeldantwoord

Status code: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Zie [retentie van back-ups rest API](/rest/api/sql/backupshorttermretentionpolicies)voor meer informatie.

#### <a name="sample-request"></a>Voorbeeld aanvraag

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Voorbeeldantwoord

Status code: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Zie [retentie van back-ups rest API](/rest/api/sql/backupshorttermretentionpolicies)voor meer informatie.

## <a name="configure-backup-storage-redundancy"></a>Opslag redundantie voor back-ups configureren

> [!NOTE]
> Configureer bare opslag redundantie voor back-ups voor SQL Managed instance kan alleen worden opgegeven tijdens het proces voor het maken van een beheerd exemplaar. Zodra de resource is ingericht, kunt u de optie voor opslag redundantie van back-ups niet meer wijzigen. Voor SQL Database is de open bare preview van deze functie momenteel beschikbaar in Brazilië-zuid en is deze algemeen beschikbaar in de Azure-regio Zuidoost-Azië. 

Een opslag redundantie van een back-up van een beheerd exemplaar kan worden ingesteld tijdens het maken van een exemplaar. Voor een SQL Database kan deze worden ingesteld bij het maken van de data base of kan worden bijgewerkt voor een bestaande data base. De standaard waarde is geografisch redundante opslag. Bezoek de [pagina met prijzen voor Managed instances](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)voor verschillen in de prijs tussen lokaal redundante, zone-redundante en geografisch redundante back-upopslag.

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Opslag redundantie voor back-ups configureren met behulp van de Azure Portal

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

In Azure Portal kunt u de redundantie opslag voor back-ups configureren op de Blade **SQL database maken** . Deze optie is beschikbaar in de sectie opslag redundantie van back-ups. 
![SQL Database Blade maken openen](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

In de Azure Portal, de optie voor het wijzigen van de redundantie van back-upopslag bevindt zich op de Blade **Compute + Storage** die toegankelijk is via de optie **Managed instance configureren** op het tabblad **basis beginselen** wanneer u uw SQL Managed instance maakt.
![Berekenings-en opslag configuratie openen-Blade](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Zoek de optie voor het selecteren van back-upopslag redundantie op de Blade **reken kracht en opslag** .
![Opslag redundantie voor back-ups configureren](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Opslag redundantie voor back-ups configureren met behulp van Power shell

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Als u de redundantie van back-upopslag wilt configureren bij het maken van een nieuwe Data Base, kunt u de para meter-BackupStoageRedundancy opgeven. Mogelijke waarden zijn geo, zone en local. Standaard gebruiken alle SQL-data bases geo-redundante opslag voor back-ups. Geo Restore is uitgeschakeld als er een Data Base wordt gemaakt met een lokale of zone redundante back-upopslag. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Ga voor meer informatie naar [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Als u de redundantie van back-upopslag van een bestaande Data Base wilt bijwerken, kunt u de para meter-BackupStorageRedundancy gebruiken. Mogelijke waarden zijn geo, zone en local.
Houd er rekening mee dat het tot 48 uur kan duren voordat de wijzigingen op de Data Base worden toegepast. Als u overschakelt van geografisch redundante back-upopslag naar lokale of zone redundante opslag, wordt geo Restore uitgeschakeld. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Ga voor meer informatie naar [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> Als u de para meter-BackupStorageRedundancy wilt gebruiken bij het herstellen van de data base, het kopiëren van de data base of het maken van secundaire bewerkingen, gebruikt u Azure PowerShell versie AZ. SQL 


#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Voor het configureren van redundantie van back-upopslag tijdens het maken van een beheerd exemplaar kunt u de para meter-BackupStoageRedundancy opgeven. Mogelijke waarden zijn geo, zone en local.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Ga voor meer informatie naar [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Azure Policy gebruiken om redundantie van back-upopslag af te dwingen

Als u gegevens locatie vereisten hebt waarbij u al uw gegevens in één Azure-regio moet houden, wilt u mogelijk zone redundante of lokaal redundante back-ups voor uw SQL Database of beheerde instantie afdwingen met behulp van Azure Policy. Azure Policy is een service die u kunt gebruiken om beleid te maken, toe te wijzen en te beheren waarmee regels worden toegepast op Azure-resources. Azure Policy helpt u bij het houden van deze resources die voldoen aan uw bedrijfs normen en service overeenkomsten. Zie [Overzicht van Azure Policy](../../governance/policy/overview.md) voor meer informatie. 

### <a name="built-in-backup-storage-redundancy-policies"></a>Ingebouwde beleids regels voor back-upopslag 

Volgende nieuwe ingebouwde beleids regels worden toegevoegd, die kunnen worden toegewezen op het niveau van het abonnement of de resource groep om het maken van nieuwe data base (s) of exemplaren met geografisch redundante back-upopslag te blok keren. 

[SQL Database moet het gebruik van GRS-back-up-redundantie voorkomen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[Door SQL beheerde instanties moeten het gebruik van GRS-back-up-redundantie voorkomen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

[Hier](./policy-reference.md)vindt u een volledige lijst met ingebouwde beleids definities voor de SQL database en het beheerde exemplaar.

Voor het afdwingen van vereisten voor gegevens locatie op een organisatie niveau, kunnen deze beleids regels worden toegewezen aan een abonnement. Nadat deze zijn toegewezen op abonnements niveau, kunnen gebruikers in het gegeven abonnement geen data base of een beheerd exemplaar maken met geografisch redundante back-upopslag via Azure Portal of Azure PowerShell. 

> [!IMPORTANT]
> Azure-beleid wordt niet afgedwongen bij het maken van een Data Base via T-SQL. Als u gegevens locatie wilt afdwingen bij het maken van een Data Base met behulp van T-SQL, [gebruikt u ' local ' of ' zone ' als invoer voor BACKUP_STORAGE_REDUNDANCY para meter in de instructie Create Data Base](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

Meer informatie over het toewijzen van beleid met behulp van de [Azure Portal](../../governance/policy/assign-policy-portal.md) of [Azure PowerShell](../../governance/policy/assign-policy-powershell.md)


## <a name="next-steps"></a>Volgende stappen

- Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat uw gegevens worden beschermd tegen onbedoelde beschadiging of verwijdering. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie over de andere SQL database oplossingen voor bedrijfs continuïteit.
- Meer informatie over [het herstellen van een Data Base naar een bepaald tijdstip met behulp van de Azure Portal](recovery-using-backups.md).
- Meer informatie over het herstellen van [een Data Base naar een bepaald tijdstip met behulp van Power shell](scripts/restore-database-powershell.md).
- Voor informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van de Azure Portal, Zie [lange termijn retentie van back-ups beheren met behulp van de Azure Portal](long-term-backup-retention-configure.md).
- Zie [lange termijn retentie van back-ups beheren met Power shell](long-term-backup-retention-configure.md)voor meer informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van Power shell.
- Zie [back-upopslag verbruik op beheerde instantie wordt uitgelegd](https://aka.ms/mi-backup-explained)voor meer informatie over het gebruik van back-upopslag in Azure SQL Managed instance.
- Zie voor meer informatie over het afstemmen van back-upopslag en-kosten voor Azure SQL Managed instance de [kosten voor back-upopslag verfijnen op een beheerd exemplaar](https://aka.ms/mi-backup-tuning).
