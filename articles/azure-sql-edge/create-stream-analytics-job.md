---
title: Een T-SQL streaming-taak maken in Azure SQL Edge
description: Meer informatie over het maken van Stream Analytics taken in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656339"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Een taak voor gegevens stromen maken in Azure SQL Edge 

In dit artikel wordt uitgelegd hoe u een T-SQL streaming-taak in Azure SQL Edge maakt. U maakt de externe stream-invoer-en uitvoer objecten en vervolgens definieert u de query van de streaming-taak als onderdeel van het maken van de streaming-taak.

## <a name="configure-the-external-stream-input-and-output-objects"></a>De externe stream-invoer-en uitvoer objecten configureren

T-SQL streaming maakt gebruik van de functionaliteit van de externe gegevens bron van SQL Server om de gegevens bronnen te definiëren die zijn gekoppeld aan de externe stream-invoer en uitvoer van de streaming-taak. Gebruik de volgende T-SQL-opdrachten om een externe stream-invoer of-uitvoer object te maken:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [EXTERNE STREAM maken (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Als Azure SQL Edge, SQL Server of Azure SQL Database als een uitvoer stroom wordt gebruikt, hebt u de [Data Base scoped CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)nodig. Deze T-SQL-opdracht definieert de referenties voor toegang tot de data base.

### <a name="supported-input-and-output-stream-data-sources"></a>Ondersteunde gegevens bronnen voor invoer en uitvoer stroom

Azure SQL Edge ondersteunt momenteel alleen de volgende gegevens bronnen als invoer en uitvoer van streams.

| Gegevens bron type | Invoer | Uitvoer | Beschrijving |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | J | J | Gegevens bron om streaminggegevens te lezen en schrijven naar een Azure IoT Edge hub. Zie [IOT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)voor meer informatie.|
| SQL Database | N | Y | Gegevens bron verbinding om streaminggegevens te schrijven naar SQL Database. De data base kan een lokale Data Base zijn in Azure SQL Edge of een externe data base in SQL Server of Azure SQL Database.|
| Kafka | Y | N | Gegevens bron voor het lezen van streaminggegevens uit een Kafka-onderwerp. Deze adapter is momenteel alleen beschikbaar voor Intel-of AMD-versies van Azure SQL Edge. Het is niet beschikbaar voor de ARM64-versie van Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Voor beeld: een extern stream-invoer/uitvoer-object maken voor Azure IoT Edge hub

In het volgende voor beeld wordt een extern Stream-object gemaakt voor Azure IoT Edge hub. Als u een externe stream-invoer/uitvoer-gegevens bron voor Azure IoT Edge hub wilt maken, moet u eerst een externe bestands indeling maken voor de indeling van de gegevens die worden gelezen of geschreven.

1. Een externe bestands indeling van het type JSON maken.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Een externe gegevens bron maken voor Azure IoT Edge hub. Met het volgende T-SQL-script wordt een gegevens bron verbinding gemaakt met een IoT Edge hub die wordt uitgevoerd op dezelfde docker-host als Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Maak het externe Stream-object voor Azure IoT Edge hub. Met het volgende T-SQL-script wordt een Stream-object gemaakt voor de IoT Edge hub. In het geval van een IoT Edge hub-stroom object is de locatie parameter de naam van het onderwerp van de IoT Edge hub of het kanaal dat wordt gelezen of geschreven.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Voor beeld: een extern Stream-object maken voor Azure SQL Database

In het volgende voor beeld wordt een extern Stream-object gemaakt voor de lokale data base in Azure SQL Edge. 

1. Maak een hoofd sleutel voor de data base. Dit is vereist om het referentie geheim te versleutelen.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Maak een database bereik referentie voor toegang tot de SQL Server bron. In het volgende voor beeld wordt een referentie voor de externe gegevens bron gemaakt met identiteit = username en geheim = wacht woord.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Een externe gegevens bron maken met externe gegevens bron maken. Het volgende voor beeld:

    * Hiermee maakt u een externe gegevens bron met de naam *LocalSQLOutput*.
    * Hiermee wordt de externe gegevens bron geïdentificeerd (locatie = ' <vendor> :// <server> [: <port> ] '). In het voor beeld verwijst deze naar een lokaal exemplaar van de Azure SQL-rand.
    * Maakt gebruik van de referentie die eerder is gemaakt.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Maak het externe Stream-object. In het volgende voor beeld wordt een extern Stream-object gemaakt dat verwijst naar een tabel *dbo. TemperatureMeasurements*, in de Data Base- *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Voor beeld: een extern Stream-object maken voor Kafka

In het volgende voor beeld wordt een extern Stream-object gemaakt voor de lokale data base in Azure SQL Edge. In dit voor beeld wordt ervan uitgegaan dat de Kafka-server is geconfigureerd voor anonieme toegang. 

1. Een externe gegevens bron maken met externe gegevens bron maken. Het volgende voor beeld:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Maak een externe bestands indeling voor de Kafka-invoer. In het volgende voor beeld is een JSON-bestands indeling met GZipped-compressie gemaakt. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Maak het externe Stream-object. In het volgende voor beeld wordt een extern Stream-object gemaakt dat verwijst naar het onderwerp Kafka `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>De streaming-taak en de streaming-query's maken

Gebruik de door het `sys.sp_create_streaming_job` systeem opgeslagen procedure om de streaming-query's te definiëren en de streaming-taak te maken. De `sp_create_streaming_job` opgeslagen procedure heeft de volgende para meters:

- `job_name`: De naam van de streaming-taak. De namen van streaming-taken zijn uniek binnen het exemplaar.
- `statement`: Query's op basis van streaming-query-instructies in de [query taal stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).

In het volgende voor beeld wordt een eenvoudige streaming-taak gemaakt met één streaming-query. Deze query leest de invoer van de IoT Edge hub en schrijft naar `dbo.TemperatureMeasurements` in de-data base.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

In het volgende voor beeld wordt een complexere streaming-taak gemaakt met meerdere verschillende query's. Deze query's bevatten een die gebruikmaakt van de ingebouwde `AnomalyDetection_ChangePoint` functie om afwijkingen in de gegevens van de Tempe ratuur te identificeren.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Streaming-taken starten, stoppen, neerzetten en controleren

Voer de opgeslagen procedure uit als u een streaming-taak in Azure SQL Edge wilt starten `sys.sp_start_streaming_job` . De opgeslagen procedure vereist dat de naam van de streaming-taak als invoer wordt gestart.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Voer de opgeslagen procedure uit om een streaming-taak te stoppen `sys.sp_stop_streaming_job` . Voor de opgeslagen procedure moet de naam van de streaming-taak als invoer worden gestopt.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Voer de opgeslagen procedure uit om een streaming-taak neer te zetten (of te verwijderen) `sys.sp_drop_streaming_job` . Voor de opgeslagen procedure moet de naam van de streaming-taak worden verwijderd als invoer.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Voer de opgeslagen procedure uit om de huidige status van een streaming-taak op te halen `sys.sp_get_streaming_job` . Voor de opgeslagen procedure moet de naam van de streaming-taak worden verwijderd als invoer. De naam en de huidige status van de streaming-taak worden uitgevoerd.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

De streaming-taak kan een van de volgende statussen hebben:

| Status | Beschrijving |
|--------| ------------|
| Gemaakt | De streaming-taak is gemaakt, maar is nog niet gestart. |
| Starten | De streaming-taak bevindt zich in de begin fase. |
| Niet-actief | De streaming-taak wordt uitgevoerd, maar er is geen invoer om te verwerken. |
| Wordt verwerkt | De streaming-taak wordt uitgevoerd en verwerkt de invoer. Deze status geeft aan dat de streaming-taak een goede status heeft. |
| Verminderd beschikbaar | De streaming-taak wordt uitgevoerd, maar er zijn enkele niet-fatale fouten opgetreden tijdens de invoer verwerking. De invoer taak wordt nog steeds uitgevoerd, maar gaat weg van invoer die fouten tegen komt. |
| Gestopt | De streaming-taak is gestopt. |
| Mislukt | De streaming-taak is mislukt. Dit is doorgaans een indicatie van een fatale fout tijdens de verwerking. |

## <a name="next-steps"></a>Volgende stappen

- [Meta gegevens weer geven die zijn gekoppeld aan streaming-taken in Azure SQL Edge](streaming-catalog-views.md) 
- [Een externe stroom maken](create-external-stream-transact-sql.md)