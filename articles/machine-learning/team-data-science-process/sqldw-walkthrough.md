---
title: Een model bouwen en implementeren met behulp van Azure Synapse Analytics-team data Science process
description: Bouw en implementeer een machine learning model met behulp van Azure Synapse Analytics met een openbaar beschik bare gegevensset.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93315998"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Het proces van de team data Science in actie: Azure Synapse Analytics gebruiken
In deze zelf studie leert u hoe u een machine learning model bouwt en implementeert met behulp van Azure Synapse Analytics voor een openbaar beschik bare gegevensset, de NYC-gegevensset voor de [taxi](https://www.andresmh.com/nyctaxitrips/) Het binaire classificatie model heeft voor speld, ongeacht of er een tip voor een reis wordt betaald.  Modellen bevatten een multi klasse-classificatie (ongeacht of er sprake is van een tip) en regressie (de verdeling van de fooien die worden betaald).

De procedure volgt de werk stroom [team data Science process (TDSP)](./index.yml) . We laten zien hoe u een Data Science-omgeving instelt, hoe u de gegevens in azure Synapse Analytics laadt en hoe u Azure Synapse Analytics of een IPython-notebook gebruikt om de gegevens-en Engineer functies te verkennen. Vervolgens laten we zien hoe u een model met Azure Machine Learning bouwt en implementeert.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>De NYC-gegevensset voor taxi trips
De NYC-gegevens over de taxi bestaan uit ongeveer 20 GB gecomprimeerde CSV-bestanden (~ 48 GB niet-gecomprimeerd), waarbij meer dan 173.000.000 afzonderlijke reizen en de betaalde tarieven voor elke reis worden opgenomen. Elke reis record bevat de locaties en tijden voor ophalen en dropoff, het licentie nummer van geanonimiseerd Hack (rijbewijs) en het Medallion (unieke ID-nummer van de taxi). De gegevens omvatten alle reizen in het jaar 2013 en worden in de volgende twee gegevens sets voor elke maand vermeld:

1. Het **trip_data.csv** bestand bevat reis details, zoals het aantal reizigers, de ophaal-en dropoff punten, de duur van de reis en de lengte van de reis. Hier volgen enkele voorbeeld records:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Het **trip_fare.csv** bestand bevat de details van het ritbedrag dat voor elke reis is betaald, zoals het betalings type, het tarief bedrag, de toeslag en belastingen, fooien en het aantal verschuldigde bedragen. Hier volgen enkele voorbeeld records:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

De **unieke sleutel** die wordt gebruikt voor het koppelen \_ van reis gegevens en reis \_ tarief, bestaat uit de volgende drie velden:

* medallion,
* Hack- \_ licentie en
* \_datum/tijd van ophalen.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Drie typen Voorspellings taken adresseren
We formuleren drie Voorspellings problemen op basis van het *fooien \_ aantal* om drie soorten model taken te illustreren:

1. **Binaire classificatie**: als u wilt voors pellen of er voor een reis een tip is betaald, dat wil zeggen, is een *fooiwaarde \_* van meer dan $0 een positief voor beeld, terwijl een *Tip- \_ bedrag* van $0 een negatief voor beeld is.
2. **Classificatie** met verschillende klassen: om het bereik van fooien voor de reis te voors pellen. We delen het *fooien \_ bedrag* in vijf bakken of klassen:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Regressie taak**: voor het voors pellen van de hoeveelheid fooien die voor een reis wordt betaald.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>De Azure data Science-omgeving instellen voor geavanceerde analyse
Voer de volgende stappen uit om uw Azure data Science-omgeving in te stellen.

**Uw eigen Azure Blob-opslag account maken**

* Wanneer u uw eigen Azure Blob-opslag inricht, kiest u een geografische locatie voor uw Azure Blob-opslag in of zo dicht mogelijk bij **Zuid-Centraal VS**, waar de NYCe taxi gegevens worden opgeslagen. De gegevens worden gekopieerd met AzCopy uit de open bare Blob-opslag container naar een container in uw eigen opslag account. Hoe dichter uw Azure Blob-opslag is naar Zuid-Centraal, hoe sneller deze taak (stap 4) wordt voltooid.
* Als u uw eigen Azure Storage-account wilt maken, volgt u de stappen die worden beschreven in [over Azure Storage-accounts](../../storage/common/storage-account-create.md). Zorg ervoor dat u notities maakt voor de waarden van de volgende referenties voor het opslag account, omdat deze later in dit overzicht nodig zijn.

  * **Naam van opslag account**
  * **Sleutel van het opslag account**
  * **Container naam** (die u wilt dat de gegevens worden opgeslagen in de Azure Blob-opslag)

**Richt uw Azure Synapse Analytics-exemplaar in.**
Volg de documentatie op [een Azure Synapse Analytics maken en query's uitvoeren in de Azure Portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) om een Azure Synapse Analytics-exemplaar in te richten. Zorg ervoor dat u een notatie maakt voor de volgende Azure Synapse Analytics-referenties die in latere stappen zullen worden gebruikt.

* **Server naam**: \<server Name> . database.Windows.net
* **SQLDW-naam (data base)**
* **Gebruikersnaam**
* **Wachtwoord**

**Installeer Visual Studio en SQL Server Data Tools.** Zie aan de slag [met Visual Studio 2019 voor Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)voor instructies.

**Maak verbinding met uw Azure Synapse Analytics met Visual Studio.** Zie stap 1 & 2 in [verbinding maken met SQL Analytics in azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md)voor instructies.

> [!NOTE]
> Voer de volgende SQL-query uit op de data base die u hebt gemaakt in uw Azure Synapse Analytics (in plaats van de query die is opgenomen in stap 3 van het onderwerp Connect) om **een hoofd sleutel te maken**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Maak een Azure Machine Learning-werk ruimte onder uw Azure-abonnement.** Zie [een Azure machine learning-werk ruimte maken](../classic/create-workspace.md)voor instructies.

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>De gegevens laden in azure Synapse Analytics
Open een Windows Power shell-opdracht console. Voer de volgende Power shell-opdrachten uit om de voorbeeld SQL-script bestanden te downloaden die met u worden gedeeld op GitHub naar een lokale map die u opgeeft met de para meter *-DestDir*. U kunt de waarde van para meter *-DestDir* wijzigen naar een lokale map. Als *-DestDir* niet bestaat, wordt dit gemaakt door het Power shell-script.

> [!NOTE]
> Mogelijk moet u **als Administrator worden uitgevoerd** bij het uitvoeren van het volgende Power shell-script als uw *DestDir* -Directory moet beschikken over beheerders rechten om ernaar te schrijven
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Nadat de uitvoering is voltooid, wordt de huidige werkmap gewijzigd in *-DestDir*. Hieronder ziet u een scherm zoals hieronder:

![Huidige wijzigingen in de werkmap][19]

In uw *-DestDir* voert u het volgende Power shell-script uit in de Administrator-modus:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Wanneer het Power shell-script voor de eerste keer wordt uitgevoerd, wordt u gevraagd om de gegevens van uw Azure Synapse Analytics en uw Azure Blob-opslag account in te voeren. Wanneer dit Power shell-script voor de eerste keer wordt uitgevoerd, zijn de referenties die u hebt ingevoerd, geschreven naar een configuratie bestand SQLDW. conf in de huidige werkmap. De toekomstige uitvoering van dit Power shell-script bestand bevat de optie om alle benodigde para meters uit dit configuratie bestand te lezen. Als u een aantal para meters moet wijzigen, kunt u de para meters op het scherm op de vraag opgeven door dit configuratie bestand te verwijderen en de parameter waarden in te stellen wanneer u hierom wordt gevraagd of door de waarden van de para meters te wijzigen door het bestand SQLDW. conf te bewerken in de map *DestDir* .

> [!NOTE]
> Om te voor komen dat de schema naam strijdig is met de namen die al bestaan in uw Azure Azure Synapse Analytics, wanneer u de para meters rechtstreeks vanuit het bestand SQLDW. conf leest, wordt een wille keurig getal van drie cijfers toegevoegd aan de schema naam uit het bestand SQLDW. conf als de standaard schema naam voor elke uitvoering. Het Power shell-script kan u om een schema naam vragen: de naam kan worden opgegeven bij de gebruikers keuze.
>
>

Dit **Power shell-script** bestand voert de volgende taken uit:

* **Down loads en installeert AzCopy** als AzCopy nog niet is geïnstalleerd

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **Kopieert gegevens naar uw persoonlijke Blob Storage-account** vanuit de open bare blob met AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Laadt gegevens met poly base (door LoadDataToSQLDW. SQL uit te voeren) aan uw Azure Synapse Analytics** vanuit uw persoonlijke Blob Storage-account met de volgende opdrachten.

  * Een schema maken

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Een referentie voor een Data Base-scope maken

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Een externe gegevens bron maken voor een Azure Storage BLOB

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * Een externe bestands indeling maken voor een CSV-bestand. De gegevens worden gedecomprimeerd en velden worden gescheiden met het sluis teken.

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Externe ritbedrag-en reis tabellen maken voor NYC taxi-gegevensset in Azure Blob-opslag.

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Gegevens uit externe tabellen in Azure Blob-opslag laden in azure Synapse Analytics

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - Maak een voorbeeld gegevens tabel (NYCTaxi_Sample) en voeg er gegevens aan toe om SQL-query's te selecteren in de reis-en ritbedrag tabellen. (In sommige stappen van deze walkthrough moet u deze voorbeeld tabel gebruiken.)

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

De geografische locatie van uw opslag accounts is van invloed op laad tijden.

> [!NOTE]
> Afhankelijk van de geografische locatie van uw persoonlijke Blob Storage-account, kan het proces van het kopiëren van gegevens van een open bare BLOB naar uw privé-opslag account ongeveer 15 minuten of zelfs langer duren en het proces van het laden van gegevens van uw opslag account naar uw Azure Azure Synapse Analytics kan 20 minuten of langer duren.
>
>

U moet bepalen wat er gebeurt als u dubbele bron-en doel bestanden hebt.

> [!NOTE]
> Als de CSV-bestanden die moeten worden gekopieerd uit de open bare Blob-opslag naar uw persoonlijke Blob Storage-account al bestaan in uw privé Blob Storage-account, wordt u door AzCopy gevraagd of u deze wilt overschrijven. Als u deze niet wilt overschrijven, voert u de invoer **n** uit wanneer u hierom wordt gevraagd. Als u deze **allemaal** wilt overschrijven, voert u **een** invoer in wanneer u hierom wordt gevraagd. U kunt ook **y** invoeren om CSV-bestanden afzonderlijk te overschrijven.
>
>

![Uitvoer van AzCopy][21]

U kunt uw eigen gegevens gebruiken. Als uw gegevens zich op uw on-premises machine in uw echte app bevindt, kunt u nog steeds AzCopy gebruiken om on-premises gegevens te uploaden naar uw persoonlijke Azure Blob-opslag. U hoeft alleen de **bron** locatie te wijzigen, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` in de AzCopy-opdracht van het Power shell-script bestand naar de lokale map die uw gegevens bevat.

> [!TIP]
> Als uw gegevens zich al in uw persoonlijke Azure Blob-opslag in uw echte App-toepassing bevindt, kunt u de AzCopy-stap in het Power shell-script overs Laan en de gegevens rechtstreeks uploaden naar Azure Azure Synapse Analytics. Hiervoor zijn aanvullende bewerkingen van het script vereist om het te passen op de indeling van uw gegevens.
>
>

Met dit Power shell-script kunt u ook de Azure Synapse Analytics-gegevens in de voorbeeld bestanden voor gegevens exploratie met een SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb en SQLDW_Explorations_Scripts. py koppelen, zodat deze drie bestanden gereed zijn om onmiddellijk na het uitvoeren van het Power shell-script te worden uitgevoerd.

Na een geslaagde uitvoering ziet u een scherm zoals hieronder wordt weer gegeven:

![Uitvoer van een geslaagde script uitvoering][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Ontwikkeling van gegevens en functies in azure Synapse Analytics
In deze sectie voeren we het verkennen en het genereren van functies uit door SQL-query's uit te voeren op Azure Synapse Analytics direct met behulp van **Visual Studio data tools**. Alle SQL-query's die in deze sectie worden gebruikt, vindt u in het voorbeeld script met de naam *SQLDW_Explorations. SQL*. Dit bestand is al gedownload naar uw lokale map door het Power shell-script. U kunt deze ook ophalen via [github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Maar in het bestand in GitHub is de analyse gegevens van Azure Synapse niet aangesloten.

Maak verbinding met uw Azure Synapse Analytics met behulp van Visual Studio met de aanmeldings naam en het wacht woord voor Azure Synapse Analytics en open de **SQL objectverkenner** om te bevestigen dat de data base en de tabellen zijn geïmporteerd. Haal het *SQLDW_Explorations. SQL* -bestand op.

> [!NOTE]
> Als u een PDW-query editor (parallel data warehouse) wilt openen, gebruikt u de **nieuwe query** opdracht terwijl uw PDW is geselecteerd in de **SQL-objectverkenner**. De standaard SQL-query editor wordt niet ondersteund door PDW.
>
>

Dit zijn de typen taken voor het verkennen van gegevens en het genereren van functies die in deze sectie worden uitgevoerd:

* Verken gegevens distributies van een paar velden in verschillende tijd Vensters.
* Onderzoek de kwaliteit van de gegevens van de velden lengte graad en breedte graad.
* Genereer binaire en classificatie labels voor multi klassen op basis van het **fooien \_ bedrag**.
* Maak functies en bereken/vergelijk de retour waarden voor de reis.
* Voeg de twee tabellen samen en extraheer een wille keurig voor beeld die wordt gebruikt om modellen te maken.

### <a name="data-import-verification"></a>Verificatie van gegevens import
Deze query's bieden een snelle controle van het aantal rijen en kolommen in de tabellen dat eerder is gevuld met de parallelle bulk import van poly base.

--Rapport aantal rijen in tabel <nyctaxi_trip> zonder tabel scan

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

--Rapport aantal kolommen in tabel <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Uitvoer:** U moet 173.179.759 rijen en 14 kolommen ophalen.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: reis distributie per Medallion
In dit voor beeld wordt de Medallions (taxi-nummers) geïdentificeerd die meer dan 100 trips binnen een opgegeven periode hebben voltooid. De query zou profiteren van de gepartitioneerde tabel toegang, omdat deze wordt voor bereid op het partitie schema van de **\_ datum van ophalen**. Bij het uitvoeren van query's op de volledige gegevensset wordt ook gebruikgemaakt van de gepartitioneerde tabel en/of index scan.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Uitvoer:** De query moet een tabel retour neren met rijen waarin de 13.369 Medallions (taxi's) en het aantal trips dat is voltooid in 2013 worden geretourneerd. De laatste kolom bevat het aantal voltooide reizen.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploratie: reis distributie door Medallion en hack_license
In dit voor beeld worden de Medallions (taxi nummers) en hack_license getallen (Stuur Programma's) geïdentificeerd die meer dan 100 trips binnen een opgegeven periode hebben voltooid.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Uitvoer:** De query moet een tabel retour neren met 13.369 rijen die de 13.369-Car/stuur programma-Id's opgeven die meer dan 100 trips in 2013 hebben voltooid. De laatste kolom bevat het aantal voltooide reizen.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Beoordeling van gegevens kwaliteit: Verifieer records met een onjuiste lengte graad en/of breedte graad
In dit voor beeld wordt onderzocht of een van de lengte-en/of breedte velden een ongeldige waarde bevat (radiale graden moet tussen-90 en 90) zijn of (0, 0) coördinaten hebben.

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Uitvoer:** De query retourneert 837.467 trips met een ongeldige lengte graad en/of een breedte van een veld.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Verkennen: gekanteld versus zonder gekantelde trips
In dit voor beeld wordt gezocht naar het aantal trips dat is gekanteld versus. het aantal dat niet in een opgegeven periode is gekanteld (of in de volledige gegevensset als dit het volledige jaar bevat dat hier wordt ingesteld). Deze verdeling weerspiegelt de binaire label distributie om later te worden gebruikt voor het model leren van een binaire classificatie.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Uitvoer:** De query moet de volgende tip-frequenties retour neren voor het jaar 2013:90.447.622 gekanteld en 82.264.709 niet gekanteld.

### <a name="exploration-tip-classrange-distribution"></a>Exploratie: punt/bereik distributie van fooien
In dit voor beeld wordt de verdeling van penpuntstijl-bereiken in een bepaalde periode (of in de volledige gegevensset voor het hele jaar) berekend. Deze distributie van label klassen wordt later gebruikt voor het model leren van een classificatie met multi klassen.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**Uitvoer**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploratie: reis afstand berekenen en vergelijken
In dit voor beeld worden de lengte van de ophaling en de dropoff en de breedte graad van de SQL-locatie geconverteerd naar de geografische locatie verschil en wordt een wille keurig voor beeld geretourneerd van de resultaten voor de vergelijking. In het voor beeld worden de resultaten beperkt tot geldige coördinaten, alleen met behulp van de query voor gegevens kwaliteits beoordeling die eerder is besproken.

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>Functie techniek voor het gebruik van SQL-functies
Soms kunnen SQL-functies een efficiënte optie zijn voor functie techniek. In dit scenario hebben we een SQL-functie gedefinieerd om de directe afstand tussen de locaties voor ophalen en dropoff te berekenen. U kunt de volgende SQL-scripts uitvoeren in **Visual Studio data tools**.

Dit is het SQL-script dat de functie afstand definieert.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

Hier volgt een voor beeld van het aanroepen van deze functie voor het genereren van functies in uw SQL-query:

--Voorbeeld query voor het aanroepen van de functie voor het maken van functies

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Uitvoer:** Met deze query wordt een tabel (met 2.803.538 rijen) gegenereerd met de breedte-en breedte graad van ophalen en dropoff en de bijbehorende directe afstanden in mijlen. Dit zijn de resultaten voor de eerste drie rijen:

| (Rijnummer) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74,001083 |40,736622 |-73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |-74,00399 |.7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Gegevens voorbereiden voor het maken van modellen
Met de volgende query wordt de **nyctaxi- \_ reis** -en **nyctaxi- \_ ritbedrag** tabellen toegevoegd, wordt een binaire classificatie label met een classificatie **\_ klasse** met meerdere klassen **gekanteld**, en wordt een voor beeld geëxtraheerd uit de volledig gekoppelde gegevensset. De steek proef wordt uitgevoerd door een subset van de trips op te halen op basis van de ophaal tijd.  Deze query kan worden gekopieerd en vervolgens rechtstreeks in de module [Azure machine learning Studio (klassiek)](https://studio.azureml.net) [import gegevens][importeren voor] directe gegevens opname van het SQL database-exemplaar in Azure worden geplakt. De query sluit records met onjuiste (0, 0) coördinaten toe.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

Wanneer u klaar bent om door te gaan met de Azure Machine Learning, kunt u het volgende doen:

1. Sla de laatste SQL-query op om de gegevens te extra heren en voor te bereiden en kopieer de query rechtstreeks naar een import gegevens][import-data] module in azure machine learning, of
2. Behoud de voor bereide en ontworpen gegevens die u wilt gebruiken voor het maken van modellen in een nieuwe Azure Synapse Analytics-tabel en gebruik de nieuwe tabel in de module [import data][import-data] in azure machine learning. Met het Power shell-script in de vorige stap hebt u deze taak voor u uitgevoerd. U kunt rechtstreeks vanuit deze tabel lezen in de module gegevens importeren.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Gegevens exploratie en functie techniek in IPython notebook
In deze sectie worden de gegevens voor het verkennen en het genereren van functies met behulp van python-en SQL-query's uitgevoerd op basis van de Azure Synapse Analytics die u eerder hebt gemaakt. Een voor beeld van een IPython-notebook met de naam **SQLDW_Explorations. ipynb** en een python-script bestand **SQLDW_Explorations_Scripts. py** zijn gedownload naar uw lokale map. Ze zijn ook beschikbaar op [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Deze twee bestanden zijn identiek in python-scripts. Het python-script bestand wordt aan u door gegeven voor het geval u geen IPython-notebook server hebt. Deze twee voor beelden van python-bestanden zijn ontworpen onder **Python 2,7**.

De benodigde informatie over Azure Synapse Analytics in de IPython-voorbeeld notitieblok en het python-script bestand dat naar uw lokale computer is gedownload, is eerder aan het Power shell-script gekoppeld. Ze zijn zonder enige wijziging een uitvoerbaar bestand.

Als u al een Azure Machine Learning-werk ruimte hebt ingesteld, kunt u de IPython-voor beeld-notebook rechtstreeks uploaden naar de IPython-notebook service van AzureML en de uitvoering ervan starten. Dit zijn de stappen die u kunt uploaden naar de IPython-notebook service van AzureML:

1. Meld u aan bij uw Azure Machine Learning-werk ruimte, klik bovenaan op **Studio** en klik op **notitie blokken** aan de linkerkant van de webpagina.

    ![Klik op studio en vervolgens op NOTEBOOKs][22]
2. Klik op **Nieuw** in de linkerbovenhoek van de webpagina en selecteer **python 2**. Vervolgens geeft u een naam op voor het notitie blok en klikt u op het vinkje om de nieuwe lege IPython-notebook te maken.

    ![Klik op nieuw en selecteer vervolgens python 2][23]
3. Klik op het symbool **Jupyter** in de linkerbovenhoek van het nieuwe IPython-notitie blok.

    ![Klik op Jupyter-symbool][24]
4. Sleep de voor beeld-IPython-notebook naar de **structuur** pagina van de IPython-Notebook Service voor AzureML en klik op **uploaden**. Vervolgens wordt de voor beeld-IPython-notebook geüpload naar de IPython-Notebook Service.

    ![Klik op uploaden][25]

Als u de voor beeld-IPython-notebook of het python-script bestand wilt uitvoeren, zijn de volgende python-pakketten nodig. Als u de IPython Notebook-Service gebruikt, zijn deze pakketten vooraf geïnstalleerd.

- Pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Wanneer u geavanceerde analytische oplossingen bouwt op Azure Machine Learning met grote gegevens, is dit de aanbevolen volg orde:

* Lees in een klein voor beeld van de gegevens in een gegevens frame in het geheugen.
* Voer enkele visualisaties en onderzoek uit met behulp van de voorbeeld gegevens.
* Experimenteer met functie techniek met behulp van de voorbeeld gegevens.
* Gebruik python om SQL-Query's rechtstreeks uit te voeren op Azure Synapse Analytics voor grotere gegevens ontwikkeling, gegevens manipulatie en functie techniek.
* Bepaal de steekproef grootte die geschikt is voor het bouwen van Azure Machine Learning-modellen.

Hier volgen enkele voor beelden van gegevens ontwikkeling, gegevens visualisatie en functie. Meer informatie over het verkennen van gegevens vindt u in de voor beeld-IPython-notebook en het python-voorbeeld script bestand.

### <a name="initialize-database-credentials"></a>Database referenties initialiseren
Initialiseer de instellingen voor de database verbinding in de volgende variabelen:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Database verbinding maken
Dit is de connection string waarmee de verbinding met de data base wordt gemaakt.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapport aantal rijen en kolommen in tabel <nyctaxi_trip>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totaal aantal rijen = 173179759
* Totaal aantal kolommen = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Rapport aantal rijen en kolommen in tabel <nyctaxi_fare>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totaal aantal rijen = 173179759
* Totaal aantal kolommen = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Lees-in een klein gegevens voorbeeld van de Azure Synapse Analytics-Data Base

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

De tijd voor het lezen van de voorbeeld tabel is 14,096495 seconden.
Het aantal opgehaalde rijen en kolommen = (1000, 21).

### <a name="descriptive-statistics"></a>Beschrijvende statistieken
Nu bent u klaar om de voorbeeld gegevens te verkennen. We beginnen met het bekijken van een aantal beschrijvende statistieken voor de **reis \_ afstand** (of andere velden die u opgeeft).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Visualisatie: Boxplot-voor beeld
Vervolgens kijken we naar het Boxplot-venster voor de reis afstand om de quantiles te visualiseren.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Boxplot-uitvoer][1]

### <a name="visualization-distribution-plot-example"></a>Visualisatie: voor beeld van een distributie tekening
Er worden grafieken getekend waarmee de distributie en een histogram worden gevisualiseerd voor de bemonsterde reis afstanden.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Distributie tekening uitvoer][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisatie: balk-en lijn tekenen
In dit voor beeld nemen we de reis afstand op in vijf opslag locaties en visualiseren we de binning-resultaten.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

We kunnen de bovenstaande opslaglocatie verdeling in een staaf-of lijn diagram uitzetten met:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Werk balk tekenen][3]

en

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Uitvoer van het regel teken][4]

### <a name="visualization-scatterplot-examples"></a>Visualisatie: scatterplot-voor beelden
Er wordt een spreidings diagram weer gegeven tussen de **\_ duur van de reis tijd \_ in \_ seconden** en **reis \_ afstand** om te zien of er een correlatie is

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Scatterplot uitvoer van relatie tussen tijd en afstand][6]

Op dezelfde manier kunnen we de relatie tussen **tarief \_ code** en **reis \_ afstand** controleren.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Scatterplot uitvoer van de relatie tussen code en afstand][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Gegevens verkennen op basis van voor beelden van gegevens met behulp van SQL-query's in IPython notebook
In deze sectie verkennen we gegevens distributies met behulp van de voorbeeld gegevens die zijn opgeslagen in de nieuwe tabel die we hierboven hebben gemaakt. Vergelijk bare exploratie kan worden uitgevoerd met behulp van de oorspronkelijke tabellen.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploratie: het aantal rijen en kolommen in de tabel met voor beelden rapporteren

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploratie: gekantelde/niet-verdeelde distributie

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Exploratie: distributie van punt klasse

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploratie: de tip-distributie op klasse zetten

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![#26 afzetten][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploratie: dagelijkse distributie van reizen

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploratie: reis distributie per Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploratie: reis distributie door Medallion en hack License

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Verkennen: distributie van reis tijd

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Verkennen: distributie van reis afstand

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Exploratie: distributie van het type betaling

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>De uiteindelijke vorm van de featurized-tabel controleren

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modellen maken in Azure Machine Learning
U kunt nu door gaan met het model leren van het bouwen en model implementeren in [Azure machine learning](https://studio.azureml.net). De gegevens zijn gereed voor gebruik in een van de eerder genoemde Voorspellings problemen, namelijk:

1. **Binaire classificatie**: om te voors pellen of er al dan niet een tip voor een reis is betaald.
2. **Classificatie** met verschillende klassen: om het bereik aan betaalde fooien te voors pellen volgens de eerder gedefinieerde klassen.
3. **Regressie taak**: voor het voors pellen van de hoeveelheid fooien die voor een reis wordt betaald.

Meld u aan bij uw **Azure machine learning (klassieke)** werk ruimte om de modellerings oefening te starten. Als u nog geen machine learning-werk ruimte hebt gemaakt, raadpleegt u [een werk ruimte Azure machine learning Studio (klassiek) maken](../classic/create-workspace.md).

1. Als u aan de slag wilt gaan met Azure Machine Learning, raadpleegt u [Wat is Azure machine learning Studio (klassiek)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Meld u aan bij [Azure machine learning Studio (klassiek)](https://studio.azureml.net).
3. Op de start pagina van Machine Learning Studio (klassiek) vindt u een schat aan informatie, Video's, zelf studies, koppelingen naar de referentie modules en andere bronnen. Voor meer informatie over Azure Machine Learning raadpleegt u het [Azure machine learning documentatie centrum](https://azure.microsoft.com/documentation/services/machine-learning/).

Een typisch Oefen experiment bestaat uit de volgende stappen:

1. Maak een **Nieuw** experiment.
2. De gegevens ophalen in Azure Machine Learning Studio (klassiek).
3. De gegevens vooraf verwerken, transformeren en manipuleren als dat nodig is.
4. Zo nodig functies genereren.
5. Splits de gegevens in sets voor training/validatie/testen (of een afzonderlijke gegevens sets voor elk).
6. Selecteer een of meer machine learning algoritmen, afhankelijk van het probleem dat u wilt oplossen. Bijvoorbeeld binaire classificatie, classificatie met meer klassen, regressie.
7. Train een of meer modellen met behulp van de trainings gegevensset.
8. Een score voor de validatie gegevensset met behulp van de getrainde model (len).
9. Evalueer de model (len) om de relevante metrische gegevens voor het leer probleem te berekenen.
10. Stem de model (len) af en selecteer het beste model om te implementeren.

In deze oefening hebben we de gegevens in azure Synapse Analytics al bekeken en ontworpen, en besloten over de grootte van de steek proef tot opname in Azure Machine Learning Studio (klassiek). Hier volgt de procedure voor het maken van een of meer van de Voorspellings modellen:

1. De gegevens ophalen in Azure Machine Learning Studio (klassiek) met behulp van de module [import data][import-data] , die beschikbaar is in de sectie **gegevens invoer en-uitvoer** . Zie de referentie pagina gegevens importeren [][-gegevens] module importeren voor meer informatie.

    ![Gegevens importeren in azure ML][17]
2. Selecteer **Azure SQL database** als **gegevens bron** in het deel venster **Eigenschappen** .
3. Voer de naam van de data base-DNS in het veld **database server naam** in. Formatteer `tcp:<your_virtual_machine_DNS_name>,1433`
4. Voer de **database naam** in het bijbehorende veld in.
5. Voer de *SQL-gebruikers naam* in de naam van de **Server gebruikers account** en het *wacht woord* in het **wacht woord van de server gebruikers account** in.
7. Plak in het tekst gebied **database query** bewerken de query waarmee de benodigde database velden worden geëxtraheerd (met inbegrip van berekende velden zoals de labels) en druk op voor beelden van de gegevens naar de gewenste steekproef grootte.

Een voor beeld van een experiment met binaire classificatie voor het lezen van gegevens rechtstreeks vanuit de Azure Synapse Analytics-Data Base bevindt zich in de onderstaande afbeelding (Vergeet niet om de tabel namen te vervangen nyctaxi_trip en nyctaxi_fare door de schema naam en de tabel namen die u in uw walkthrough hebt gebruikt). Vergelijk bare experimenten kunnen worden gebouwd voor classificaties en regressie problemen.

![Azure ML-trein][10]

> [!IMPORTANT]
> In de voor beelden van model gegevens extractie en bemonsterings query's in de vorige secties **zijn alle labels voor de drie model oefeningen opgenomen in de query**. Een belang rijke (vereiste) stap in elk van de modellerings oefeningen is het **uitsluiten** van de overbodige labels voor de andere twee problemen en eventuele andere **doel lekkages**. Als u bijvoorbeeld een binaire classificatie gebruikt, gebruikt u het **label en** sluit u de velden **Tip- \_ klasse**, **foois \_ hoeveelheid** en **totaal \_ bedrag** uit. Deze laatste zijn doelwit lekkages, omdat ze de fooi hebben betaald.
>
> Als u overbodige kolommen of doel lekkages wilt uitsluiten, kunt u de module [kolommen selecteren in gegevensset][select-columns] of de [meta gegevens bewerken][edit-metadata]gebruiken. Zie [kolommen selecteren in gegevensset][select-columns] en referentie pagina's voor [meta gegevens bewerken][edit-metadata] voor meer informatie.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Modellen implementeren in Azure Machine Learning
Als uw model klaar is, kunt u het eenvoudig implementeren als een webservice rechtstreeks vanuit het experiment. Zie [een Azure machine learning-webservice implementeren](../classic/deploy-a-machine-learning-web-service.md)voor meer informatie over het implementeren van Azure ml-webservices.

Als u een nieuwe webservice wilt implementeren, moet u het volgende doen:

1. Een score experiment maken.
2. Implementeer de webservice.

Als u een score experiment wilt maken op basis van een **voltooid** trainings experiment, klikt u op Score voor punten **maken** in de onderste actie balk.

![Azure-Score][18]

Azure Machine Learning probeert een score experiment te maken op basis van de onderdelen van het trainings experiment. Met name:

1. Sla het getrainde model op en verwijder de modules voor model trainingen.
2. Identificeer een logische **invoer poort** om het verwachte invoer gegevens schema weer te geven.
3. Identificeer een logische **uitvoer poort** om het verwachte uitvoer schema van de webservice weer te geven.

Wanneer het Score-experiment wordt gemaakt, bekijkt u de resultaten en brengt u de gewenste wijzigingen aan. Een typische aanpassing is het vervangen van de invoer-gegevensset of-query met een die label velden uitsluit, omdat deze label velden niet aan het schema worden toegewezen wanneer de service wordt aangeroepen. Het is ook een goed idee om de grootte van de invoer-gegevensset en/of de query te verkleinen naar enkele records, genoeg om het invoer schema aan te duiden. Voor de uitvoer poort is het gebruikelijk om alle invoer **velden uit te sluiten en alleen** de **gescoorde labels** op te nemen in de uitvoer met behulp van de module [select columns in dataset][select-columns] .

In de afbeelding hieronder vindt u een voor beeld van een score experiment. Wanneer u klaar bent om te implementeren, klikt u op de knop **PUBLISH web service** in de onderste actie balk.

![Azure ML-publicatie][11]

## <a name="summary"></a>Samenvatting
Om te samen vatting wat we in deze walkthrough zelf studie hebben gedaan, hebt u een Azure data Science-omgeving gemaakt, met een grote open bare gegevensset gewerkt, waardoor de gegevens worden verwerving naar model trainingen, en vervolgens naar de implementatie van een Azure machine learning-webservice.

### <a name="license-information"></a>Licentie gegevens
Deze voorbeeld walkthrough en de bijbehorende scripts en IPython-Notebook (s) worden door micro soft gedeeld onder de MIT-licentie. Controleer het LICENSE.txt-bestand in de map van de voorbeeld code op GitHub voor meer informatie.

## <a name="references"></a>Referenties
- [Download pagina voor Andrés Monroy NYCe taxi](https://www.andresmh.com/nyctaxitrips/)
- [De taxi-reis gegevens van NYC door Chris Whong te folie](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Onderzoek en statistieken voor NYCe taxi en limousine-Commissie](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data