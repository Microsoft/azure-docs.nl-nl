---
title: Problemen met Azure Data Factory connectors oplossen
description: Meer informatie over het oplossen van connectorproblemen in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713422"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory connectors oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene manieren beschreven om problemen met Azure Data Factory-connectors op te lossen.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Foutcode: AzureBlobOperationFailed

- **Bericht:**'Blobbewerking is mislukt. ContainerName: %containerName;, path: %path;."

- **Oorzaak:** Een probleem met de Blob Storage bewerking.

- **Aanbeveling:** als u de foutdetails wilt controleren, [raadpleegt Blob Storage foutcodes](/rest/api/storageservices/blob-service-error-codes). Neem contact op met het team Blob Storage hulp.


### <a name="invalid-property-during-copy-activity"></a>Ongeldige eigenschap tijdens kopieeractiviteit

- **Bericht:**`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Oorzaak:** Het type dat in de gegevensset is gedefinieerd, is inconsistent met het bron- of sinktype dat is gedefinieerd in de kopieeractiviteit.

- **Oplossing:** bewerk de JSON-definitie van de gegevensset of pijplijn om de typen consistent te maken en vervolgens de implementatie opnieuw uit te proberen.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Foutbericht: Aanvraaggrootte is te groot

- **Symptomen:** wanneer u gegevens kopieert naar Azure Cosmos DB met een standaard batchgrootte voor schrijven, krijgt u de volgende foutmelding: `Request size is too large.`

- **Oorzaak:** Azure Cosmos DB de grootte van één aanvraag beperkt tot 2 MB. De formule is *aanvraaggrootte = schrijfbatchgrootte van \* één documentgrootte.* Als de documentgrootte groot is, resulteert het standaardgedrag in een aanvraaggrootte die te groot is. U kunt de grootte van de schrijfbatch afstemmen.

- **Oplossing:** verminder in de sink voor kopieeractiviteiten de waarde voor *de schrijfbatchgrootte* (de standaardwaarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Foutbericht: Schending van unieke indexbeperking

- **Symptomen:** wanneer u gegevens naar Azure Cosmos DB kopieert, wordt de volgende fout weergegeven:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Oorzaak:** Er zijn twee mogelijke oorzaken:

    - Oorzaak 1: Als  u Invoegen als schrijfgedrag gebruikt, betekent deze fout dat uw brongegevens rijen of objecten met dezelfde id hebben.
    - Oorzaak 2: Als u **Upsert** als schrijfgedrag gebruikt en u een andere unieke sleutel in de container in stelt, betekent deze fout dat uw brongegevens rijen of objecten met verschillende ID's bevatten, maar dezelfde waarde voor de gedefinieerde unieke sleutel.

- **Oplossing**: 

    - Stel voor oorzaak 1 **Upsert in** als schrijfgedrag.
    - Zorg er bij oorzaak 2 voor dat elk document een andere waarde heeft voor de gedefinieerde unieke sleutel.

### <a name="error-message-request-rate-is-large"></a>Foutbericht: Aanvraagsnelheid is groot

- **Symptomen:** wanneer u gegevens naar Azure Cosmos DB kopieert, wordt de volgende fout weergegeven:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Oorzaak:** het aantal gebruikte aanvraageenheden (AANVRAAGeenheden) is groter dan de beschikbare AANVRAAG's die zijn geconfigureerd in Azure Cosmos DB. Zie Aanvraageenheden in Azure Cosmos DB voor meer informatie over het berekenen [Azure Cosmos DB.](../cosmos-db/request-units.md#request-unit-considerations)

- **Oplossing:** probeer een van de volgende twee oplossingen:

    - *Verhoog het aantal container-RUs* naar een hogere waarde in Azure Cosmos DB. Met deze oplossing worden de prestaties van de kopieeractiviteit verbeterd, maar worden er meer kosten in Azure Cosmos DB. 
    - Verklein *writeBatchSize* tot een lagere waarde, zoals 1000, en verklein *parallelCopies* tot een lagere waarde, zoals 1. Deze oplossing vermindert de prestaties van de kopieeruitvoering, maar er worden geen extra kosten in Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Kolommen ontbreken in kolomtoewijzing

- **Symptomen:** wanneer u een schema voor een Azure Cosmos DB kolomtoewijzing importeert, ontbreken er enkele kolommen. 

- **Oorzaak:** Data Factory wordt het schema van de eerste 10 Azure Cosmos DB documenten. Als sommige documentkolommen of -eigenschappen geen waarden bevatten, wordt het schema niet gedetecteerd door Data Factory en wordt het daarom niet weergegeven.

- **Oplossing:** u kunt de query afstemmen zoals wordt weergegeven in de volgende code om af te dwingen dat de kolomwaarden met lege waarden worden weergegeven in de resultatenset. Stel dat de *onmogelijke* kolom ontbreekt in de eerste tien documenten). U kunt de kolom ook handmatig toevoegen voor toewijzing.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Foutbericht: De GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen:** wanneer u gegevens kopieert van Azure Cosmos DB MongoAPI of MongoDB met het veld Universally Unique Identifier (UUID), wordt de volgende fout weergegeven:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Oorzaak:** Er zijn twee manieren om de UUID in Binary JSON (BSON) weer te geven: UuidStardard en UuidLegacy. Standaard wordt UuidLegacy gebruikt om gegevens te lezen. U ontvangt een foutmelding als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Oplossing:** voeg in het MongoDB connection string de *optie uuidRepresentation=standard* toe. Zie [MongoDB-connection string](connector-mongodb.md#linked-service-properties)voor meer informatie.
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Foutcode: CosmosDbSqlApiOperationFailed

- **Bericht:**`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Oorzaak:** Een probleem met de bewerking CosmosDbSqlApi.

- **Aanbeveling:** als u de foutdetails wilt controleren, [raadpleegt Azure Cosmos DB helpdocument](../cosmos-db/troubleshoot-dot-net-sdk.md). Neem contact op met het team Azure Cosmos DB hulp.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Foutbericht: De onderliggende verbinding is gesloten: Kan geen vertrouwensrelatie tot stand brengen voor het beveiligde SSL/TLS-kanaal.

- **Symptomen:** Copy-activiteit mislukt met de volgende fout: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Oorzaak:** De certificaatvalidatie is mislukt tijdens de TLS-handshake.

- **Oplossing:** als tijdelijke oplossing gebruikt u de gefaseerd kopie om de validatie van Transport Layer Security (TLS) voor de Azure Data Lake Storage Gen1. U moet dit probleem reproduceren en de netwerkmonitor-trace (netmon) verzamelen en vervolgens uw netwerkteam in contact laten komen om de configuratie van het lokale netwerk te controleren.

    ![Diagram van Azure Data Lake Storage Gen1 verbindingen voor het oplossen van problemen.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Foutbericht: De externe server heeft een fout geretourneerd: (403) Verboden

- **Symptomen:** Copy-activiteit mislukken met de volgende fout: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Oorzaak:** Een mogelijke oorzaak is dat de service-principal of beheerde identiteit die u gebruikt, geen toegang heeft tot bepaalde mappen of bestanden.

- **Oplossing:** verleen de juiste machtigingen voor alle mappen en submappen die u moet kopiëren. Zie Gegevens kopiëren naar of van een Azure Data Lake Storage Gen1 met behulp [van Azure Data Factory voor meer Azure Data Factory.](connector-azure-data-lake-store.md#linked-service-properties)

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Foutbericht: Kan toegangs token niet krijgen met behulp van service-principal. ADAL-fout: service_unavailable

- **Symptomen:** Copy-activiteit mislukt met de volgende fout:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Oorzaak:** wanneer de STS (Service Token Server) die eigendom is van Azure Active Directory niet beschikbaar is, betekent dit dat het te druk is om aanvragen te verwerken en de HTTP-fout 503 wordt weergegeven. 

- **Oplossing:** de kopieeractiviteit na enkele minuten opnieuw uit te proberen.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Foutcode: ADLSGen2OperationFailed

- **Bericht:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen leiden tot deze fout. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

  | Oorzaakanalyse                                               | Aanbeveling                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Als Azure Data Lake Storage Gen2 foutmelding geeft dat een bewerking is mislukt.| Controleer het gedetailleerde foutbericht dat door de Azure Data Lake Storage Gen2. Als de fout een tijdelijke fout is, kunt u de bewerking opnieuw proberen. Neem contact op met de Azure Storage en geef de aanvraag-id op in het foutbericht. |
  | Als het foutbericht de tekenreeks 'Verboden' bevat, heeft de service-principal of beheerde identiteit die u gebruikt mogelijk niet voldoende machtigingen voor toegang tot Azure Data Lake Storage Gen2. | Zie Gegevens kopiëren en transformeren in een Azure Data Lake Storage Gen2 met behulp van Azure Data Factory om deze [fout op te lossen.](./connector-azure-data-lake-storage.md#service-principal-authentication) |
  | Als het foutbericht de tekenreeks InternalServerError bevat, wordt de fout geretourneerd door Azure Data Lake Storage Gen2. | De fout kan worden veroorzaakt door een tijdelijke fout. Als dat het geval is, voert u de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neem dan contact Azure Storage ondersteuning en geef de aanvraag-id op uit het foutbericht. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>De aanvraag voor Azure Data Lake Storage Gen2 account heeft een time-outfout veroorzaakt

- **Bericht:** 
  * Foutcode = `UserErrorFailedBlobFSOperation`
  * Foutbericht = `BlobFS operation failed for: A task was canceled.`

- **Oorzaak:** Het probleem wordt veroorzaakt door Azure Data Lake Storage Gen2 sink-time-out, die meestal optreedt op de zelf-hostende Integration Runtime (IR).

- **Aanbeveling**: 

    - Plaats uw zelf-hostende IR-computer en het doelaccount Azure Data Lake Storage Gen2 in dezelfde regio, indien mogelijk. Dit kan helpen een willekeurige time-outfout te voorkomen en betere prestaties te produceren.

    - Controleer of er een speciale netwerkinstelling is, zoals ExpressRoute, en zorg ervoor dat het netwerk voldoende bandbreedte heeft. We raden u aan de instelling Voor zelf-hostende IR gelijktijdige taken te verlagen wanneer de totale bandbreedte laag is. Zo kunt u concurrentie van netwerkresources voorkomen voor meerdere gelijktijdige taken.

    - Als de bestandsgrootte gemiddeld of klein is, gebruikt u een kleinere blokgrootte voor niet-binaire kopieën om een dergelijke time-outfout te verhelpen. Zie Put Block Blob Storage [meer informatie.](/rest/api/storageservices/put-block)

       Als u de aangepaste blokgrootte wilt opgeven, bewerkt u de eigenschap in uw JSON-bestandseditor zoals hier wordt weergegeven:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files opslag

### <a name="error-code-azurefileoperationfailed"></a>Foutcode: AzureFileOperationFailed

- **Bericht:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Oorzaak:** Een probleem met de Azure Files opslagbewerking.

- **Aanbeveling:** als u de foutdetails wilt controleren, [raadpleegt Azure Files help](/rest/api/storageservices/file-service-error-codes). Neem contact op met het Azure Files voor meer hulp.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database en SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Foutcode: SqlFailedToConnect

- **Bericht:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen leiden tot deze fout. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

    | Oorzaakanalyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Als Azure SQL bijvoorbeeld de tekenreeks SqlErrorNumber=47073 bevat, betekent dit dat openbare netwerktoegang wordt geweigerd in de connectiviteitsinstelling. | Stel op Azure SQL firewall de optie Openbare **netwerktoegang weigeren** in op *Nee.* Zie connectiviteitsinstellingen voor [Azure SQL meer informatie.](../azure-sql/database/connectivity-settings.md#deny-public-network-access) |
    | Als Azure SQL foutbericht een SQL-foutcode bevat, zoals 'SqlErrorNumber=[errorcode]', zie dan de gids Azure SQL probleemoplossing. | Zie Connectiviteitsproblemen en andere fouten met Azure SQL Database en Azure SQL Managed Instance voor [een aanbeveling.](../azure-sql/database/troubleshoot-common-errors-issues.md) |
    | Controleer of poort 1433 in de lijst met toegestane firewalls staat. | Zie Poorten die worden gebruikt [door SQL Server voor meer informatie.](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-) |
    | Als het foutbericht de tekenreeks SqlException bevat, SQL Database geeft de fout aan dat een bepaalde bewerking is mislukt. | Zoek voor meer informatie op SQL-foutcode in [Database engine errors](/sql/relational-databases/errors-events/database-engine-events-and-errors). Neem contact op met de Azure SQL voor meer hulp. |
    | Als dit een tijdelijk probleem is (bijvoorbeeld een instabele netwerkverbinding), voegt u opnieuw proberen toe aan het activiteitenbeleid om dit te verhelpen. | Zie Pijplijnen en activiteiten in Azure Data Factory voor [meer Azure Data Factory.](./concepts-pipelines-activities.md#activity-policy) |
    | Als het foutbericht de tekenreeks 'Client met IP-adres '...' bevat is niet toegestaan voor toegang tot de server. Als u verbinding probeert te maken met Azure SQL Database, wordt de fout meestal veroorzaakt door een probleem Azure SQL Database firewall. | Schakel in Azure SQL Server-firewallconfiguratie de **optie Azure-services** en -resources toegang geven tot deze server in. Zie IP-firewallregels [voor Azure SQL Database en Azure Synapse meer informatie.](../azure-sql/database/firewall-configure.md) |
    
### <a name="error-code-sqloperationfailed"></a>Foutcode: SqlOperationFailed

- **Bericht:**`A database operation failed. Please search error to get more details.`

- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen tot deze fout leiden. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

    | Oorzaakanalyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Als het foutbericht de tekenreeks SqlException bevat, SQL Database een foutmelding dat aangeeft dat een specifieke bewerking is mislukt. | Als de SQL-fout niet duidelijk is, probeert u de database te wijzigen in het meest recente compatibiliteitsniveau '150'. Het kan sql-fouten van de nieuwste versie veroorzaken. Raadpleeg de [documentatie](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat) voor meer informatie. <br/> Voor meer informatie over het oplossen van SQL-problemen zoekt u op SQL-foutcode in [Database-enginefouten.](/sql/relational-databases/errors-events/database-engine-events-and-errors) Neem contact op met de Azure SQL voor meer hulp. |
    | Als het foutbericht de tekenreeks PdwManagedToNativeInteropException bevat, wordt dit meestal veroorzaakt door een verschil tussen de grootte van de bron- en sinkkolom. | Controleer de grootte van de bron- en sinkkolommen. Neem contact op met de Azure SQL voor meer hulp. |
    | Als het foutbericht de tekenreeks InvalidOperationException bevat, wordt dit meestal veroorzaakt door ongeldige invoergegevens. | Om te bepalen welke rij het probleem heeft ondervonden, moet u de fouttolerantiefunctie inschakelen voor de kopieeractiviteit, die problematische rijen kan omleiden naar de opslag voor verder onderzoek. Zie Fouttolerantie van kopieeractiviteit [in](./copy-activity-fault-tolerance.md)Azure Data Factory. |


### <a name="error-code-sqlunauthorizedaccess"></a>Foutcode: SqlUnauthorizedAccess

- **Bericht:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Oorzaak:** De referenties zijn onjuist of het aanmeldingsaccount heeft geen toegang tot de SQL database.

- **Aanbeveling:** controleer of het aanmeldingsaccount voldoende machtigingen heeft voor toegang tot de SQL database.


### <a name="error-code-sqlopenconnectiontimeout"></a>Foutcode: SqlOpenConnectionTimeout

- **Bericht:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Oorzaak:** Het probleem kan een tijdelijke SQL database zijn.

- **Aanbeveling:** u kunt de bewerking voor het bijwerken van de gekoppelde service connection string met een grotere time-outwaarde voor de verbinding.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Foutcode: SqlAutoCreateTableTypeMapFailed

- **Bericht:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Oorzaak:** De tabel voor automatisch maken kan niet voldoen aan de bronvereiste.

- **Aanbeveling:** werk het kolomtype in *toewijzingen bij* of maak handmatig de sinktabel op de doelserver.


### <a name="error-code-sqldatatypenotsupported"></a>Foutcode: SqlDataTypeNotSupported

- **Bericht:**`A database operation failed. Check the SQL errors.`

- **Oorzaak:** als het probleem optreedt in de SQL-bron en de fout is gerelateerd aan SqlDateTime-overloop, overschrijdt de gegevenswaarde het logische typebereik (1-1-1753 12:00:00 - 31-12-9999 11:59:59 uur).

- **Aanbeveling:** cast het type naar de tekenreeks in de SQL-bronquery of wijzig in de kolomtoewijzing voor kopieeractiviteit het kolomtype in *Tekenreeks*.

- **Oorzaak:** als het probleem optreedt op de SQL-sink en de fout is gerelateerd aan SqlDateTime-overloop, overschrijdt de gegevenswaarde het toegestane bereik in de sinktabel.

- **Aanbeveling:** werk het bijbehorende kolomtype bij naar het *type datetime2* in de sinktabel.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Foutcode: SqlInvalidDbStoredProcedure

- **Bericht:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Oorzaak:** De opgegeven opgeslagen procedure is ongeldig. De oorzaak kan zijn dat de opgeslagen procedure geen gegevens retourneerde.

- **Aanbeveling:** Valideer de opgeslagen procedure met behulp van SQL Tools. Zorg ervoor dat de opgeslagen procedure gegevens kan retourneren.


### <a name="error-code-sqlinvaliddbquerystring"></a>Foutcode: SqlInvalidDbQueryString

- **Bericht:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Oorzaak:** De opgegeven SQL-query is ongeldig. De oorzaak kan zijn dat de query geen gegevens retournt.

- **Aanbeveling:** Valideer de SQL-query met behulp van SQL Tools. Zorg ervoor dat de query gegevens kan retourneren.


### <a name="error-code-sqlinvalidcolumnname"></a>Foutcode: SqlInvalidColumnName

- **Bericht:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Oorzaak:** De kolom kan niet worden gevonden omdat de configuratie mogelijk onjuist is.

- **Aanbeveling:** controleer de kolom in de query, *structuur* in de gegevensset en *toewijzingen* in de activiteit.


### <a name="error-code-sqlbatchwritetimeout"></a>Foutcode: SqlBatchWriteTimeout

- **Bericht:**`Timeouts in SQL write operation.`

- **Oorzaak:** Het probleem kan worden veroorzaakt door SQL database tijdelijke fout.

- **Aanbeveling:** de bewerking opnieuw uitvoeren. Als het probleem zich blijft voordoen, kunt u contact opnemen Azure SQL ondersteuning.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Foutcode: SqlBatchWriteTransactionFailed

- **Bericht:**`SQL transaction commits failed.`

- **Oorzaak:** als uitzonderingsdetails voortdurend duiden op een time-out van een transactie, is de netwerklatentie tussen de integratieruntime en de database groter dan de standaarddrempelwaarde van 30 seconden.

- **Aanbeveling:** werk de aan SQL gekoppelde service-connection string bij met een *time-outwaarde* voor de verbinding die gelijk is aan of groter is dan 120 en opnieuw de activiteit.

- **Oorzaak:** Als de uitzonderingsdetails af en toe aangeven dat de SQL-verbinding is verbroken, kan dit een tijdelijke netwerkfout of een probleem SQL database zijn.

- **Aanbeveling:** de activiteit opnieuw proberen en de metrische gegevens aan SQL database kant controleren.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Foutcode: SqlBulkCopyInvalidColumnLength

- **Bericht:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Oorzaak:** HET bulksgewijs kopiëren van SQL is mislukt omdat deze een ongeldige kolomlengte heeft ontvangen van de BCP-client (Bulk Copy Program Utility).

- **Aanbeveling:** schakel de fouttolerantiefunctie voor de kopieeractiviteit in om te bepalen in welke rij het probleem is aangetroffen. Dit kan problematische rijen omleiden naar de opslag voor verder onderzoek. Zie Fouttolerantie van kopieeractiviteit [in](./copy-activity-fault-tolerance.md)Azure Data Factory.


### <a name="error-code-sqlconnectionisclosed"></a>Foutcode: SqlConnectionIsClosed

- **Bericht:**`The connection is closed by SQL Database.`

- **Oorzaak:** de SQL-verbinding wordt gesloten door de SQL database wanneer een hoge gelijktijdige run wordt uitgevoerd en de server de verbinding beëindigt.

- **Aanbeveling:** de verbinding opnieuw proberen. Als het probleem zich blijft voordoen, kunt u contact opnemen Azure SQL ondersteuning.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Foutbericht: Conversie is mislukt bij het converteren van een tekenreeks naar uniqueidentifier

- **Symptomen:** wanneer u gegevens uit een gegevensbron in tabelvorm (zoals SQL Server) naar Azure Synapse Analytics kopieert met behulp van gefaseerd kopiëren en PolyBase, wordt de volgende fout weergegeven:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Oorzaak:** Azure Synapse Analytics PolyBase kan een lege tekenreeks niet converteren naar een GUID.

- **Oplossing:** stel in de sink voor kopieeractiviteiten, onder PolyBase-instellingen, de standaardoptie voor het **gebruikstype** in op *false*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Foutbericht: Verwacht gegevenstype: DECIMAL(x,x), Foutwaarde

- **Symptomen:** wanneer u gegevens uit een gegevensbron in tabelvorm (zoals SQL Server) naar Azure Synapse Analytics kopieert met behulp van gefaseerd kopiëren en PolyBase, wordt de volgende fout weergegeven:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Oorzaak:** Azure Synapse Analytics PolyBase kan geen lege tekenreeks (null-waarde) invoegen in een decimale kolom.

- **Oplossing:** Stel in de sink voor kopieeractiviteit, onder PolyBase-instellingen, de standaardoptie voor het **gebruikstype** in op false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Foutbericht: Java-uitzonderingsbericht: HdfsBridge::CreateRecordReader

- **Symptomen:** u kopieert gegevens naar Azure Synapse Analytics polybase en ontvangt de volgende fout:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Oorzaak:** De oorzaak kan zijn dat het schema (totale kolombreedte) te groot is (groter dan 1 MB). Controleer het schema van de doeltabel Azure Synapse Analytics door de grootte van alle kolommen toe te voegen:

    - Int = 4 bytes
    - Bigint = 8 bytes
    - Varchar(n), char(n), binary(n), varbinary(n) = n bytes
    - Nvarchar(n), nchar(n) = n*2 bytes
    - Datum = 6 bytes
    - Datetime/(2), smalldatetime = 16 bytes
    - Datetimeoffset = 20 bytes
    - Decimaal = 19 bytes
    - Float = 8 bytes
    - Geld = 8 bytes
    - Smallmoney = 4 bytes
    - Echt = 4 bytes
    - Smallint = 2 bytes
    - Tijd = 12 bytes
    - Tinyint = 1 byte

- **Oplossing**: 
    - Verklein de kolombreedte tot minder dan 1 MB.
    - Of gebruik een benadering voor bulksgewijs invoegen door PolyBase uit te uitschakelen.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Foutbericht: Er wordt niet voldaan aan de voorwaarde die is opgegeven met behulp van voorwaardelijke HTTP-header(s)

- **Symptomen:** u gebruikt SQL-query om gegevens op te halen uit Azure Synapse Analytics en de volgende fout te ontvangen:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Oorzaak:** er Azure Synapse Analytics een probleem aangetroffen tijdens het uitvoeren van een query op de externe tabel in Azure Storage.

- **Oplossing:** voer dezelfde query uit in SQL Server Management Studio (SSMS) en controleer of u hetzelfde resultaat krijgt. Als u dit doet, opent u een ondersteuningsticket om Azure Synapse Analytics en geeft u uw Azure Synapse Analytics server en databasenaam op.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>De prestatielaag is laag en leidt tot kopieerfout

- **Symptomen:** u kopieert gegevens naar Azure SQL Database en ontvangt de volgende fout: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Oorzaak:** Azure SQL Database s1 heeft I/O-limieten (input/output) bereikt.

- **Oplossing:** upgrade de Azure SQL Database prestatielaag om het probleem op te lossen. 


### <a name="sql-table-cant-be-found"></a>SQL-tabel kan niet worden gevonden 

- **Symptomen:** u kopieert gegevens van hybride naar een on-premises SQL Server tabel en ontvangt de volgende fout:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Oorzaak:** Het huidige SQL-account heeft onvoldoende machtigingen om aanvragen uit te voeren die zijn uitgegeven door .NET SqlBulkCopy.WriteToServer.

- **Oplossing:** schakel over naar een SQL-account met meer bevoegdheden.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Foutbericht: Tekenreeks of binaire gegevens worden afgekapt

- **Symptomen:** er treedt een fout op wanneer u gegevens kopieert naar een on-premises Azure SQL Server-tabel. 

- **Oorzaak:** De Cx SQL-tabelschemadefinitie heeft een of meer kolommen met minder lengte dan verwacht.

- **Oplossing:** probeer het volgende om het probleem op te lossen:

    1. Als u wilt oplossen welke rijen [](./copy-activity-fault-tolerance.md)het probleem hebben, moet u SQL Sink-fouttolerantie toepassen, met name 'redirectIncompatibleRowSettings'.

        > [!NOTE]
        > Fouttolerantie kan extra uitvoeringstijd vereisen, wat kan leiden tot hogere kosten.

    2. Controleer de omgeleide gegevens op de kolomlengte van het SQL-tabelschema om te zien welke kolommen moeten worden bijgewerkt.

    3. Werk het tabelschema dienovereenkomstig bij.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Foutcode: AzureTableDuplicateColumnsFromSource

- **Bericht:**`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Oorzaak:** Dubbele bronkolommen kunnen om een van de volgende redenen optreden:
   * U gebruikt de database als bron en toegepaste tabel-joins.
   * U hebt ongestructureerde CSV-bestanden met dubbele kolomnamen in de koprij.

- **Aanbeveling:** controleer de bronkolommen en herstel deze, indien nodig.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Foutcode: DB2DriverRunFailed

- **Bericht:**`Error thrown from driver. Sql code: '%code;'`

- **Oorzaak:** als het foutbericht de tekenreeks SQLSTATE=51002 SQLCODE=-805 bevat, volgt u de tip in Gegevens kopiëren uit [DB2](./connector-db2.md#linked-service-properties)met behulp van Azure Data Factory .

- **Aanbeveling:** probeer NULLID in te stellen in de `packageCollection`  eigenschap .


## <a name="delimited-text-format"></a>Tekstindeling met scheidingstekens

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Foutcode: DelimitedTextColumnNameNotAllowNull

- **Bericht:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Oorzaak:** wanneer 'firstRowAsHeader' is ingesteld in de activiteit, wordt de eerste rij gebruikt als kolomnaam. Deze fout betekent dat de eerste rij een lege waarde bevat (bijvoorbeeld 'ColumnA, ColumnB').

- **Aanbeveling:** controleer de eerste rij en herstel de waarde als deze leeg is.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Foutcode: DelimitedTextMoreColumnsThanDefined

- **Bericht:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen leiden tot deze fout. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

  | Oorzaakanalyse                                               | Aanbeveling                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Het kolomtelling van de problematische rij is groter dan het aantal kolommen van de eerste rij. Dit kan worden veroorzaakt door een gegevensprobleem of onjuiste kolom scheidingstekens of char-instellingen aan te geven. | Haal het aantal rijen op uit het foutbericht, controleer de kolom van de rij en los de gegevens op. |
  | Als het verwachte aantal kolommen '1' is in een foutbericht, hebt u mogelijk verkeerde compressie- of indelingsinstellingen opgegeven, waardoor Data Factory bestanden onjuist zijn geparseerd. | Controleer de indelingsinstellingen om te controleren of deze overeenkomen met uw bronbestanden. |
  | Als uw bron een map is, hebben de bestanden onder de opgegeven map mogelijk een ander schema. | Zorg ervoor dat de bestanden in de opgegeven map een identiek schema hebben. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service en Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Foutcode: DynamicsCreateServiceClientError

- **Bericht:**`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Oorzaak:** Het probleem is een tijdelijk probleem aan de dynamics-serverzijde.

- **Aanbeveling:** de pijplijn opnieuw uit te proberen. Als het opnieuw mislukt, probeert u de parallellelisme te verminderen. Neem contact op met dynamics-ondersteuning als het probleem zich blijft voordoen.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Ontbrekende kolommen wanneer u een schema importeert of een voorbeeld van gegevens bekijkt

- **Symptomen:** sommige kolommen ontbreken wanneer u een schema importeert of een voorbeeld van gegevens bekijkt. Foutbericht: `The valid structure information (column name and type) are required for Dynamics source.`

- **Oorzaak:** Dit probleem is een ontwerp, omdat Data Factory kolommen die geen waarden bevatten in de eerste tien records niet kunnen tonen. Zorg ervoor dat de kolommen die u hebt toegevoegd, de juiste indeling hebben. 

- **Aanbeveling:** voeg de kolommen handmatig toe op het tabblad Toewijzing.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Foutcode: DynamicsMissingTargetForMultiTargetLookupField

- **Bericht:**`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Oorzaak:** De doelkolom bestaat niet in de bron of in de kolomtoewijzing.

- **Aanbeveling**:  
  1. Zorg ervoor dat de bron de doelkolom bevat. 
  2. Voeg de doelkolom toe aan de kolomtoewijzing. Zorg ervoor dat de sinkkolom de indeling *{fieldName} heeft. @EntityReference*


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Foutcode: DynamicsInvalidTargetForMultiTargetLookupField

- **Bericht:**`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Oorzaak:** Er wordt een verkeerde entiteitsnaam opgegeven als doelentiteit van een opzoekveld voor meerdere doel.

- **Aanbeveling:** geef een geldige entiteitsnaam op voor het zoekveld voor meerdere doel.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Foutcode: DynamicsInvalidTypeForMultiTargetLookupField

- **Bericht:**`The provided target type is not a valid string. Field: '%fieldName;'.`

- **Oorzaak:** De waarde in de doelkolom is geen tekenreeks.

- **Aanbeveling:** Geef een geldige tekenreeks op in de doelkolom met meerdere doelzoekers.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Foutcode: DynamicsFailedToRequetServer

- **Bericht:**`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Oorzaak:** De Dynamics-server is niet toegankelijk of niet toegankelijk, of het netwerk ondervindt problemen.

- **Aanbeveling:** controleer voor meer informatie de netwerkverbinding of controleer het logboek van de Dynamics-server. Neem contact op met dynamics-ondersteuning voor meer hulp.


### <a name="error-code--dynamicsfailedtoconnect"></a>Foutcode: DynamicsFailedToConnect 
 
 - **Bericht:**`Failed to connect to Dynamics: %message;` 
 

 - **Oorzaak:** Als u in het foutbericht ziet, betekent dit dat uw server mogelijk bepaalde configuraties heeft die `Office 365 auth with OAuth failed` niet compatibel zijn met OAuth. 
 
 - **Aanbeveling**: 
    1. Neem contact op met het dynamics-ondersteuningsteam met het gedetailleerde foutbericht voor hulp.  
    1. Gebruik de service-principal-verificatie en raadpleeg dit [artikel: Voorbeeld: Dynamics online met behulp van azure AD-service-principal en certificaatverificatie.](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication) 
 

 - **Oorzaak:** Als u in het foutbericht ziet, betekent dit dat u de verkeerde URL van de Dynamics-service of proxy/firewall invoert om het verkeer `Unable to retrieve authentication parameters from the serviceUri` te onderscheppen. 
 
 - **Aanbeveling**:
    1. Zorg ervoor dat u de juiste service-URI in de gekoppelde service hebt gezet. 
    1. Als u de zelf-hostende IR gebruikt, moet u ervoor zorgen dat de firewall/proxy de aanvragen naar de Dynamics-server niet onderscheppen. 
   
 
 - **Oorzaak:** Als u in het foutbericht ziet, betekent dit dat er onverwachte reacties `An unsecured or incorrectly secured fault was received from the other party` zijn ontvangen vanaf de serverzijde. 
 
 - **Aanbeveling**: 
    1. Zorg ervoor dat uw gebruikersnaam en wachtwoord juist zijn als u de Office 365-verificatie gebruikt. 
    1. Zorg ervoor dat u de juiste service-URI hebt ingevoerd. 
    1. Als u een regionale CRM-URL gebruikt (URL heeft een getal na crm), moet u ervoor zorgen dat u de juiste regionale id gebruikt.
    1. Neem contact op met het ondersteuningsteam van Dynamics voor hulp. 
 

 - **Oorzaak:** Als u in het foutbericht ziet, betekent dit dat de naam van uw organisatie onjuist is of dat u een verkeerde CRM-regio-id hebt gebruikt `No Organizations Found` in de service-URL. 
 
 - **Aanbeveling**: 
    1. Zorg ervoor dat u de juiste service-URI hebt ingevoerd.
    1. Als u de regionale CRM-URL gebruikt (URL heeft een getal na crm), moet u ervoor zorgen dat u de juiste regionale id gebruikt. 
    1. Neem contact op met het ondersteuningsteam van Dynamics voor hulp. 

 
 - **Oorzaak:** als u een foutbericht met betrekking tot AAD ziet, betekent dit dat er `401 Unauthorized` een probleem is met de service-principal. 

 - **Aanbeveling:** volg de richtlijnen in het foutbericht om het probleem met de service-principal op te lossen.  
 
 
 - **Oorzaak:** Voor andere fouten is het probleem meestal aan de serverzijde. 

 - **Aanbeveling:** gebruik [XrmToolBox om](https://www.xrmtoolbox.com/) verbinding te maken. Als de fout zich blijft voordoen, neem dan contact op met het Dynamics-ondersteuningsteam voor hulp. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Foutcode: DynamicsOperationFailed 
 
- **Bericht:**`Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Oorzaak:** De bewerking is mislukt aan de serverzijde. 

- **Aanbeveling:** extraheert de foutcode van de dynamics-bewerking uit het foutbericht: , en raadpleeg het artikel Foutcodes voor `Dynamics operation failed with error code: {code}` webservice voor meer gedetailleerde informatie. [](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) U kunt indien nodig contact opnemen met het ondersteuningsteam van Dynamics. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Foutcode: DynamicsInvalidFetchXml 
  
- **Bericht:**`The Fetch Xml query specified is invalid.` 

- **Oorzaak:** Er is een fout opgetreden in het XML-bestand ophalen.  

- **Aanbeveling:** los de fout in het XML-bestand ophalen op. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Foutcode: DynamicsMissingKeyColumns 
 
- **Bericht:**`Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Oorzaak:** De brongegevens bevatten niet de sleutelkolom voor de sinkentiteit. 

- **Aanbeveling:** Controleer of de sleutelkolommen zich in de brongegevens of wijs een bronkolom toe aan de sleutelkolom van de sink-entiteit. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Foutcode: DynamicsPrimaryKeyMustBeGuid 
 
- **Bericht:**`The primary key attribute '%attribute;' must be of type guid.` 
 
- **Oorzaak:** Het type primaire sleutelkolom is niet 'Guid'. 
 
- **Aanbeveling:** zorg ervoor dat de primaire sleutelkolom in de brongegevens van het type 'Guid' is. 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Foutcode: DynamicsAlternateKeyNotFound 
 
- **Bericht:**`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Oorzaak:** De opgegeven alternatieve sleutel bestaat niet, wat kan worden veroorzaakt door verkeerde sleutelnamen of onvoldoende machtigingen. 
 
- **Aanbeveling**: <br/> 
    1. Typfouten in de sleutelnaam zijn opgelost.<br/> 
    1. Zorg ervoor dat u voldoende machtigingen hebt voor de entiteit. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Foutcode: DynamicsInvalidSchemaDefinition 
 
- **Bericht:**`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Oorzaak:** Sinkkolommen in de kolomtoewijzing missen de eigenschap 'type'. 
 
- **Aanbeveling:** u kunt de eigenschap 'type' toevoegen aan die kolommen in de kolomtoewijzing met behulp van de JSON-editor in de portal. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Foutcode: FtpFailedToConnectToFtpServer

- **Bericht:**`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Oorzaak:** Er kan een onjuist type gekoppelde service worden gebruikt voor de FTP-server, zoals het gebruik van de gekoppelde SFTP-service (Secure FTP) om verbinding te maken met een FTP-server.

- **Aanbeveling:** Controleer de poort van de doelserver. FTP maakt gebruik van poort 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Foutcode: HttpFileFailedToRead

- **Bericht:**`Failed to read data from http server. Check the error from http server：%message;`

- **Oorzaak:** Deze fout treedt op Azure Data Factory met de HTTP-server, maar de HTTP-aanvraagbewerking mislukt.

- **Aanbeveling:** Controleer de HTTP-statuscode in het foutbericht en los het probleem met de externe server op.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Foutcode: ArgumentOutOfRangeException

- **Bericht:**`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Oorzaak:** In Data Factory worden datum/tijd-waarden ondersteund in het bereik van 0001-01-01 00:00:00 tot 9999-12-31 23:59:59. Oracle ondersteunt echter een breder scala aan datum/tijd-waarden, zoals de bc century of min/sec>59, wat leidt tot fouten in Data Factory.

- **Aanbeveling**: 

    Voer uit om te zien of de waarde in Oracle binnen het bereik van Data Factory `select dump(<column name>)` valt. 

    Zie Hoe worden datums opgeslagen in Oracle? voor meer informatie over de bytereeks [in het resultaat.](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)


## <a name="orc-format"></a>ORC-indeling

### <a name="error-code-orcjavainvocationexception"></a>Foutcode: OrcJavaInvocationException

- **Bericht:**`An error occurred when invoking Java, message: %javaException;.`
- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen leiden tot deze fout. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

    | Oorzaakanalyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wanneer het foutbericht de tekenreeksen java.lang.OutOfMemory, Java heap space en doubleCapacity bevat, is dit meestal een probleem met geheugenbeheer in een oude versie van Integration Runtime. | Als u zelf-hostend gebruik Integration Runtime, raden we u aan om een upgrade uit te voeren naar de nieuwste versie. |
    | Wanneer het foutbericht de tekenreeks java.lang.OutOfMemory bevat, heeft de integration runtime onvoldoende resources om de bestanden te verwerken. | Beperk de gelijktijdige runs op de integratieruntime. Voor zelf-hostende IR kunt u omhoog schalen naar een krachtige machine met geheugen dat gelijk is aan of groter is dan 8 GB. |
    |Wanneer het foutbericht de tekenreeks NullPointerReference bevat, kan de oorzaak een tijdelijke fout zijn. | Voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neem dan contact op met de ondersteuning. |
    | Wanneer het foutbericht de tekenreeks BufferOverflowException bevat, kan de oorzaak een tijdelijke fout zijn. | Voer de bewerking opnieuw uit. Neem contact op met de ondersteuning als het probleem zich blijft voordoen. |
    | Wanneer het foutbericht de tekenreeks 'java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text' bevat, kan de oorzaak een probleem met typeconversie in Java Runtime zijn. Meestal betekent dit dat de brongegevens niet goed kunnen worden verwerkt in Java Runtime. | Dit is een gegevensprobleem. Probeer een tekenreeks te gebruiken in plaats van teken of varchar in ORC-indeling. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Foutcode: OrcDateTimeExceedLimit

- **Bericht:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Oorzaak**: Als de datum/tijd-waarde '0001-01-01 00:00:00' is, kan dit worden veroorzaakt door de verschillen tussen de Agenda Van Nu en de [Hebtoaanse kalender](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Aanbeveling:** Controleer de ticks-waarde en vermijd het gebruik van de datum/tijd-waarde '0001-01-01 00:00:00'.


## <a name="parquet-format"></a>Parquet-indeling

### <a name="error-code-parquetjavainvocationexception"></a>Foutcode: ParquetJavaInvocationException

- **Bericht:**`An error occurred when invoking java, message: %javaException;.`

- **Oorzaken en aanbevelingen:** Verschillende oorzaken kunnen leiden tot deze fout. Controleer de onderstaande lijst op mogelijke oorzaakanalyses en gerelateerde aanbevelingen.

    | Oorzaakanalyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wanneer het foutbericht de tekenreeksen 'java.lang.OutOfMemory', 'Java heap space' en 'doubleCapacity' bevat, is het meestal een probleem met geheugenbeheer in een oude versie van Integration Runtime. | Als u zelf-hostende IR gebruikt en de versie lager is dan 3.20.7159.1, wordt u aangeraden een upgrade uit te voeren naar de nieuwste versie. |
    | Wanneer het foutbericht de tekenreeks java.lang.OutOfMemory bevat, heeft de integration runtime onvoldoende resources om de bestanden te verwerken. | Beperk de gelijktijdige runs op de integratieruntime. Voor zelf-hostende IR kunt u omhoog schalen naar een krachtige machine met geheugen dat gelijk is aan of groter is dan 8 GB. |
    | Wanneer het foutbericht de tekenreeks NullPointerReference bevat, kan het een tijdelijke fout zijn. | Voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neem dan contact op met de ondersteuning. |

### <a name="error-code-parquetinvalidfile"></a>Foutcode: ParquetInvalidFile

- **Bericht:**`File is not a valid Parquet file.`

- **Oorzaak:** Dit is een probleem met een Parquet-bestand.

- **Aanbeveling:** controleer of de invoer een geldig Parquet-bestand is.


### <a name="error-code-parquetnotsupportedtype"></a>Foutcode: ParquetNotSupportedType

- **Bericht:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Oorzaak:** De Parquet-indeling wordt niet ondersteund in Azure Data Factory.

- **Aanbeveling:** controleer de brongegevens door naar Ondersteunde [bestandsindelingen en compressiecodecs te](./supported-file-formats-and-compression-codecs.md)gaan door de activiteit te kopiëren in Azure Data Factory .


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Foutcode: ParquetMissedDecimalPrecisionScale

- **Bericht:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Oorzaak:** Het aantal precisie en schaal zijn geparseerd, maar dergelijke informatie is niet opgegeven.

- **Aanbeveling:** De bron retourneert niet de juiste precisie- en schaalgegevens. Controleer de kolom probleem op de informatie.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Foutcode: ParquetInvalidDecimalPrecisionScale

- **Bericht:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Oorzaak:** Het schema is ongeldig.

- **Aanbeveling:** Controleer de probleemkolom op precisie en schaal.


### <a name="error-code-parquetcolumnnotfound"></a>Foutcode: ParquetColumnNotFound

- **Bericht:**`Column %column; does not exist in Parquet file.`

- **Oorzaak:** Het bronschema komt niet overeen met het sinkschema.

- **Aanbeveling:** Controleer de toewijzingen in de activiteit. Zorg ervoor dat de bronkolom kan worden toegesneden op de juiste sinkkolom.


### <a name="error-code-parquetinvaliddataformat"></a>Foutcode: ParquetInvalidDataFormat

- **Bericht:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Oorzaak:** De gegevens kunnen niet worden geconverteerd naar het type dat is opgegeven in mappings.source.

- **Aanbeveling:** controleer de brongegevens of geef het juiste gegevenstype voor deze kolom op in de kolomtoewijzing voor kopieeractiviteit. Zie Ondersteunde [bestandsindelingen en compressiecodecs](./supported-file-formats-and-compression-codecs.md)door kopieeractiviteit in Azure Data Factory.


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Foutcode: ParquetDataCountNotMatchColumnCount

- **Bericht:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Oorzaak:** Een verschil tussen het aantal bronkolomken en het aantal sinkkolomken.

- **Aanbeveling:** controleer of het aantal bronkolomken hetzelfde is als het aantal sinkkolomken in 'toewijzing'.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Foutcode: ParquetDataTypeNotMatchColumnType

- **Bericht:**`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Oorzaak:** De gegevens uit de bron kunnen niet worden geconverteerd naar het type dat in de sink is gedefinieerd.

- **Aanbeveling:** geef het juiste type op in mapping.sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Foutcode: ParquetBridgeInvalidData

- **Bericht:**`%message;`

- **Oorzaak:** De gegevenswaarde heeft de limiet overschreden.

- **Aanbeveling:** de bewerking opnieuw uitvoeren. Als het probleem zich blijft voordoen, kunt u contact met ons opnemen.


### <a name="error-code-parquetunsupportedinterpretation"></a>Foutcode: ParquetUnsupportedInterpretation

- **Bericht:**`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Oorzaak:** Dit scenario wordt niet ondersteund.

- **Aanbeveling:**'ParquetInterpretFor' mag niet 'sparkSql' zijn.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Foutcode: ParquetUnsupportFileLevelCompressionOption

- **Bericht:**`File level compression is not supported for Parquet.`

- **Oorzaak:** Dit scenario wordt niet ondersteund.

- **Aanbeveling:** Verwijder 'CompressionType' in de nettolading.


### <a name="error-code-usererrorjniexception"></a>Foutcode: UserErrorJniException

- **Bericht:**`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Oorzaak:** een Java Virtual Machine (JVM) kan niet worden gemaakt omdat er enkele onrechtmatige (globale) argumenten zijn ingesteld.

- **Aanbeveling:** meld u aan bij de computer die als host *voor elk knooppunt* van uw zelf-hostende IR wordt gebruikt. Controleer als volgt of de systeemvariabele juist is ingesteld: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Start alle IR-knooppunten opnieuw op en vervolgens de pijplijn opnieuw uit.


### <a name="arithmetic-overflow"></a>Rekenkundige overloop

- **Symptomen:** Er is een foutbericht opgetreden bij het kopiëren van Parquet-bestanden: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Oorzaak:** Momenteel worden alleen de decimale precisie <= 38 en de lengte van het gehele getal <= 20 ondersteund wanneer u bestanden van Oracle naar Parquet kopieert. 

- **Oplossing:** als tijdelijke oplossing kunt u kolommen met dit probleem converteren naar VARCHAR2.


### <a name="no-enum-constant"></a>Geen enumconstante

- **Symptomen:** Er is een foutbericht opgetreden wanneer u gegevens kopieert naar parquet-indeling: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , of: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Oorzaak:** 

    Het probleem kan worden veroorzaakt door spaties of niet-ondersteunde speciale tekens (zoals; {} ()\n\t=) in de kolomnaam, omdat Parquet een dergelijke indeling niet ondersteunt. 

    Een kolomnaam zoals *contoso(test)* parseert bijvoorbeeld het type tussen haakjes van [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . De fout wordt opgetreden omdat er geen dergelijk testtype is.

    Als u ondersteunde typen wilt controleren, gaat u naar de [gitHub-site apache/parquet-mr.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)

- **Oplossing**: 

    Controleer of:
    - De naam van de sinkkolom heeft spaties.
    - De eerste rij met spaties wordt gebruikt als kolomnaam.
    - Het type OriginalType wordt ondersteund. Vermijd het gebruik van deze speciale tekens: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Foutcode: RestSinkCallFailed

- **Bericht:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Oorzaak:** Deze fout treedt op Azure Data Factory met het REST-eindpunt via het HTTP-protocol en de aanvraagbewerking mislukt.

- **Aanbeveling:** controleer de HTTP-statuscode of het bericht in het foutbericht en los het probleem met de externe server op.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Onverwacht netwerkreactie van de REST-connector

- **Symptomen:** het eindpunt ontvangt soms een onverwacht antwoord (400, 401, 403, 500) van de REST-connector.

- **Oorzaak:** De REST-bronconnector gebruikt de URL en HTTP-methode/header/body van de gekoppelde service/gegevensset/kopieerbron als parameters wanneer deze een HTTP-aanvraag samenmaakt. Het probleem wordt waarschijnlijk veroorzaakt door een aantal fouten in een of meer opgegeven parameters.

- **Oplossing**: 
    - Gebruik 'curl' in een opdrachtpromptvenster om te zien of de parameter de oorzaak is **(Accept** en **User-Agent** headers moeten altijd worden opgenomen):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Als de opdracht hetzelfde onverwachte antwoord retourneert, herstelt u de voorgaande parameters met 'curl' totdat het verwachte antwoord wordt retourneert. 

      U kunt ook 'curl--help' gebruiken voor geavanceerder gebruik van de opdracht.

    - Als alleen de Data Factory REST-connector een onverwacht antwoord retourneert, kunt u contact opnemen met Microsoft Ondersteuning voor verdere probleemoplossing.
    
    - Houd er rekening mee dat 'curl' mogelijk niet geschikt is om een validatieprobleem met een SSL-certificaat te reproduceren. In sommige scenario's is de opdracht curl uitgevoerd zonder problemen met de validatie van SSL-certificaten. Maar wanneer dezelfde URL wordt uitgevoerd in een browser, wordt er geen SSL-certificaat geretourneerd voor de client om een vertrouwensrelatie met de server tot stand te gebracht.

      **Hulpprogramma's zoals Postman** en **Fiddler** worden aanbevolen voor de voorgaande case.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Foutcode: SftpOperationFail

- **Bericht:**`Failed to '%operation;'. Check detailed error from SFTP.`

- **Oorzaak:** Een probleem met de SFTP-bewerking.

- **Aanbeveling:** controleer de foutdetails van SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Foutcode: SftpRenameOperationFail

- **Bericht:**`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Oorzaak:** De SFTP-server biedt geen ondersteuning voor het wijzigen van de naam van het tijdelijke bestand.

- **Aanbeveling:** stel useTempFileRename in de sink voor kopiëren in als onwaar om het uploaden naar het tijdelijke bestand uit te schakelen.


### <a name="error-code-sftpinvalidsftpcredential"></a>Foutcode: SftpInvalidSftpCredential

- **Bericht:**`Invalid SFTP credential provided for '%type;' authentication type.`

- **Oorzaak:** Inhoud van persoonlijke sleutel wordt opgehaald uit de Azure-sleutelkluis of SDK, maar deze is niet correct gecodeerd.

- **Aanbeveling**:  

    Als de inhoud van de persoonlijke sleutel afkomstig is uit uw sleutelkluis, kan het oorspronkelijke sleutelbestand werken als u het rechtstreeks uploadt naar de gekoppelde SFTP-service.

    Zie Gegevens kopiëren van en naar de [SFTP-server](./connector-sftp.md#use-ssh-public-key-authentication)met behulp van Azure Data Factory voor Azure Data Factory. De inhoud van de persoonlijke sleutel is inhoud met base64-gecodeerde persoonlijke SSH-sleutel.

    Codeer *het hele* oorspronkelijke persoonlijke sleutelbestand met base64-codering en sla de gecodeerde tekenreeks op in uw sleutelkluis. Het oorspronkelijke persoonlijke sleutelbestand is het bestand dat kan worden gebruikt voor de gekoppelde SFTP-service als u **Uploaden uit het** bestand selecteert.

    Hier zijn enkele voorbeelden die u kunt gebruiken om de tekenreeks te genereren:

    - C#-code gebruiken:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python-code gebruiken:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Gebruik een base64-conversieprogramma van derden. U wordt [aangeraden het hulpprogramma Coderen naar Base64-indeling te](https://www.base64encode.org/) gebruiken.

- **Oorzaak:** De verkeerde inhoudsindeling voor de sleutel is gekozen.

- **Aanbeveling**:  

    Persoonlijke SSH-sleutel in PKCS#8-indeling (beginnend met '-----BEGIN ENCRYPTED PRIVATE KEY-----') wordt momenteel niet ondersteund voor toegang tot de SFTP-server in Data Factory. 

    Voer de volgende opdrachten uit om de sleutel te converteren naar een traditionele SSH-sleutelindeling, te beginnen met '-----BEGIN RSA PRIVATE KEY-----':

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Oorzaak:** Ongeldige referenties of inhoud van persoonlijke sleutel.

- **Aanbeveling:** als u wilt zien of uw sleutelbestand of wachtwoord juist is, controleert u het bij hulpprogramma's zoals WinSCP.

### <a name="sftp-copy-activity-failed"></a>SFTP-kopieeractiviteit is mislukt

- **Symptomen:** 
  * Foutcode: UserErrorInvalidColumnMappingColumnNotFound 
  * Foutbericht: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Oorzaak:** De bron bevat geen kolom met de naam 'AccMngr'.

- **Oplossing:** om te bepalen of de kolom 'AccMngr' bestaat, controleert u de configuratie van uw gegevensset door de kolom van de doelgegevensset toe tewijsen.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Foutcode: SftpFailedToConnectToSftpServer

- **Bericht:**`Failed to connect to SFTP server '%server;'.`

- **Oorzaak:** als het foutbericht de tekenreeks 'Socket read operation has timed out after 30.000 milliseconds' bevat, is een mogelijke oorzaak dat er een onjuist type gekoppelde service wordt gebruikt voor de SFTP-server. U kunt bijvoorbeeld de gekoppelde FTP-service gebruiken om verbinding te maken met de SFTP-server.

- **Aanbeveling:** Controleer de poort van de doelserver. Standaard gebruikt SFTP poort 22.

- **Oorzaak:** Als het foutbericht de tekenreeks 'Serverreactie bevat geen SSH-protocolidentificatie' bevat, is een mogelijke oorzaak dat de SFTP-server de verbinding heeft beperkt. Data Factory maakt meerdere verbindingen om parallel van de SFTP-server te downloaden en soms treedt er SFTP-serverbeperking op. Normaal gesproken retourneren verschillende servers verschillende fouten wanneer ze te maken krijgen met beperkingen.

- **Aanbeveling**:  

    Geef het maximum aantal gelijktijdige verbindingen van de SFTP-gegevensset op als 1 en geef de kopieeractiviteit opnieuw op. Als de activiteit slaagt, kunt u er zeker van zijn dat beperking de oorzaak is.

    Als u de lage doorvoer wilt verhogen, neem dan contact op met uw SFTP-beheerder om de limiet voor het aantal gelijktijdige verbindingen te verhogen. U kunt ook een van de volgende stappen uitvoeren:

    * Als u zelf-hostend IR gebruikt, voegt u het IP-adres van de zelf-hostende IR-machine toe aan de lijst met toegestane IR's.
    * Als u gebruik maakt van Azure IR, voegt u [Azure Integration Runtime IP-adressen toe.](./azure-integration-runtime-ip-addresses.md) Als u geen bereik van IP's wilt toevoegen aan de lijst met toegestane SFTP-servers, gebruikt u in plaats daarvan Zelf-hostende IR.

## <a name="sharepoint-online-list"></a>SharePoint Online-lijst

### <a name="error-code-sharepointonlineauthfailed"></a>Foutcode: SharePointOnlineAuthFailed

- **Bericht:**`The access token generated failed, status code: %code;, error message: %message;.`

- **Oorzaak:** De service-principal-id en -sleutel zijn mogelijk niet juist ingesteld.

- **Aanbeveling:** Controleer uw geregistreerde toepassing (service-principal-id) en sleutel om te zien of deze juist zijn ingesteld.


## <a name="xml-format"></a>XML-indeling

### <a name="error-code-xmlsinknotsupported"></a>Foutcode: XmlSinkNotSupported

- **Bericht:**`Write data in XML format is not supported yet, choose a different format!`

- **Oorzaak:** Er is een XML-gegevensset gebruikt als sink-gegevensset in uw kopieeractiviteit.

- **Aanbeveling:** gebruik een gegevensset in een andere indeling dan die van de sink-gegevensset.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Foutcode: XmlAttributeColumnNameConflict

- **Bericht:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Oorzaak:** Er is een kenmerk voorvoegsel gebruikt, waardoor het conflict is veroorzaakt.

- **Aanbeveling:** stel een andere waarde in voor de eigenschap attributePrefix.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Foutcode: XmlValueColumnNameConflict

- **Bericht:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Oorzaak:** Een van de onderliggende elementnamen is gebruikt als de kolomnaam voor de elementwaarde.

- **Aanbeveling:** stel een andere waarde in voor de eigenschap valueColumn.


### <a name="error-code-xmlinvalid"></a>Foutcode: XmlInvalid

- **Bericht:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Oorzaak:** Het XML-invoerbestand is niet goed gevormd.

- **Aanbeveling:** Corrigeert het XML-bestand om het goed te maken.


## <a name="general-copy-activity-error"></a>Algemene kopieeractiviteitfout

### <a name="error-code-jrenotfound"></a>Foutcode: JreNotFound

- **Bericht:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Oorzaak:** De zelf-hostende IR kan Java Runtime niet vinden. Java Runtime is vereist voor het lezen van bepaalde bronnen.

- **Aanbeveling:** Controleer uw Integratieruntime-omgeving. Zie [Zelf-hostend Integration Runtime.](./format-parquet.md#using-self-hosted-integration-runtime)


### <a name="error-code-wildcardpathsinknotsupported"></a>Foutcode: WildcardPathSinkNotSupported

- **Bericht:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Oorzaak:** de sink-gegevensset biedt geen ondersteuning voor jokertekenwaarden.

- **Aanbeveling:** Controleer de sink-gegevensset en herschrijf het pad zonder een jokerteken te gebruiken.


### <a name="fips-issue"></a>FIPS-probleem

- **Symptomen:** Copy-activiteit mislukt op een zelf-hostend IR-computer met FIPS met het volgende foutbericht: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Oorzaak:** Deze fout kan optreden wanneer u gegevens kopieert met connectors zoals Azure Blob, SFTP, en meer. Federal Information Processing Standards (FIPS) definieert een bepaalde set cryptografische algoritmen die mogen worden gebruikt. Wanneer de FIPS-modus is ingeschakeld op de computer, worden sommige cryptografische klassen waar de kopieeractiviteit van afhankelijk is, in sommige scenario's geblokkeerd.

- **Oplossing:** [ontdek waarom we de FIPS-modus](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)niet meer aanbevelen en evalueer of u FIPS kunt uitschakelen op uw zelf-hostende IR-computer.

    Als u FIPS alleen wilt laten Azure Data Factory en de activiteit wilt laten slagen, doet u het volgende:

    1. Open de map waarin de zelf-hostende IR is geïnstalleerd. Het pad is meestal *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Open het *diawp.exe.config* en voeg vervolgens aan het einde van de sectie `<runtime>` `<enforceFIPSPolicy enabled="false"/>` toe, zoals hier wordt weergegeven:

        ![Schermopname van een sectie van het diawp.exe.config met FIPS uitgeschakeld.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Sla het bestand op en start de zelf-hostende IR-machine opnieuw op.


## <a name="next-steps"></a>Volgende stappen

Probeer de volgende resources voor meer hulp bij het oplossen van problemen:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functieaanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-pagina](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)