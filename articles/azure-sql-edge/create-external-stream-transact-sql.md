---
title: EXTERNE STREAM maken (Transact-SQL)-Azure SQL Edge
description: Meer informatie over de instructie externe STREAM maken in Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659219"
---
# <a name="create-external-stream-transact-sql"></a>EXTERNE STREAM maken (Transact-SQL)

Het externe STREAM-object heeft een dubbel doel van zowel een invoer-als uitvoer stroom. Het kan worden gebruikt als invoer voor het uitvoeren van query's in streaminggegevens van services voor gebeurtenis opname, zoals Azure Event hub, Azure IoT Hub (of Edge hub) of Kafka, of het kan worden gebruikt als uitvoer om op te geven waar en hoe resultaten van een streaming-query moeten worden opgeslagen.

Een externe stroom kan ook worden opgegeven en gemaakt als uitvoer en invoer voor services zoals Event hub of Blob Storage. Dit vereenvoudigt het koppelen van scenario's waarbij een streaming-query de resultaten van de externe stream als uitvoer persistent maakt en een andere streaming-query die uit dezelfde externe stroom als invoer wordt gelezen.

Azure SQL Edge ondersteunt momenteel alleen de volgende gegevens bronnen als invoer en uitvoer van streams.

| Gegevens bron type | Invoer | Uitvoer | Beschrijving |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | J | J | Gegevens bron om streaminggegevens te lezen en schrijven naar een Azure IoT Edge hub. Zie [IOT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)voor meer informatie.|
| SQL Database | N | J | Gegevens bron verbinding om streaminggegevens te schrijven naar SQL Database. De data base kan een lokale Data Base zijn in Azure SQL Edge of een externe data base in SQL Server of Azure SQL Database.|
| Kafka | J | N | Gegevens bron voor het lezen van streaminggegevens uit een Kafka-onderwerp. Kafka-ondersteuning is niet beschikbaar voor de ARM64-versie van Azure SQL Edge.|



## <a name="syntax"></a>Syntaxis

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumenten

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Locatie**: Hiermee geeft u de naam op van de werkelijke gegevens of locatie in de gegevens bron. 
   - Voor Edge hub-of Kafka-stroom objecten, locatie geeft de naam van de Edge hub of het Kafka-onderwerp op waaruit moet worden gelezen of waarnaar wordt geschreven.
   - Voor de locatie van SQL Stream-objecten (SQL Server, Azure SQL Database of Azure SQL Edge) geeft u de naam van de tabel op. Als de stroom is gemaakt in dezelfde data base en hetzelfde schema als de doel tabel, dan hoeft alleen de tabel naam voldoende te zijn. Als dat niet het geval is, moet u de naam van de tabel volledig kwalificeren (<database-SCHEMA_NAME. table_name).
   - Voor Azure Blob Storage Stream-object locatie verwijst naar het pad patroon dat in de BLOB-container moet worden gebruikt. Raadpleeg voor meer informatie over deze functie (/articles/stream-Analytics/stream-Analytics-define-outputs.MD # Blob-Storage-and-Azure-data-Lake-Gen2)

- **INPUT_OPTIONS**: Geef opties op als sleutel-waardeparen voor services, zoals Kafka, IOT Edge hub die is invoer van streaming-query's
    - PARTITIES: aantal partities dat is gedefinieerd voor een onderwerp. Het maximum aantal partities dat kan worden gebruikt, is beperkt tot 32.
      - Is van toepassing op Kafka-invoer stromen
    - CONSUMER_GROUP: gebeurtenis-en IoT-hubs beperken het aantal lezers binnen één consumer groep (tot 5). Als dit veld leeg blijft, wordt de gebruikers groep ' $Default ' gebruikt.
      - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.  
    - TIME_POLICY: beschrijft of gebeurtenissen moeten worden verwijderd of de gebeurtenis tijd moet worden aangepast wanneer er gebeurtenissen of gebeurtenissen met een aflopende volg orde worden door gegeven aan de tolerantie waarde.
      - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: de Maxi maal toegestane tijds vertraging. De vertraging geeft het verschil aan tussen de tijds tempel van de gebeurtenis en de systeem klok.
      - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: gebeurtenissen kunnen buiten de juiste volg orde komen nadat ze de reis hebben gemaakt van de invoer naar de streaming-query. Deze gebeurtenissen kunnen worden geaccepteerd als-is, of u kunt ervoor kiezen om een bepaalde periode te onderbreken om ze opnieuw te rangschikken.
      - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Geef opties op als sleutel-waardeparen voor ondersteunde services die worden uitgevoerd voor het streamen van query's 
  - REJECT_POLICY: DROP | De soort nieuwe poging voor het verwerken van gegevens fouten wanneer er fouten in de gegevens conversie optreden. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - MINIMUM_ROWS:  
    Mini maal vereiste rijen per batch die naar een uitvoer wordt geschreven. Voor Parquet maakt elke batch een nieuw bestand. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - MAXIMUM_TIME:  
    Maximale wacht tijd in minuten per batch. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. 
    - Is van toepassing op alle ondersteunde uitvoer bewerkingen 
  - PARTITION_KEY_COLUMN:  
    De kolom die wordt gebruikt voor de partitie sleutel. 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Een door komma's gescheiden lijst met de namen van uitvoer kolommen die als aangepaste eigenschappen aan berichten worden toegevoegd.  
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Een JSON-indeling met naam/waarde-paren van namen en uitvoer kolommen van systeem eigenschappen die moeten worden ingevuld op Service Bus berichten. bijvoorbeeld {"MessageId": "Kolom1", "PartitionKey": "Kolom2"} 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge. 
  - PARTITION_KEY:  
    De naam van de uitvoer kolom met de partitie sleutel. De partitie sleutel is een unieke id voor de partitie in een bepaalde tabel die het eerste deel vormt van de primaire sleutel van een entiteit. Het is een teken reeks waarde die een grootte van Maxi maal 1 KB kan hebben. 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
  - ROW_KEY:  
    De naam van de uitvoer kolom met de rij-sleutel. De rij is een unieke id voor een entiteit binnen een bepaalde partitie. Het vormt het tweede deel van de primaire sleutel van een entiteit. De rij is een teken reeks waarde die een grootte van Maxi maal 1 KB kan hebben. 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.
  - BATCH_SIZE:  
    Hiermee wordt het aantal trans acties voor de tabel opslag aangegeven, waarbij het maximum tot 100 records kan gaan. Voor Azure Functions vertegenwoordigt dit de Batch grootte in bytes die worden verzonden naar de functie per aanroep. de standaard waarde is 256 kB. 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Het maximum aantal gebeurtenissen dat is verzonden naar de functie per aanroep voor Azure function: de standaard waarde is 100. Voor SQL Database vertegenwoordigt dit het maximum aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie. de standaard waarde is 10.000. 
    - Is van toepassing op alle op SQL gebaseerde uitvoer 
  - STAGING_AREA: extern gegevens bron object voor het Blob Storage van het faserings gebied voor gegevens opname met hoge door Voer in azure Synapse Analytics 
    - Gereserveerd voor toekomstig gebruik. Is niet van toepassing op Azure SQL Edge.


## <a name="examples"></a>Voorbeelden

### <a name="example-1---edgehub"></a>Voor beeld 1-EdgeHub

Type: invoer of uitvoer<br>

Syntaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Voor beeld 2-Azure SQL Database, Azure SQL Edge, SQL Server

Type: uitvoer<br>

Syntaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Voor beeld 3-Kafka

Type: invoer<br>

Syntaxis:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Zie ook

- [EXTERNE stroom verwijderen (Transact-SQL)](drop-external-stream-transact-sql.md)
