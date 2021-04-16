---
title: Serverloze compute-laag
description: In dit artikel wordt de nieuwe serverloze rekenlaag beschreven en vergeleken met de bestaande inrichtende rekenlaag voor Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/15/2021
ms.openlocfilehash: ea9d5a5c39bf73ede2391c586f09dd95ff79b63c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531974"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database serverloos maken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Serverloos is een compute-laag voor afzonderlijke databases in Azure SQL Database waarmee de compute-resources automatisch worden geschaald op basis van de eisen van de workload en er wordt gefactureerd op basis van de hoeveelheid rekenkracht die per seconde wordt gebruikt. De serverloze compute-laag pauzeert databases ook automatisch tijdens inactieve perioden, zodat er alleen opslag wordt gefactureerd, en hervat databases automatisch wanneer er weer activiteit plaatsvindt.

## <a name="serverless-compute-tier"></a>Serverloze compute-laag

De serverloze rekenlaag voor individuele databases in Azure SQL Database wordt geparameteriseerd door een bereik voor automatisch schalen en een vertraging voor automatisch onderbreken. De configuratie van deze parameters vormt de ervaring voor databaseprestaties en de rekenkosten.

![serverloze facturering](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Prestatieconfiguratie

- De **minimale vCores en** maximum **vCores** zijn configureerbare parameters die het bereik van de rekencapaciteit definiëren dat beschikbaar is voor de database. Geheugen- en I/O-limieten zijn evenredig met het opgegeven vCore-bereik.  
- De **vertraging voor automatisch onderbreken** is een configureerbare parameter die de periode definieert waarin de database inactief moet zijn voordat deze automatisch wordt onderbroken. De database wordt automatisch hervat wanneer de volgende aanmelding of andere activiteit plaatsvindt.  Automatisch onderbreken kan ook worden uitgeschakeld.

### <a name="cost"></a>Kosten

- De kosten voor een serverloze database zijn de som van de rekenkosten en opslagkosten.
- Wanneer het rekengebruik ligt tussen de geconfigureerde minimum- en maximumlimieten, worden de rekenkosten gebaseerd op de gebruikte vCore en het geheugen.
- Wanneer het rekengebruik lager is dan de geconfigureerde minimumlimieten, worden de rekenkosten gebaseerd op de geconfigureerde minimum aantal vCores en min. geheugen.
- Wanneer de database is onderbroken, zijn de rekenkosten nul en worden alleen opslagkosten gemaakt.
- De opslagkosten worden op dezelfde manier bepaald als in de inrichtende rekenlaag.

Zie Facturering voor meer [kostendetails.](serverless-tier-overview.md#billing)

## <a name="scenarios"></a>Scenario's

Serverloos is geoptimaliseerd in prijs-prestatieverhouding voor individuele databases met periodieke, onvoorspelbare gebruikspatronen waarbij lichte vertraging bij het maken van berekeningen na een periode van weinig gebruik geen probleem is. De ingerichte rekenlaag is daarentegen geoptimaliseerd in prijs-prestatieverhouding voor individuele databases of meerdere databases in elastische pools met een hoger gemiddeld gebruik dat geen vertraging kan ondervinden bij het maken van berekeningen.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenario's die geschikt zijn voor serverloze computing

- Individuele databases met onregelmatige, onvoorspelbare gebruikspatronen die worden afgewisseld met perioden van inactiviteit en een lager gemiddeld rekengebruik gedurende een bepaalde periode.
- Individuele databases in de inrichtende rekenlaag die vaak opnieuw worden geschaald en klanten die de voorkeur geven aan het delegeren van opnieuw schalen van rekenkracht aan de service.
- Nieuwe individuele databases zonder gebruiksgeschiedenis waarbij de rekenkracht moeilijk of niet kan worden schatten vóór de implementatie in SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenario's die geschikt zijn voor inrichtende rekenkracht

- Individuele databases met meer regelmatige, voorspelbare gebruikspatronen en een hoger gemiddeld rekengebruik gedurende een bepaalde periode.
- Databases die geen prestatie-afwegingen kunnen tolereren die het gevolg zijn van frequentere geheugeninkorting of vertragingen bij het hervatting van een onderbroken status.
- Meerdere databases met onregelmatige, onvoorspelbare gebruikspatronen die kunnen worden geconsolideerd in elastische pools voor een betere optimalisatie van de prijsprestaties.

## <a name="comparison-with-provisioned-compute-tier"></a>Vergelijking met inrichtende rekenlaag

De volgende tabel bevat een overzicht van de verschillen tussen de serverloze rekenlaag en de inrichtende rekenlaag:

| | **Serverloze compute** | **Inrichten van rekenkracht** |
|:---|:---|:---|
|**Databasegebruikspatroon**| Onregelmatige, onvoorspelbare gebruik met een lager gemiddeld rekengebruik gedurende een periode. | Meer normale gebruikspatronen met een hoger gemiddeld rekengebruik gedurende een bepaalde periode of meerdere databases die gebruikmaken van elastische pools.|
| **Prestatiebeheerinspanning** |Lager|Hoger|
|**Schaalbaarheid van rekenkracht**|Automatisch|Handmatig|
|**Reactiesnelheid van rekenkracht**|Lager na inactieve perioden|Onmiddellijke|
|**Factureringsgranulariteit**|Per seconde|Per uur|

## <a name="purchasing-model-and-service-tier"></a>Aankoopmodel en servicelaag

SQL Database serverloos wordt momenteel alleen ondersteund in de Algemeen-laag op hardware van de 5e generatie in het vCore-aankoopmodel.

## <a name="autoscaling"></a>Automatisch schalen

### <a name="scaling-responsiveness"></a>Reactiesnelheid schalen

In het algemeen worden serverloze databases uitgevoerd op een machine met voldoende capaciteit om te voldoen aan de vraag naar resources zonder onderbreking van de hoeveelheid rekenkracht die is aangevraagd binnen de limieten die zijn ingesteld door de maximale vCores-waarde. Af en toe treedt taakverdeling automatisch op als de machine niet binnen een paar minuten kan voldoen aan de vraag naar resources. Als de vraag naar resources bijvoorbeeld 4 vCores is, maar er slechts twee vCores beschikbaar zijn, kan het enkele minuten duren voordat er vier vCores zijn opgegeven. De database blijft online tijdens taakverdeling, met uitzondering van een korte periode aan het einde van de bewerking wanneer verbindingen worden verwijderd.

### <a name="memory-management"></a>Geheugenbeheer

Geheugen voor serverloze databases wordt vaker vrijgevorderd dan voor inrichtende rekendatabases. Dit gedrag is belangrijk voor het beheren van de kosten in serverloos en kan van invloed zijn op de prestaties.

#### <a name="cache-reclamation"></a>Cache-reclamatie

In tegenstelling tot inrichtende rekendatabases wordt geheugen uit de SQL-cache vrij gemaakt van een serverloze database wanneer het CPU- of actieve cachegebruik laag is.

- Actief cachegebruik wordt als laag beschouwd wanneer de totale grootte van de laatst gebruikte cachegegevens een bepaalde tijd onder een drempelwaarde valt.
- Wanneer het vrijmaken van de cache wordt geactiveerd, wordt de grootte van de doelcache stapsgewijs gereduceerd tot een fractie van de vorige grootte en wordt het vrijmaken alleen voortgezet als het gebruik laag blijft.
- Bij het vrijmaken van de cache is het beleid voor het selecteren van cache-vermeldingen die moeten worden verwijderd, hetzelfde selectiebeleid als voor inrichtende rekendatabases wanneer de geheugendruk hoog is.
- De cachegrootte wordt nooit lager dan de minimumgeheugenlimiet, zoals gedefinieerd door min. vCores, die kunnen worden geconfigureerd.

In zowel serverloze als inrichtende rekendatabases kunnen cache-vermeldingen worden verwijderd als al het beschikbare geheugen wordt gebruikt.

Houd er rekening mee dat wanneer het CPU-gebruik laag is, het actieve cachegebruik hoog kan blijven, afhankelijk van het gebruikspatroon en dat geheugen niet kan worden vrijgediend.  Er kunnen ook extra vertragingen optreden nadat de gebruikersactiviteit stopt voordat het geheugen wordt vrijgetrokken, omdat periodieke achtergrondprocessen reageren op eerdere gebruikersactiviteit.  Verwijderbewerkingen en QDS-opschoontaken genereren bijvoorbeeld gedupleterecords die zijn gemarkeerd voor verwijdering, maar niet fysiek worden verwijderd totdat het opschoningsproces wordt uitgevoerd waarbij gegevenspagina's in de cache kunnen worden gelezen.

#### <a name="cache-hydration"></a>Cache-uiting

De SQL-cache groeit naarmate gegevens op dezelfde manier en met dezelfde snelheid van de schijf worden opgehaald als voor inrichtende databases. Wanneer de database bezet is, mag de cache onbeperkt groeien tot de maximale geheugenlimiet.

## <a name="auto-pausing-and-auto-resuming"></a>Automatisch onderbreken en automatisch hervat

### <a name="auto-pausing"></a>Automatisch onderbreken

Automatisch onderbreken wordt geactiveerd als aan alle volgende voorwaarden wordt voldaan voor de duur van de vertraging voor automatisch onderbreken:

- Aantal sessies = 0
- CPU = 0 voor de gebruikersworkload die wordt uitgevoerd in de gebruikersgroep

Er wordt een optie geboden om automatisch onderbreken uit te schakelen, indien gewenst.

De volgende functies bieden geen ondersteuning voor automatisch onderbreken, maar bieden wel ondersteuning voor automatisch schalen.  Als een van de volgende functies wordt gebruikt, moet automatisch onderbreken worden uitgeschakeld en blijft de database online, ongeacht de duur van inactiviteit van de database:

- Geo-replicatie (actieve geo-replicatie en groepen voor automatische failover).
- Langetermijnretentie van back-ups (LTR).
- De synchronisatiedatabase die wordt gebruikt in SQL Data Sync.  In tegenstelling tot synchronisatiedatabases ondersteunen hub- en liddatabases automatisch onderbreken.
- DNS-aliasing
- De taakdatabase die wordt gebruikt in Elastische taken (preview).

Automatisch onderbreken wordt tijdelijk voorkomen tijdens de implementatie van sommige service-updates waarvoor de database online moet zijn.  In dergelijke gevallen wordt automatisch onderbreken weer toegestaan zodra de service-update is voltooid.

### <a name="auto-resuming"></a>Automatisch hervat

Automatische hervatting wordt geactiveerd als aan een van de volgende voorwaarden op elk moment wordt voldaan:

|Functie|Trigger voor automatisch hervatten|
|---|---|
|Verificatie en autorisatie|Aanmelden|
|Detectie van bedreigingen|Instellingen voor detectie van bedreigingen in- of uitschakelen op database- of serverniveau.<br>Instellingen voor detectie van bedreigingen wijzigen op database- of serverniveau.|
|Gegevensdetectie en -classificatie|Gevoeligheidslabels toevoegen, wijzigen, verwijderen of weergeven|
|Controleren|Controlerecords weergeven.<br>Controlebeleid bijwerken of weergeven.|
|Gegevensmaskering|Regels voor gegevensmaskering toevoegen, wijzigen, verwijderen of weergeven|
|Transparent Data Encryption|Status of status van transparante gegevensversleuteling weergeven|
|Evaluatie van beveiligingsproblemen|Ad-hocscans en periodieke scans indien ingeschakeld|
|Query's uitvoeren op gegevensopslag (prestaties)|Instellingen voor queryopslag wijzigen of weergeven|
|Aanbevelingen voor prestaties|Prestatieaanbevelingen weergeven of toepassen|
|Automatisch afstemmen|Toepassing en verificatie van aanbevelingen voor automatisch afstemmen, zoals automatisch indexeren|
|Database kopiëren|Maak de database als kopie.<br>Exporteren naar een BACPAC-bestand.|
|SQL-gegevenssynchronisatie|Synchronisatie tussen hub- en liddatabases die volgens een configureerbare planning worden uitgevoerd of handmatig worden uitgevoerd|
|Bepaalde databasemetagegevens wijzigen|Nieuwe databasetags toevoegen.<br>Het maximum aantal vCores, min. vCores of vertraging bij automatisch gebruik wijzigen.|
|SQL Server Management Studio (SSMS)|Het gebruik van SSMS-versies die lager zijn dan 18.1 en het openen van een nieuw queryvenster voor elke database op de server, hervat elke automatisch onderbroken database op dezelfde server. Dit gedrag treedt niet op als u SSMS versie 18.1 of hoger gebruikt.|

Bewaking, beheer of andere oplossingen die een van de hierboven genoemde bewerkingen uitvoeren, activeren automatisch hervatting.

Automatisch hervatting wordt ook geactiveerd tijdens de implementatie van een aantal service-updates waarvoor de database online moet zijn.

### <a name="connectivity"></a>Connectiviteit

Als een serverloze database is onderbroken, wordt de database hervat met de eerste aanmelding en wordt een foutbericht weergegeven waarin staat dat de database niet beschikbaar is met foutcode 40613. Zodra de database is hervat, moet de aanmelding opnieuw worden proberen om verbinding te maken. Database-clients met verbindingslogica voor opnieuw proberen hoeven niet te worden gewijzigd.

### <a name="latency"></a>Latentie

De latentie voor het automatisch hervatten en automatisch onderbreken van een serverloze database is doorgaans 1 minuut om automatisch te hervatten en 1-10 minuten om automatisch te onderbreken.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Door de klant beheerde transparante gegevensversleuteling (BYOK)

Als [](transparent-data-encryption-byok-overview.md) het gebruik van door de klant beheerde transparante gegevensversleuteling (BYOK) en de serverloze database automatisch wordt onderbroken wanneer de sleutel wordt verwijderd of intrekken, blijft de database in de status automatisch onderbroken.  In dit geval wordt de database na de volgende hervatting binnen ongeveer tien minuten ontoegankelijk.  Zodra de database niet meer toegankelijk is, is het herstelproces hetzelfde als voor inrichtende rekendatabases.  Als de serverloze database online is wanneer een sleutel wordt verwijderd of intrekken, is de database ook binnen ongeveer tien minuten niet toegankelijk op dezelfde manier als bij inrichtende rekendatabases.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding in serverloze rekenlaag

Het maken van een nieuwe database of het verplaatsen van een bestaande database naar een serverloze rekenlaag volgt hetzelfde patroon als het maken van een nieuwe database in de inrichtende rekenlaag en omvat de volgende twee stappen.

1. Geef de servicedoelstelling op. De servicedoelstelling is van het oog op de servicelaag, het genereren van hardware en het maximum aantal vCores. Zie Serverloze [resourcelimieten voor opties voor servicedoelstelling](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Geef desgewenst het minimum aantal vCores en de vertraging voor automatisch gebruik op om de standaardwaarden te wijzigen. In de volgende tabel ziet u de beschikbare waarden voor deze parameters.

   |Parameter|Waardeopties|Standaardwaarde|
   |---|---|---|---|
   |Minimum aantal vCores|Is afhankelijk van het maximum aantal vCores dat is geconfigureerd. Zie [resourcelimieten.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 vCores|
   |Vertraging bij automatisch gebruik|Minimaal: 60 minuten (1 uur)<br>Maximum: 10080 minuten (7 dagen)<br>Verhogingen: 10 minuten<br>Autopause uitschakelen: -1|60 minuten|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Een nieuwe database maken in de serverloze rekenlaag

In de volgende voorbeelden wordt een nieuwe database gemaakt in de serverloze rekenlaag.

#### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Zie [Quickstart: Een individuele database maken in Azure SQL Database met behulp van de Azure Portal](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) gebruiken

Wanneer u T-SQL gebruikt, worden standaardwaarden toegepast voor de min. vcores en vertraging bij automatisch gebruik.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Zie CREATE [DATABASE voor meer informatie.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Een database verplaatsen van de inrichtende rekenlaag naar de serverloze rekenlaag

In de volgende voorbeelden wordt een database verplaatst van de inrichtende rekenlaag naar de serverloze rekenlaag.

#### <a name="use-powershell"></a>PowerShell gebruiken


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) gebruiken

Wanneer u T-SQL gebruikt, worden standaardwaarden toegepast voor de min. vcores en vertraging bij automatisch onderbreken.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Zie ALTER [DATABASE voor meer informatie.](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Een database verplaatsen van de serverloze rekenlaag naar de inrichtende rekenlaag

Een serverloze database kan op dezelfde manier worden verplaatst naar een inrichtende rekenlaag als een inrichtende rekendatabase naar een serverloze rekenlaag.

## <a name="modifying-serverless-configuration"></a>Serverloze configuratie wijzigen

### <a name="use-powershell"></a>PowerShell gebruiken

Het wijzigen van de maximale of minimale vCores en de vertraging bij automatisch gebruik wordt uitgevoerd met behulp van de [opdracht Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) in PowerShell met behulp van de argumenten `MaxVcore` , en `MinVcore` `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Het wijzigen van de maximale of minimale vCores en de vertraging voor automatisch gebruik wordt uitgevoerd met behulp van de [opdracht az sql db update](/cli/azure/sql/db#az-sql-db-update) in Azure CLI met behulp van de argumenten , en `capacity` `min-capacity` `auto-pause-delay` .


## <a name="monitoring"></a>Bewaking

### <a name="resources-used-and-billed"></a>Gebruikte en gefactureerde resources

De resources van een serverloze database worden ingekapseld door app-pakket, SQL-exemplaar en gebruikersresourcegroepentiteiten.

#### <a name="app-package"></a>App-pakket

Het app-pakket is de buitenste bronbeheergrens voor een database, ongeacht of de database zich in een serverloze of inrichtende rekenlaag heeft. Het app-pakket bevat het SQL-exemplaar en externe services die samen het bereik hebben van alle gebruikers- en systeembronnen die worden gebruikt door een database in SQL Database. Voorbeelden van externe services zijn R en zoeken in volledige tekst. Het SQL-exemplaar is in het algemeen de over het hele resourcegebruik in het app-pakket.

#### <a name="user-resource-pool"></a>Gebruikersresourcegroep

De gebruikersresourcegroep is de binnenste bronbeheergrens voor een database, ongeacht of de database zich in een serverloze of inrichtende rekenlaag heeft. De resourcegroep van de gebruiker scopes CPU en IO voor gebruikersworkload gegenereerd door DDL-query's zoals CREATE en ALTER en DML-query's zoals SELECT, INSERT, UPDATE en DELETE. Deze query's vertegenwoordigen over het algemeen het grootste deel van het gebruik binnen het app-pakket.

### <a name="metrics"></a>Metrische gegevens

Metrische gegevens voor het bewaken van het resourcegebruik van het app-pakket en de gebruikersgroep van een serverloze database worden vermeld in de volgende tabel:

|Entiteit|Metrisch|Beschrijving|Eenheden|
|---|---|---|---|
|App-pakket|app_cpu_percent|Het percentage vCores dat door de app wordt gebruikt ten opzichte van het maximum aantal vCores dat is toegestaan voor de app.|Percentage|
|App-pakket|app_cpu_billed|De hoeveelheid rekenkracht die voor de app wordt gefactureerd tijdens de rapportageperiode. Het bedrag dat tijdens deze periode wordt betaald, is het product van deze metrische gegevens en de prijs van de vCore-eenheid. <br><br>De waarden van deze metrische waarde worden bepaald door het maximum aantal gebruikte CPU's en het geheugen dat elke seconde wordt gebruikt, in de tijd samen te stellen. Als de gebruikte hoeveelheid kleiner is dan de minimale hoeveelheid die is ingericht zoals is ingesteld door de minimale vCores en het minimale geheugen, wordt de minimale hoeveelheid gefactureerd.Om DE CPU te vergelijken met het geheugen voor factureringsdoeleinden, wordt het geheugen genormaliseerd in eenheden van vCores door de hoeveelheid geheugen in GB met 3 GB per vCore te schalen.|vCore-seconden|
|App-pakket|app_memory_percent|Percentage geheugen dat door de app wordt gebruikt ten opzichte van het maximale geheugen dat is toegestaan voor de app.|Percentage|
|Gebruikersgroep|cpu_percent|Het percentage vCores dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van het maximum aantal vCores dat is toegestaan voor de werkbelasting van de gebruiker.|Percentage|
|Gebruikersgroep|data_IO_percent|Percentage gegevens-IOPS dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van het maximum aantal toegestane gegevens-IOPS voor gebruikersworkloads.|Percentage|
|Gebruikersgroep|log_IO_percent|Percentage logboek-MB/s dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van het maximum aantal logboek-MB/s dat is toegestaan voor gebruikersworkload.|Percentage|
|Gebruikersgroep|workers_percent|Het percentage werkbelastingen dat wordt gebruikt door de werkbelasting van gebruikers ten opzichte van het maximum aantal werkbelastingen dat is toegestaan voor gebruikers.|Percentage|
|Gebruikersgroep|sessions_percent|Het percentage sessies dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van het maximum aantal sessies dat is toegestaan voor de werkbelasting van de gebruiker.|Percentage|

### <a name="pause-and-resume-status"></a>Status onderbreken en hervatten

In de Azure Portal wordt de databasestatus weergegeven in het overzichtsvenster van de server met de databases die deze bevat. De databasestatus wordt ook weergegeven in het overzichtsvenster voor de database.

Gebruik de volgende opdrachten om de status van een database onderbreken en hervatten op te vragen:

#### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Bronlimieten

Zie Serverloze rekenlaag [voor resourcelimieten.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)

## <a name="billing"></a>Billing

De hoeveelheid rekenkracht die wordt gefactureerd, is de maximale cpu die wordt gebruikt en het geheugen dat elke seconde wordt gebruikt. Als de gebruikte hoeveelheid CPU en het gebruikte geheugen minder is dan de minimale hoeveelheid die voor elk cpu-gebruik is ingericht, wordt het inrichtende bedrag gefactureerd. Als u CPU wilt vergelijken met geheugen voor factureringsdoeleinden, wordt het geheugen genormaliseerd in eenheden van vCores door de hoeveelheid geheugen in GB met 3 GB per vCore te schalen.

- **Gefactureerde resource:** CPU en geheugen
- **Gefactureerd** bedrag: prijs per vCore * max (min. vCores, gebruikte vCores, min. geheugen GB * 1/3, geheugen GB gebruikt * 1/3) 
- **Factureringsfrequentie:** per seconde

De prijs per vCore-eenheid is de kosten per vCore per seconde. Raadpleeg de pagina [Azure SQL Database prijzen voor](https://azure.microsoft.com/pricing/details/sql-database/single/) specifieke eenheidsprijzen in een bepaalde regio.

De hoeveelheid gefactureerde rekenkracht wordt blootgesteld aan de volgende metrische gegevens:

- **Metrische** gegevens: app_cpu_billed (vCore-seconden)
- **Definitie:** max (min. vCores, gebruikte vCores, min. geheugen GB * 1/3, geheugen GB gebruikt * 1/3)
- **Rapportagefrequentie:** per minuut

Deze hoeveelheid wordt elke seconde berekend en gedurende 1 minuut geaggregeerd.

### <a name="minimum-compute-bill"></a>Minimale rekenrekening

Als een serverloze database is onderbroken, is de rekenrekening nul.  Als een serverloze database niet is onderbroken, is de minimale rekenrekening niet lager dan de hoeveelheid vCores op basis van het maximum (min. vCores, min. geheugen GB * 1/3).

Voorbeelden:

- Stel dat een serverloze database niet is onderbroken en geconfigureerd met maximaal 8 vCores en 1 min. vCores die overeenkomen met 3,0 GB min. geheugen.  Vervolgens is de minimale rekenrekening gebaseerd op het maximum (1 vCore, 3,0 GB * 1 vCore / 3 GB) = 1 vCore.
- Stel dat een serverloze database niet is onderbroken en geconfigureerd met maximaal 4 vCores en 0,5 min. vCores die overeenkomen met 2,1 GB min. geheugen.  Vervolgens is de minimale rekenrekening gebaseerd op het maximum (0,5 vCores, 2,1 GB * 1 vCore / 3 GB) = 0,7 vCores.

De [Azure SQL Database prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) voor serverloos kan worden gebruikt om te bepalen welk minimumgeheugen kan worden geconfigureerd op basis van het aantal maximaal en min. aantal vCores dat is geconfigureerd.  Als de geconfigureerde minimale vCores groter is dan 0,5 vCores, is de minimale rekenrekening onafhankelijk van het minimale geheugen dat is geconfigureerd en alleen gebaseerd op het aantal minimale vCores dat is geconfigureerd.

### <a name="example-scenario"></a>Voorbeeldscenario

Overweeg een serverloze database die is geconfigureerd met 1 min. vCores en maximaal 4 vCores.  Dit komt overeen met ongeveer 3 GB min. geheugen en maximaal 12 GB geheugen.  Stel dat de vertraging voor automatisch onderbreken is ingesteld op 6 uur en dat de databaseworkload actief is gedurende de eerste 2 uur van een periode van 24 uur en anders inactief is.    

In dit geval wordt de database in de eerste 8 uur gefactureerd voor reken- en opslagcapaciteit.  Hoewel de database inactief is vanaf het tweede uur, wordt deze in de volgende zes uur nog steeds gefactureerd voor rekenkracht op basis van de minimale rekenkracht die is ingericht terwijl de database online is.  Alleen opslag wordt gefactureerd gedurende de rest van de periode van 24 uur terwijl de database wordt onderbroken.

Om precies te zijn, wordt de rekenrekening in dit voorbeeld als volgt berekend:

|Tijdsinterval|vCores die elke seconde worden gebruikt|GB die elke seconde wordt gebruikt|Rekendimensie gefactureerd|vCore-seconden gefactureerd gedurende een tijdsinterval|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores gebruikt|4 vCores * 3600 seconden = 14400 vCore-seconden|
|1:00-2:00|1|12|Gebruikt geheugen|12 GB * 1/3 * 3600 seconden = 14400 vCore-seconden|
|2:00-8:00|0|0|Min. geheugen ingericht|3 GB * 1/3 * 21600 seconden = 21600 vCore-seconden|
|8:00-24:00|0|0|Er wordt geen rekenkracht gefactureerd terwijl deze is onderbroken|0 vCore-seconden|
|Totaal aantal vCore-seconden gefactureerd gedurende 24 uur||||50400 vCore seconden|

Stel dat de prijs van de rekeneenheid $ 0,000145/vCore/seconde is.  Vervolgens is de berekening die wordt gefactureerd voor deze periode van 24 uur het product van de prijs van de rekeneenheid en de vCore-seconden die worden gefactureerd: $0,000145/vCore/seconde * 50400 vCore-seconden ~ $ 7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit en gereserveerde capaciteit

Azure Hybrid Benefit (AHB) en kortingen voor gereserveerde capaciteit zijn niet van toepassing op de serverloze rekenlaag.

## <a name="available-regions"></a>Beschikbare regio's

De serverloze rekenlaag is wereldwijd beschikbaar, met uitzondering van de volgende regio's: China - oost, China - noord, Duitsland - centraal, Duitsland - noordoost en US Gov Central (Iowa).

## <a name="next-steps"></a>Volgende stappen

- Als u aan de slag wilt gaan, raadpleegt u [Quickstart: Een individuele database maken in Azure SQL Database met de Azure-portal](single-database-create-quickstart.md).
- Zie voor resourcelimieten [Resourcelimieten voor serverloze compute-lagen](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
