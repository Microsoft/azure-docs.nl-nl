---
title: Problemen met Azure Data Factory-connectors oplossen
description: Meer informatie over het oplossen van connector problemen in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 9d8f940e3900c00b1c6f6623dfeff2d92ca85aa3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042430"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory-connectors oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene manieren besproken om problemen met Azure Data Factory-connectors op te lossen.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Fout code: AzureBlobOperationFailed

- **Bericht**: de BLOB-bewerking is mislukt. ContainerName:% containerName;, pad:% pad;. "

- **Oorzaak**: er is een probleem met de Blob Storage bewerking.

- **Aanbeveling**: als u de fout details wilt controleren, raadpleegt u [Blob Storage fout codes](/rest/api/storageservices/blob-service-error-codes). Neem contact op met het Blob Storage team voor meer informatie.


### <a name="invalid-property-during-copy-activity"></a>Ongeldige eigenschap tijdens de Kopieer activiteit

- **Bericht**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Oorzaak**: het type dat in de gegevensset is gedefinieerd, is inconsistent met het bron-of sink-type dat is gedefinieerd in de Kopieer activiteit.

- **Oplossing**: Bewerk de definitie van de gegevensset of de pijp lijn om de typen consistent te maken en voer de implementatie opnieuw uit.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fout bericht: de omvang van de aanvraag is te groot

- **Symptomen**: wanneer u gegevens naar Azure Cosmos DB kopieert met een standaard grootte voor schrijf batch, wordt de volgende fout weer gegeven: `Request size is too large.`

- **Oorzaak**: Azure Cosmos DB de grootte van een enkele aanvraag beperkt tot 2 MB. De formule is *aanvraag grootte = één document grootte \* Batch grootte schrijven*. Als de grootte van uw document groot is, wordt het standaard gedrag veroorzaakt door een te grote aanvraag grootte. U kunt de grootte van de schrijf batch afstemmen.

- **Oplossing**: Verminder in de Sink Copy-activiteit de waarde voor de grootte van de *Schrijf batch* (de standaard waarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Fout bericht: schending van een unieke index beperking

- **Symptomen**: wanneer u gegevens naar Azure Cosmos DB kopieert, wordt de volgende fout weer gegeven:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Oorzaak**: er zijn twee mogelijke oorzaken:

    - Oorzaak 1: als u **Invoegen** als schrijf gedrag gebruikt, betekent dit dat de bron gegevens rijen of objecten met dezelfde id hebben.
    - Oorzaak 2: als u **Upsert** gebruikt als schrijf gedrag en u een andere unieke sleutel instelt op de container, betekent dit dat de bron gegevens rijen of objecten met andere id's hebben, maar dezelfde waarde hebben als de gedefinieerde unieke sleutel.

- **Oplossing**: 

    - Stel **Upsert** voor oorzaak 1 in als schrijf gedrag.
    - Zorg ervoor dat elk document een andere waarde heeft voor de gedefinieerde unieke sleutel voor oorzaak 2.

### <a name="error-message-request-rate-is-large"></a>Fout bericht: de aanvraag frequentie is groot

- **Symptomen**: wanneer u gegevens naar Azure Cosmos DB kopieert, wordt de volgende fout weer gegeven:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Oorzaak**: het aantal gebruikte aanvraag eenheden (RUs) is groter dan de beschik bare RUs-waarde die is geconfigureerd in azure Cosmos db. Zie [aanvraag eenheden in azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations)voor meer informatie over het Azure Cosmos DB berekenen van RUs.

- **Oplossing**: Probeer een van de volgende twee oplossingen:

    - Verhoog het *container RUs* -nummer naar een hogere waarde in azure Cosmos db. Met deze oplossing worden de prestaties van de Kopieer activiteit verbeterd, maar worden er meer kosten in rekening gebracht in Azure Cosmos DB. 
    - Verklein *writeBatchSize* naar een lagere waarde, zoals 1000, en verminder *parallelCopies* naar een lagere waarde, zoals 1. Met deze oplossing worden de prestaties van de Kopieer uitvoering verminderd, maar worden er geen kosten in rekening gebracht voor de Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Kolommen ontbreken in kolom toewijzing

- **Symptomen**: bij het importeren van een schema voor Azure Cosmos DB voor kolom toewijzing, ontbreken sommige kolommen. 

- **Oorzaak**: Data Factory wordt het schema van de eerste tien Azure Cosmos DB documenten afleiden. Als sommige document kolommen of-eigenschappen geen waarden bevatten, wordt het schema niet gedetecteerd door Data Factory en dus niet weer gegeven.

- **Oplossing**: u kunt de query afstemmen zoals wordt weer gegeven in de volgende code, zodat de kolom waarden in de resultatenset met lege waarden worden weer gegeven. Stel dat de *onmogelijke* kolom ontbreekt in de eerste tien documenten). U kunt ook de kolom voor toewijzing hand matig toevoegen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fout bericht: de GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen**: wanneer u gegevens kopieert van Azure Cosmos DB MongoAPI of MongoDb met het veld Universally Unique Identifier (UUID), wordt het volgende fout bericht weer gegeven:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Oorzaak**: er zijn twee manieren om de uuid in binaire JSON (BSON): UuidStardard en UuidLegacy weer te geven. UuidLegacy wordt standaard gebruikt om gegevens te lezen. U krijgt een fout melding als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Oplossing**: Voeg In het Connection String MongoDb de optie *uuidRepresentation = Standard* toe. Zie [MongoDB Connection String](connector-mongodb.md#linked-service-properties)voor meer informatie.
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL-API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Fout code: CosmosDbSqlApiOperationFailed

- **Bericht**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Oorzaak**: er is een probleem met de CosmosDbSqlApi-bewerking.

- **Aanbeveling**: Zie [Azure Cosmos DB Help-document](../cosmos-db/troubleshoot-dot-net-sdk.md)als u de fout details wilt controleren. Neem contact op met het Azure Cosmos DB team voor meer informatie.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fout bericht: de onderliggende verbinding is gesloten: kan geen vertrouwens relatie tot stand brengen voor het beveiligde SSL/TLS-kanaal.

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Oorzaak**: de validatie van het certificaat is mislukt tijdens de TLS-handshake.

- **Oplossing**: als tijdelijke oplossing kunt u de gefaseerde kopie gebruiken om de validatie van de Transport Layer Security (TLS) voor Azure data Lake Storage gen1 over te slaan. U moet dit probleem reproduceren en de netwerk monitor (netmon) traceren en vervolgens uw netwerk team laten controleren of de configuratie van het lokale netwerk is ingeschakeld.

    ![Diagram van Azure Data Lake Storage Gen1 verbindingen voor het oplossen van problemen.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fout bericht: de externe server heeft een fout geretourneerd: (403) verboden

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Oorzaak**: een mogelijke oorzaak is dat de service-principal of beheerde identiteit die u gebruikt, geen toegang heeft tot bepaalde mappen of bestanden.

- **Oplossing**: wijs de juiste machtigingen toe aan alle mappen en submappen die u wilt kopiëren. Zie [gegevens kopiëren naar of van Azure data Lake Storage gen1 met behulp van Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties)voor meer informatie.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fout bericht: kan geen toegangs Token ophalen met behulp van de Service-Principal. ADAL-fout: service_unavailable

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Oorzaak**: wanneer de service token server (STS) die eigendom is van Azure Active Directory niet beschikbaar is, betekent dit dat het te druk is om aanvragen te verwerken en wordt HTTP-fout 503 geretourneerd. 

- **Oplossing**: Voer de Kopieer activiteit na enkele minuten opnieuw uit.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Fout code: ADLSGen2OperationFailed

- **Bericht**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

  | Oorzaak van analyse                                               | Aanbeveling                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Als Azure Data Lake Storage Gen2 een fout genereert die een bepaalde bewerking is mislukt.| Controleer het gedetailleerde fout bericht dat wordt gegenereerd door Azure Data Lake Storage Gen2. Als de fout een tijdelijke fout is, voert u de bewerking opnieuw uit. Neem contact op met Azure Storage ondersteuning voor meer informatie en geef de aanvraag-ID op in een fout bericht. |
  | Als het fout bericht de teken reeks ' verboden ' bevat, is de service-principal of beheerde identiteit die u gebruikt mogelijk niet gemachtigd om toegang te krijgen tot Azure Data Lake Storage Gen2. | Zie [gegevens in azure data Lake Storage Gen2 kopiëren en transformeren met behulp van Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication)om deze fout op te lossen. |
  | Als het fout bericht de teken reeks "InternalServerError" bevat, wordt de fout geretourneerd door Azure Data Lake Storage Gen2. | De fout wordt mogelijk veroorzaakt door een tijdelijke fout. Als dat het geval is, voert u de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met Azure Storage ondersteuning en geeft u de aanvraag-ID op uit het fout bericht. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>De aanvraag voor het Azure Data Lake Storage Gen2-account heeft een time-outfout veroorzaakt

- **Bericht**: 
  * Fout code = `UserErrorFailedBlobFSOperation`
  * Fout bericht = `BlobFS operation failed for: A task was canceled.`

- **Oorzaak**: het probleem wordt veroorzaakt door de time-outfout van de Azure data Lake Storage Gen2-sink, die meestal plaatsvindt op de zelf-hostende Integration runtime computer (IR).

- **Aanbeveling**: 

    - Plaats de zelf-hostende IR-computer en het doel Azure Data Lake Storage Gen2 account in dezelfde regio, indien mogelijk. Dit kan helpen om een wille keurige time-outfout te voor komen en betere prestaties te leveren.

    - Controleer of er een speciale netwerk instelling is, zoals ExpressRoute, en zorg ervoor dat het netwerk voldoende band breedte heeft. U wordt aangeraden de zelf-hostende IR-gelijktijdige taken instelling te verlagen wanneer de totale band breedte laag is. Als u dit doet, kunt u de concurrentie van netwerk bronnen voor meerdere gelijktijdige taken helpen voor komen.

    - Als de bestands grootte gemiddeld of klein is, gebruikt u een kleinere blok grootte voor een niet-binaire kopie om een dergelijke time-outfout te verhelpen. Zie [Blob Storage put Block](/rest/api/storageservices/put-block)(Engelstalig) voor meer informatie.

       Als u de aangepaste blok grootte wilt opgeven, bewerkt u de eigenschap in de JSON-bestands editor, zoals hier wordt weer gegeven:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Opslag Azure Files

### <a name="error-code-azurefileoperationfailed"></a>Fout code: AzureFileOperationFailed

- **Bericht**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Oorzaak**: er is een probleem met de opslag bewerking van Azure files.

- **Aanbeveling**: Zie [Azure files Help](/rest/api/storageservices/file-service-error-codes)voor informatie over het controleren van de fout Details. Neem contact op met het Azure Files team voor meer informatie.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database en SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Foutcode: SqlFailedToConnect

- **Bericht**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

    | Oorzaak van analyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Als het fout bericht de teken reeks ' SqlErrorNumber = 47073 ' bevat, betekent dit dat de open bare netwerk toegang wordt geweigerd in de connectiviteits instelling. | Stel op de Azure SQL-firewall de optie **open bare netwerk toegang weigeren** in op *Nee*. Zie [Azure SQL-connectiviteits instellingen](../azure-sql/database/connectivity-settings.md#deny-public-network-access)voor meer informatie. |
    | Raadpleeg de Azure SQL-probleemoplossings handleiding als het fout bericht een SQL-fout code zoals "SqlErrorNumber = [error code]" bevat voor Azure SQL. | Zie [verbindings problemen en andere fouten oplossen met Azure SQL database en Azure SQL Managed instance](../azure-sql/database/troubleshoot-common-errors-issues.md)(Engelstalig) voor een aanbeveling. |
    | Controleer of poort 1433 wordt weer geven in de lijst met toegestane firewalls. | Zie [poorten die worden gebruikt door SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)voor meer informatie. |
    | Als het fout bericht de teken reeks ' SqlException ' bevat, SQL Database de fout geeft aan dat een bepaalde bewerking is mislukt. | Zoek op SQL-fout code in [Data Base Engine-fouten](/sql/relational-databases/errors-events/database-engine-events-and-errors)voor meer informatie. Neem contact op met de ondersteuning van Azure SQL voor meer informatie. |
    | Als dit een tijdelijk probleem is (bijvoorbeeld een instabiele netwerk verbinding), voegt u de nieuwe poging in het activiteiten beleid toe om de oplossing te verkleinen. | Zie [pijp lijnen en activiteiten in azure Data Factory](./concepts-pipelines-activities.md#activity-policy)voor meer informatie. |
    | Als het fout bericht de teken reeks ' client met IP-adres '... ' bevat heeft geen toegang tot de server, en u probeert verbinding te maken met Azure SQL Database, de fout wordt meestal veroorzaakt door een probleem met de Azure SQL Database firewall. | Schakel in de configuratie van de Azure SQL Server-firewall de optie **Azure-Services en-resources toestaan toegang tot deze server** in. Zie [Azure SQL database en Azure Synapse IP-firewall regels](../azure-sql/database/firewall-configure.md)voor meer informatie. |
    
### <a name="error-code-sqloperationfailed"></a>Foutcode: SqlOperationFailed

- **Bericht**: `A database operation failed. Please search error to get more details.`

- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

    | Oorzaak van analyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Als het fout bericht de teken reeks "SqlException" bevat, genereert SQL Database een fout die aangeeft dat een bepaalde bewerking is mislukt. | Als de SQL-fout niet duidelijk is, probeert u de data base te wijzigen in het meest recente compatibiliteits niveau ' 150 '. Het kan de meest recente versie van SQL-fouten genereren. Raadpleeg de [documentatie](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat) voor meer informatie. <br/> Zoek op SQL-fout code in [Data Base Engine-fouten](/sql/relational-databases/errors-events/database-engine-events-and-errors)voor meer informatie over het oplossen van SQL-problemen. Neem contact op met de ondersteuning van Azure SQL voor meer informatie. |
    | Als het fout bericht de teken reeks ' PdwManagedToNativeInteropException ' bevat, wordt dit meestal veroorzaakt door een niet-overeenkomende waarde voor de grootte van de bron-en Sink-kolom. | Controleer de grootte van de kolommen source en Sink. Neem contact op met de ondersteuning van Azure SQL voor meer informatie. |
    | Als het fout bericht de teken reeks "InvalidOperationException" bevat, wordt dit meestal veroorzaakt door ongeldige invoer gegevens. | Als u wilt identificeren in welke rij het probleem zich heeft voorgedaan, schakelt u de functie fout tolerantie in voor de Kopieer activiteit, waarmee problematische rijen kunnen worden omgeleid naar de opslag voor verdere onderzoek. Zie [fout tolerantie van Kopieer activiteit in azure Data Factory](./copy-activity-fault-tolerance.md)voor meer informatie. |


### <a name="error-code-sqlunauthorizedaccess"></a>Fout code: SqlUnauthorizedAccess

- **Bericht**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Oorzaak**: de referenties zijn onjuist of het aanmeldings account heeft geen toegang tot de SQL database.

- **Aanbeveling**: Controleer of het aanmeldings account voldoende machtigingen heeft voor toegang tot de SQL database.


### <a name="error-code-sqlopenconnectiontimeout"></a>Fout code: SqlOpenConnectionTimeout

- **Bericht**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Oorzaak**: het probleem kan een SQL database tijdelijke fout.

- **Aanbeveling**: Voer de bewerking opnieuw uit om de gekoppelde Service Connection String bij te werken met een grotere time-outwaarde voor de verbinding.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Fout code: SqlAutoCreateTableTypeMapFailed

- **Bericht**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Oorzaak**: de tabel voor het maken van de samen stellen kan niet voldoen aan de bron vereiste.

- **Aanbeveling**: werk het kolom Type in *toewijzingen* bij of maak de Sink-tabel hand matig op de doel server.


### <a name="error-code-sqldatatypenotsupported"></a>Fout code: SqlDataTypeNotSupported

- **Bericht**: `A database operation failed. Check the SQL errors.`

- **Oorzaak**: als het probleem zich voordoet in de SQL-bron en de fout is gerelateerd aan SqlDateTime overflow, overschrijdt de gegevens waarde het logische type bereik (1/1/1753 12:00:00 uur 12/31/9999 11:59:59 pm).

- **Aanbeveling**: Converteer het type naar de teken reeks in de SQL-bron query of wijzig het kolom Type in de kolom toewijzing van de Kopieer activiteit in een *teken reeks*.

- **Oorzaak**: als het probleem optreedt op de SQL-Sink en de fout is gerelateerd aan SqlDateTime overflow, overschrijdt de gegevens waarde het toegestane bereik in de Sink-tabel.

- **Aanbeveling**: werk het overeenkomstige kolom Type bij naar het type *DATETIME2* in de Sink-tabel.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Fout code: SqlInvalidDbStoredProcedure

- **Bericht**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Oorzaak**: de opgegeven opgeslagen procedure is ongeldig. De oorzaak kan zijn dat de opgeslagen procedure geen gegevens retourneert.

- **Aanbeveling**: Valideer de opgeslagen procedure met behulp van SQL-hulpprogram ma's. Zorg ervoor dat de opgeslagen procedure gegevens kan retour neren.


### <a name="error-code-sqlinvaliddbquerystring"></a>Fout code: SqlInvalidDbQueryString

- **Bericht**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Oorzaak**: de opgegeven SQL-query is ongeldig. De oorzaak kan zijn dat de query geen gegevens retourneert.

- **Aanbeveling**: Valideer de SQL-query met behulp van SQL-hulpprogram ma's. Zorg ervoor dat de query gegevens kan retour neren.


### <a name="error-code-sqlinvalidcolumnname"></a>Fout code: SqlInvalidColumnName

- **Bericht**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Oorzaak**: de kolom kan niet worden gevonden omdat de configuratie mogelijk onjuist is.

- **Aanbeveling**: Controleer de kolom in de query, de *structuur* in de gegevensset en de *toewijzingen* in de activiteit.


### <a name="error-code-sqlbatchwritetimeout"></a>Fout code: SqlBatchWriteTimeout

- **Bericht**: `Timeouts in SQL write operation.`

- **Oorzaak**: het probleem kan worden veroorzaakt door een SQL database tijdelijke fout.

- **Aanbeveling**: Voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met de Azure SQL-ondersteuning.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Foutcode: SqlBatchWriteTransactionFailed

- **Bericht**: `SQL transaction commits failed.`

- **Oorzaak**: als de uitzonderings Details voortdurend een time-out voor de trans actie aangeven, is de netwerk latentie tussen de Integration runtime en de data base groter dan de standaard drempel van 30 seconden.

- **Aanbeveling**: werk de SQL-gekoppelde service connection string met een *time-outwaarde* voor de verbinding die gelijk is aan of groter is dan 120 en voer de activiteit opnieuw uit.

- **Oorzaak**: als de uitzonderings details af en toe duiden dat de SQL-verbinding is verbroken, kan dit een tijdelijk netwerk fout of een SQL database-probleem zijn.

- **Aanbeveling**: Voer de activiteit opnieuw uit en controleer de metrische gegevens aan de SQL database zijde.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Fout code: SqlBulkCopyInvalidColumnLength

- **Bericht**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Oorzaak**: het bulksgewijs kopiëren van SQL is mislukt omdat het een ongeldige kolom lengte heeft ontvangen van de BCP-client (Bulk Copy Program Utility).

- **Aanbeveling**: als u wilt identificeren in welke rij het probleem is opgetreden, schakelt u de functie fout tolerantie in voor de Kopieer activiteit. Dit kan problematische rijen omleiden naar de opslag voor verdere onderzoek. Zie [fout tolerantie van Kopieer activiteit in azure Data Factory](./copy-activity-fault-tolerance.md)voor meer informatie.


### <a name="error-code-sqlconnectionisclosed"></a>Fout code: SqlConnectionIsClosed

- **Bericht**: `The connection is closed by SQL Database.`

- **Oorzaak**: de SQL-verbinding wordt gesloten door de SQL database wanneer een hoog aantal gelijktijdige uitvoeringen en de server de verbinding verbreken.

- **Aanbeveling**: Voer de verbinding opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met de Azure SQL-ondersteuning.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fout bericht: de conversie is mislukt tijdens het converteren van een teken reeks naar een unieke id

- **Symptomen**: wanneer u gegevens kopieert van een gegevens bron in tabel vorm (zoals SQL Server) naar Azure Synapse Analytics met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Oorzaak**: Azure Synapse Analytics poly Base kan een lege teken reeks niet converteren naar een GUID.

- **Oplossing**: Stel in de Sink Kopieer activiteit onder poly base-instellingen de optie **type standaard gebruiken** in op *Onwaar*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fout bericht: verwacht gegevens type: decimaal (x, x), foutieve waarde

- **Symptomen**: wanneer u gegevens kopieert van een gegevens bron in tabel vorm (zoals SQL Server) naar Azure Synapse Analytics met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Oorzaak**: Azure Synapse Analytics poly Base kan geen lege teken reeks (null-waarde) invoegen in een decimale kolom.

- **Oplossing**: Stel in de Sink Kopieer activiteit onder poly base-instellingen de optie **type standaard gebruiken** in op ONWAAR.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fout bericht: Java-uitzonderings bericht: HdfsBridge:: CreateRecordReader

- **Symptomen**: u kunt gegevens naar Azure Synapse Analytics kopiëren door poly Base te gebruiken en de volgende fout te ontvangen:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Oorzaak**: de oorzaak kan zijn dat het schema (totale kolom breedte) te groot is (groter dan 1 MB). Controleer het schema van de doel tabel van de Azure Synapse-analyse door de grootte van alle kolommen toe te voegen:

    - Int = 4 bytes
    - Bigint = 8 bytes
    - Varchar (n), char (n), binary (n), varbinary (n) = n bytes
    - Nvarchar (n), nchar (n) = n * 2 bytes
    - Datum = 6 bytes
    - Datetime/(2), smalldatetime = 16 bytes
    - Date time offset = 20 bytes
    - Decimaal = 19 bytes
    - Float = 8 bytes
    - Money = 8 bytes
    - Smallmoney = 4 bytes
    - Real = 4 bytes
    - Smallint = 2 bytes
    - Tijd = 12 bytes
    - Tinyint = 1 byte

- **Oplossing**: 
    - Verklein de kolom breedte tot minder dan 1 MB.
    - Of gebruik een bulksgewijze invoeg benadering door poly Base uit te scha kelen.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fout bericht: er is niet voldaan aan de voor waarde die is opgegeven met een HTTP-header (n)

- **Symptomen**: u gebruikt SQL query om gegevens op te halen uit Azure Synapse Analytics en de volgende fout te ontvangen:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Oorzaak**: er is een probleem opgetreden in azure Synapse Analytics tijdens het uitvoeren van een query op de externe tabel in azure Storage.

- **Oplossing**: Voer dezelfde query uit in SQL Server Management Studio (SSMS) en controleer of u hetzelfde resultaat krijgt. Als dit het geval is, opent u een ondersteunings ticket voor Azure Synapse Analytics en geeft u de naam van uw Azure Synapse Analytics-server en-Data Base op.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>De prestatie tier is laag en het kopiëren van de fout is mislukt

- **Symptomen**: u kopieert gegevens naar Azure SQL database en ontvangt de volgende fout: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Oorzaak**: Azure SQL database S1 de limieten voor i/O-invoer/uitvoer heeft bereikt.

- **Oplossing**: Voer een upgrade uit voor de laag Azure SQL database prestaties om het probleem op te lossen. 


### <a name="sql-table-cant-be-found"></a>Kan de SQL-tabel niet vinden 

- **Symptomen**: u kopieert gegevens van hybride naar een on-premises SQL Server tabel en ontvangt de volgende fout:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Oorzaak**: het huidige SQL-account beschikt niet over voldoende machtigingen voor het uitvoeren van aanvragen die zijn uitgegeven door .net SqlBulkCopy. WriteToServer.

- **Oplossing**: Schakel over naar een meer PRIVILEGEd SQL-account.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Fout bericht: teken reeks of binaire gegevens worden afgekapt

- **Symptomen**: er treedt een fout op wanneer u gegevens naar een on-premises Azure SQL Server-tabel kopieert. 

- **Oorzaak**: de schema definitie van de CX SQL-tabel bevat een of meer kolommen met een korte lengte dan verwacht.

- **Oplossing**: Probeer het volgende om het probleem op te lossen:

    1. Als u wilt oplossen welke rijen het probleem hebben, past u de [fout tolerantie](./copy-activity-fault-tolerance.md)van SQL-Sink toe, met name ' redirectIncompatibleRowSettings '.

        > [!NOTE]
        > Fout tolerantie vereist mogelijk extra uitvoerings tijd, wat kan leiden tot hogere kosten.

    2. Dubbel Controleer de omgeleide gegevens op basis van de kolom lengte van het SQL-tabel schema om te zien welke kolommen moeten worden bijgewerkt.

    3. Werk het tabel schema dienovereenkomstig bij.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Fout code: AzureTableDuplicateColumnsFromSource

- **Bericht**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Oorzaak**: dubbele bron kolommen kunnen om een van de volgende redenen optreden:
   * U gebruikt de-Data Base als bron en toegepast tabel samenvoegingen.
   * U hebt ongestructureerde CSV-bestanden met dubbele kolom namen in de veldnamenrij.

- **Aanbeveling**: Controleer de bron kolommen en los ze op, indien nodig.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Fout code: DB2DriverRunFailed

- **Bericht**: `Error thrown from driver. Sql code: '%code;'`

- **Oorzaak**: als het fout bericht de teken reeks ' SQLSTATE = 51002 SQLCODE =-805 ' bevat, volgt u de tip in [gegevens van DB2 kopiëren met behulp van Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Aanbeveling**: probeer ' NULLID ' in de eigenschap in te stellen `packageCollection`  .


## <a name="delimited-text-format"></a>Tekstindeling met scheidingstekens

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Fout code: DelimitedTextColumnNameNotAllowNull

- **Bericht**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Oorzaak**: wanneer ' firstRowAsHeader ' is ingesteld in de activiteit, wordt de eerste rij gebruikt als de naam van de kolom. Deze fout betekent dat de eerste rij een lege waarde bevat (bijvoorbeeld ' Columna, ColumnB ').

- **Aanbeveling**: Controleer de eerste rij en los de waarde op als deze leeg is.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Fout code: DelimitedTextMoreColumnsThanDefined

- **Bericht**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

  | Oorzaak van analyse                                               | Aanbeveling                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Het aantal kolommen in de problematische rij is groter dan het aantal kolommen in de eerste rij. Dit kan worden veroorzaakt door een gegevens probleem of door onjuiste instellingen voor het kolom scheidings teken of de aanhalings tekens. | Haal het aantal rijen op uit het fout bericht, Controleer de kolom van de rij en los de gegevens op. |
  | Als het verwachte aantal kolommen in een fout bericht ' 1 ' is, hebt u mogelijk verkeerde compressie-of indelings instellingen opgegeven, waardoor Data Factory uw bestanden onjuist hebt geparseerd. | Controleer de indelings instellingen om er zeker van te zijn dat ze overeenkomen met de bron bestanden. |
  | Als uw bron een map is, kunnen de bestanden in de opgegeven map een ander schema hebben. | Zorg ervoor dat de bestanden in de opgegeven map een identiek schema hebben. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service en Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Fout code: DynamicsCreateServiceClientError

- **Bericht**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Oorzaak**: het probleem is een tijdelijk probleem op de Dynamics-Server zijde.

- **Aanbeveling**: Voer de pijp lijn opnieuw uit. Als dat niet het geval is, probeert u het parallelisme te verminderen. Neem contact op met de ondersteuning van Dynamics als het probleem zich blijft voordoen.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Ontbrekende kolommen bij het importeren van een schema of preview-gegevens

- **Symptomen**: er ontbreken enkele kolommen wanneer u een schema of preview-gegevens importeert. Fout bericht: `The valid structure information (column name and type) are required for Dynamics source.`

- **Oorzaak**: dit probleem is het gevolg van een ontwerp, omdat Data Factory geen kolommen kan weer geven die geen waarden bevatten in de eerste tien records. Zorg ervoor dat de kolommen die u hebt toegevoegd de juiste indeling hebben. 

- **Aanbeveling**: Voeg de kolommen hand matig toe op het tabblad toewijzing.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Fout code: DynamicsMissingTargetForMultiTargetLookupField

- **Bericht**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Oorzaak**: de doel kolom bestaat niet in de bron of in de kolom toewijzing.

- **Aanbeveling**:  
  1. Zorg ervoor dat de bron de doel kolom bevat. 
  2. Voeg de doel kolom toe aan de kolom toewijzing. Zorg ervoor dat de kolom Sink de indeling *{FieldName} @EntityReference* heeft.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Fout code: DynamicsInvalidTargetForMultiTargetLookupField

- **Bericht**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Oorzaak**: er wordt een onjuiste entiteits naam gegeven als doel entiteit van een opzoek veld met meerdere doelen.

- **Aanbeveling**: Geef een geldige entiteits naam op voor het opzoek veld met meerdere doelen.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Fout code: DynamicsInvalidTypeForMultiTargetLookupField

- **Bericht**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Oorzaak**: de waarde in de doel kolom is geen teken reeks.

- **Aanbeveling**: Geef een geldige teken reeks op in de kolom multi-doel lookup-doel.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Fout code: DynamicsFailedToRequetServer

- **Bericht**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Oorzaak**: de Dynamics-Server is onstabiel of ontoegankelijk, of het netwerk ondervindt problemen.

- **Aanbeveling**: Controleer de netwerk verbinding of Controleer het logboek van Dynamics Server voor meer informatie. Neem contact op met de ondersteuning voor Dynamics voor meer informatie.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Fout code: FtpFailedToConnectToFtpServer

- **Bericht**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Oorzaak**: een onjuist gekoppeld Service type kan worden gebruikt voor de FTP-server, zoals het gebruik van de gekoppelde service Secure FTP (SFTP) om verbinding te maken met een FTP-server.

- **Aanbeveling**: Controleer de poort van de doel server. FTP maakt gebruik van poort 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Fout code: HttpFileFailedToRead

- **Bericht**: `Failed to read data from http server. Check the error from http server：%message;`

- **Oorzaak**: deze fout treedt op wanneer Azure Data Factory praat met de http-server, maar de bewerking van de HTTP-aanvraag is mislukt.

- **Aanbeveling**: Controleer de HTTP-status code in het fout bericht en los het probleem op de externe server op.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Fout code: ArgumentOutOfRangeException

- **Bericht**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Oorzaak**: in Data Factory worden datetime-waarden ondersteund in het bereik van 0001-01-01 00:00:00 tot 9999-12-31 23:59:59. Oracle ondersteunt echter een breder bereik van datum-/tijdwaarden, zoals de BC Century of min/SEC>59, wat leidt tot een fout in Data Factory.

- **Aanbeveling**: 

    Als u wilt zien of de waarde in Oracle binnen het bereik van Data Factory ligt, voert u uit `select dump(<column name>)` . 

    Zie [Hoe worden datums opgeslagen in Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)voor meer informatie over de byte volgorde in het resultaat.


## <a name="orc-format"></a>ORC-indeling

### <a name="error-code-orcjavainvocationexception"></a>Fout code: OrcJavaInvocationException

- **Bericht**: `An error occurred when invoking Java, message: %javaException;.`
- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

    | Oorzaak van analyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wanneer het fout bericht de teken reeksen ' Java. lang. OutOfMemory ', ' Java heap Space ' en ' doubleCapacity ' bevat, is dit doorgaans een probleem met het geheugen beheer in een oudere versie van Integration runtime. | Als u zelf-hostende Integration Runtime gebruikt, raden we u aan om een upgrade uit te voeren naar de nieuwste versie. |
    | Wanneer het fout bericht de teken reeks ' Java. lang. OutOfMemory ' bevat, heeft de Integration runtime onvoldoende bronnen om de bestanden te verwerken. | Beperk de gelijktijdige uitvoeringen van de Integration runtime. Voor zelf-hostende IR kunt u omhoog schalen naar een krachtige computer met geheugen dat groter is dan of gelijk is aan 8 GB. |
    |Als het fout bericht de teken reeks ' NullPointerReference ' bevat, is de oorzaak mogelijk een tijdelijke fout. | Voer de bewerking opnieuw uit. Neem contact op met de ondersteuning als het probleem zich blijft voordoen. |
    | Als het fout bericht de teken reeks ' BufferOverflowException ' bevat, is de oorzaak mogelijk een tijdelijke fout. | Voer de bewerking opnieuw uit. Neem contact op met de ondersteuning als het probleem zich blijft voordoen. |
    | Wanneer het fout bericht de teken reeks ' Java. lang. ClassCastException:org. apache. Hadoop. component. serde2. io. HiveCharWritable bevat, kan niet worden geconverteerd naar org. apache. Hadoop. io. Text ', is de oorzaak mogelijk een probleem met een type conversie in Java runtime. Normaal gesp roken betekent dit dat de bron gegevens niet goed kunnen worden verwerkt in Java runtime. | Dit is een gegevens probleem. Probeer een teken reeks te gebruiken in plaats van char of Varchar in ORC-indelings gegevens. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Fout code: OrcDateTimeExceedLimit

- **Bericht**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Oorzaak**: als de datum/tijd-waarde ' 0001-01-01 00:00:00 ' is, kan dit worden veroorzaakt door de verschillen tussen de [Juliaanse kalender en de Gregoriaanse kalender](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Aanbeveling**: Controleer de waarde voor de maat streepjes en Vermijd het gebruik van de datum-/tijdwaarde ' 0001-01-01 00:00:00 '.


## <a name="parquet-format"></a>Parquet-indeling

### <a name="error-code-parquetjavainvocationexception"></a>Fout code: ParquetJavaInvocationException

- **Bericht**: `An error occurred when invoking java, message: %javaException;.`

- **Oorzaken en aanbevelingen**: verschillende oorzaken kunnen leiden tot deze fout. Raadpleeg de onderstaande lijst voor mogelijke oorzaak analyse en gerelateerde aanbevelingen.

    | Oorzaak van analyse                                               | Aanbeveling                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Als het fout bericht de teken reeksen ' Java. lang. OutOfMemory ', ' Java heap Space ' en ' doubleCapacity ' bevat, is dit doorgaans een probleem met het geheugen beheer in een oude versie van Integration Runtime. | Als u een zelf-hostende IR gebruikt en de versie eerder is dan 3.20.7159.1, raden we u aan om een upgrade uit te voeren naar de meest recente versie. |
    | Wanneer het fout bericht de teken reeks ' Java. lang. OutOfMemory ' bevat, heeft de Integration runtime onvoldoende bronnen om de bestanden te verwerken. | Beperk de gelijktijdige uitvoeringen van de Integration runtime. Voor zelf-hostende IR schaalt u tot een krachtige computer met een geheugen dat gelijk is aan of groter is dan 8 GB. |
    | Wanneer het fout bericht de teken reeks ' NullPointerReference ' bevat, is dit mogelijk een tijdelijke fout. | Voer de bewerking opnieuw uit. Neem contact op met de ondersteuning als het probleem zich blijft voordoen. |

### <a name="error-code-parquetinvalidfile"></a>Fout code: ParquetInvalidFile

- **Bericht**: `File is not a valid Parquet file.`

- **Oorzaak**: dit is een Parquet-bestands probleem.

- **Aanbeveling**: Controleer of de invoer een geldig Parquet-bestand is.


### <a name="error-code-parquetnotsupportedtype"></a>Fout code: ParquetNotSupportedType

- **Bericht**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Oorzaak**: de Parquet-indeling wordt niet ondersteund in azure Data Factory.

- **Aanbeveling**: Controleer de bron gegevens met behulp van [ondersteunde bestands indelingen en compressie-codecs op basis van de kopieer activiteit in azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Fout code: ParquetMissedDecimalPrecisionScale

- **Bericht**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Oorzaak**: de precisie en schaal van het getal zijn geparseerd, maar er is geen informatie verstrekt.

- **Aanbeveling**: de bron geeft niet de juiste precisie en schaal informatie weer. Controleer de kolom probleem voor de informatie.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Fout code: ParquetInvalidDecimalPrecisionScale

- **Bericht**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Oorzaak**: het schema is ongeldig.

- **Aanbeveling**: Raadpleeg de kolom probleem voor Precision en scale.


### <a name="error-code-parquetcolumnnotfound"></a>Fout code: ParquetColumnNotFound

- **Bericht**: `Column %column; does not exist in Parquet file.`

- **Oorzaak**: het bron schema komt niet overeen met het sink-schema.

- **Aanbeveling**: Controleer de toewijzingen in de activiteit. Zorg ervoor dat de bron kolom kan worden toegewezen aan de juiste Sink-kolom.


### <a name="error-code-parquetinvaliddataformat"></a>Fout code: ParquetInvalidDataFormat

- **Bericht**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Oorzaak**: de gegevens kunnen niet worden geconverteerd naar het type dat is opgegeven in de toewijzingen. source.

- **Aanbeveling**: Controleer de bron gegevens of geef het juiste gegevens type voor deze kolom op in de kolom toewijzing van de Kopieer activiteit. Zie [ondersteunde bestands indelingen en compressie-codecs per Kopieer activiteit in azure Data Factory](./supported-file-formats-and-compression-codecs.md)voor meer informatie.


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Fout code: ParquetDataCountNotMatchColumnCount

- **Bericht**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Oorzaak**: het aantal bron kolommen en het aantal Sink-kolommen komen niet overeen.

- **Aanbeveling**: dubbel Controleer of het aantal bron kolommen gelijk is aan het aantal Sink-kolommen in ' mapping '.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Fout code: ParquetDataTypeNotMatchColumnType

- **Bericht**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Oorzaak**: de gegevens van de bron kunnen niet worden geconverteerd naar het type dat is gedefinieerd in de sink.

- **Aanbeveling**: Geef een juist type op in mapping. sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Fout code: ParquetBridgeInvalidData

- **Bericht**: `%message;`

- **Oorzaak**: de gegevens waarde heeft de limiet overschreden.

- **Aanbeveling**: Voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact met ons op.


### <a name="error-code-parquetunsupportedinterpretation"></a>Fout code: ParquetUnsupportedInterpretation

- **Bericht**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Oorzaak**: dit scenario wordt niet ondersteund.

- **Aanbeveling**: ' ParquetInterpretFor ' mag niet ' sparkSql ' zijn.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Fout code: ParquetUnsupportFileLevelCompressionOption

- **Bericht**: `File level compression is not supported for Parquet.`

- **Oorzaak**: dit scenario wordt niet ondersteund.

- **Aanbeveling**: Verwijder ' CompressionType ' in de payload.


### <a name="error-code-usererrorjniexception"></a>Fout code: UserErrorJniException

- **Bericht**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Oorzaak**: een Java Virtual Machine (JVM) kan niet worden gemaakt omdat sommige ongeldige (globale) argumenten zijn ingesteld.

- **Aanbeveling**: Meld u aan bij de computer die als host fungeert voor *elk knoop punt* van uw zelf-hostende IR. Controleer als volgt of de systeem variabele juist is ingesteld: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Start alle IR-knoop punten opnieuw op en voer de pijp lijn opnieuw uit.


### <a name="arithmetic-overflow"></a>Reken kundige overloop

- **Symptomen**: er is een fout bericht opgetreden bij het kopiëren van Parquet-bestanden: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Oorzaak**: momenteel alleen de decimale komma <= 38 en de lengte van het gehele onderdeel <= 20 worden ondersteund wanneer u bestanden van Oracle naar Parquet kopieert. 

- **Oplossing**: als tijdelijke oplossing kunt u kolommen met dit probleem omzetten in varchar2.


### <a name="no-enum-constant"></a>Geen Enum-constante

- **Symptomen**: er is een fout bericht opgetreden bij het kopiëren van gegevens naar de Parquet-indeling: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , of: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Oorzaak**: 

    Het probleem kan worden veroorzaakt door spaties of niet-ondersteunde speciale tekens (zoals,; {} () \n\t =) in de kolom naam, omdat Parquet geen dergelijke indeling ondersteunt. 

    Een kolom naam, zoals *Contoso (test)* , parseert het type tussen vier Kante haken van [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . De fout wordt gegenereerd omdat er geen dergelijk test type is.

    Als u ondersteunde typen wilt controleren, gaat u naar de GitHub [Apache/Parquet-Mr-site](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Oplossing**: 

    Dubbel Controleer of:
    - De kolom naam voor de Sink bevat spaties.
    - De eerste rij met spaties wordt gebruikt als de kolom naam.
    - Het type OriginalType wordt ondersteund. Vermijd het gebruik van deze speciale tekens: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Fout code: RestSinkCallFailed

- **Bericht**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Oorzaak**: deze fout treedt op wanneer Azure Data Factory praat met het rest-eind punt via het HTTP-protocol en de aanvraag bewerking mislukt.

- **Aanbeveling**: Controleer de HTTP-status code of het bericht in het fout bericht en los het probleem op de externe server op.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Onverwachte netwerk reactie van de REST-connector

- **Symptomen**: het eind punt ontvangt soms een onverwacht antwoord (400, 401, 403, 500) van de rest-connector.

- **Oorzaak**: de rest-bron connector gebruikt de URL en de HTTP-methode/koptekst/hoofd code van de gekoppelde service/gegevensset/Kopieer bron als para meters bij het maken van een HTTP-aanvraag. Het probleem wordt waarschijnlijk veroorzaakt door enkele fouten in een of meer opgegeven para meters.

- **Oplossing**: 
    - Gebruik ' krul ' in een opdracht prompt venster om te zien of de para meter de oorzaak is (**accepteren** en **gebruikers agent** headers moeten altijd worden opgenomen):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Als de opdracht dezelfde onverwachte reactie retourneert, verhelpt u de voor gaande para meters met ' krul ' totdat het verwachte antwoord wordt geretourneerd. 

      U kunt ook ' krul--Help ' gebruiken voor meer geavanceerd gebruik van de opdracht.

    - Als alleen de Data Factory REST-connector een onverwacht antwoord retourneert, neemt u contact op met micro soft ondersteuning voor verdere probleem oplossing.
    
    - Houd er rekening mee dat ' krul ' mogelijk niet geschikt is voor het reproduceren van een probleem met het valideren van een SSL-certificaat. In sommige scenario's is de opdracht ' krul ' uitgevoerd zonder dat er problemen zijn met de validatie van SSL-certificaten. Maar wanneer dezelfde URL wordt uitgevoerd in een browser, wordt er in feite geen SSL-certificaat geretourneerd voor de client om een vertrouwens relatie met de server tot stand te brengen.

      Hulpprogram ma's zoals **postman** en **Fiddler** worden aanbevolen voor het bovenstaande geval.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Fout code: SftpOperationFail

- **Bericht**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Oorzaak**: er is een probleem met de SFTP-bewerking.

- **Aanbeveling**: Raadpleeg de fout Details van SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Fout code: SftpRenameOperationFail

- **Bericht**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Oorzaak**: uw SFTP-server biedt geen ondersteuning voor het wijzigen van de naam van het tijdelijke bestand.

- **Aanbeveling**: Stel ' useTempFileRename ' in als False in de Kopieer-Sink om het uploaden naar het tijdelijke bestand uit te scha kelen.


### <a name="error-code-sftpinvalidsftpcredential"></a>Fout code: SftpInvalidSftpCredential

- **Bericht**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Oorzaak**: de inhoud van een persoonlijke sleutel wordt opgehaald uit de Azure-sleutel kluis of SDK, maar is niet op de juiste wijze gecodeerd.

- **Aanbeveling**:  

    Als de inhoud van de persoonlijke sleutel van uw sleutel kluis is, kan het bestand van de oorspronkelijke sleutel werken als u het rechtstreeks naar de gekoppelde service van SFTP uploadt.

    Zie [gegevens kopiëren van en naar de sftp-server met behulp van Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication)voor meer informatie. De inhoud van de persoonlijke sleutel is base64-gecodeerde SSH-inhoud met een persoonlijke sleutel.

    Versleutel het *hele* oorspronkelijke persoonlijke sleutel bestand met base64-code ring en sla de versleutelde teken reeks op in uw sleutel kluis. Het oorspronkelijke persoonlijke-sleutel bestand is de naam die kan worden gebruikt voor de gekoppelde service van SFTP als u **uploaden** selecteert in het bestand.

    Hier volgen enkele voor beelden die u kunt gebruiken om de teken reeks te genereren:

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

    - Gebruik een hulp programma voor het converteren van base64 van derden. We raden u aan het hulp programma [coderen naar base64 te](https://www.base64encode.org/) gebruiken.

- **Oorzaak**: de verkeerde inhouds indeling voor de sleutel is gekozen.

- **Aanbeveling**:  

    PKCS # 8-persoonlijke SSH-sleutel (beginnen met '-----beginnen met de versleutelde persoonlijke sleutel-----') wordt momenteel niet ondersteund voor toegang tot de SFTP-server in Data Factory. 

    Voer de volgende opdrachten uit om de sleutel te converteren naar een traditionele SSH-sleutel notatie, te beginnen met '-----BEGIN RSA-sleutel-----':

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Oorzaak**: ongeldige referenties of inhoud met persoonlijke sleutel.

- **Aanbeveling**: als u wilt weten of uw sleutel bestand of wacht woord juist is, controleert u met hulpprogram ma's, zoals WinSCP.

### <a name="sftp-copy-activity-failed"></a>SFTP-Kopieer activiteit is mislukt

- **Symptomen**: 
  * Fout code: UserErrorInvalidColumnMappingColumnNotFound 
  * Fout bericht: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Oorzaak**: de bron bevat geen kolom met de naam ' AccMngr '.

- **Oplossing**: als u wilt vaststellen of de kolom AccMngr bestaat, controleert u de configuratie van uw gegevensset door de kolom doel gegevensset te koppelen.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Fout code: SftpFailedToConnectToSftpServer

- **Bericht**: `Failed to connect to SFTP server '%server;'.`

- **Oorzaak**: als het fout bericht de teken reeks ' Lees bewerking van socket heeft na 30.000 milliseconden, een time-out heeft, is een mogelijke oorzaak dat er een onjuist gekoppeld Service type voor de sftp-server wordt gebruikt. U kunt bijvoorbeeld de gekoppelde FTP-service gebruiken om verbinding te maken met de SFTP-server.

- **Aanbeveling**: Controleer de poort van de doel server. SFTP gebruikt standaard poort 22.

- **Oorzaak**: als het fout bericht de teken reeks ' server antwoord heeft geen SSH-protocol-id ' bevat, kan een mogelijke oorzaak zijn dat de sftp-server de verbinding heeft beperkt. Data Factory maakt het mogelijk om meerdere verbindingen te maken om te downloaden van de SFTP-server, en soms wordt er een SFTP-server beperking aangetroffen. Normaal gesp roken retour neren verschillende servers verschillende fouten wanneer er beperkingen optreden.

- **Aanbeveling**:  

    Geef het maximum aantal gelijktijdige verbindingen van de SFTP-gegevensset op als 1 en voer de Kopieer activiteit opnieuw uit. Als de activiteit is geslaagd, kunt u ervoor zorgen dat beperking de oorzaak is.

    Als u de lage door voer wilt verhogen, neemt u contact op met de SFTP-beheerder om de limiet voor gelijktijdige verbindingen te verhogen. u kunt ook een van de volgende handelingen uitvoeren:

    * Als u een zelf-hostende IR gebruikt, voegt u het IP-adres van de zelf-hostende IR toe aan de acceptatie lijst.
    * Als u Azure IR gebruikt, voegt u [Azure Integration runtime IP-adressen](./azure-integration-runtime-ip-addresses.md)toe. Als u geen bereik van IP-adressen wilt toevoegen aan de acceptatie lijst van de SFTP-server, gebruikt u in plaats daarvan zelf-hostende IR.

## <a name="sharepoint-online-list"></a>SharePoint Online-lijst

### <a name="error-code-sharepointonlineauthfailed"></a>Fout code: SharePointOnlineAuthFailed

- **Bericht**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Oorzaak**: de Service-Principal-ID en-sleutel zijn mogelijk niet correct ingesteld.

- **Aanbeveling**: Controleer de geregistreerde toepassing (Service-Principal-id) en de sleutel om te zien of ze correct zijn ingesteld.


## <a name="xml-format"></a>XML-indeling

### <a name="error-code-xmlsinknotsupported"></a>Fout code: XmlSinkNotSupported

- **Bericht**: `Write data in XML format is not supported yet, choose a different format!`

- **Oorzaak**: er is een XML-gegevensset gebruikt als Sink-gegevensset in uw Kopieer activiteit.

- **Aanbeveling**: gebruik een gegevensset in een andere indeling dan die van de Sink-gegevensset.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Fout code: XmlAttributeColumnNameConflict

- **Bericht**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Oorzaak**: er is een kenmerk voorvoegsel gebruikt, waardoor het conflict is veroorzaakt.

- **Aanbeveling**: Stel een andere waarde in voor de eigenschap ' attributePrefix '.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Fout code: XmlValueColumnNameConflict

- **Bericht**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Oorzaak**: een van de onderliggende element namen is gebruikt als de kolom naam voor de waarde van het element.

- **Aanbeveling**: Stel een andere waarde in voor de eigenschap ' valueColumn '.


### <a name="error-code-xmlinvalid"></a>Fout code: XmlInvalid

- **Bericht**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Oorzaak**: het XML-invoer bestand heeft niet de juiste indeling.

- **Aanbeveling**: Corrigeer het XML-bestand om het goed te maken.


## <a name="general-copy-activity-error"></a>Fout met algemene Kopieer activiteit

### <a name="error-code-jrenotfound"></a>Fout code: JreNotFound

- **Bericht**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Oorzaak**: de zelf-hostende IR kan Java runtime niet vinden. Java runtime is vereist voor het lezen van bepaalde bronnen.

- **Aanbeveling**: Controleer uw Integration runtime-omgeving, Zie [zelf-hostende Integration runtime gebruiken](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Fout code: WildcardPathSinkNotSupported

- **Bericht**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Oorzaak**: de Sink-gegevensset ondersteunt geen joker teken waarden.

- **Aanbeveling**: Controleer de Sink-gegevensset en herschrijf het pad zonder een Joker teken waarde te gebruiken.


### <a name="fips-issue"></a>FIPS-probleem

- **Symptomen**: Kopieer activiteit mislukt op een met FIPS ingeschakelde, zelf-hostende IR-computer met het volgende fout bericht: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Oorzaak**: deze fout kan optreden wanneer u gegevens kopieert met connectors zoals Azure Blob, SFTP, enzovoort. Federal Information Processing Standards (FIPS) definieert een bepaalde set cryptografische algoritmen die mogen worden gebruikt. Wanneer de FIPS-modus is ingeschakeld op de computer, zijn sommige cryptografische klassen waarvan de activiteit wordt gekopieerd, in sommige scenario's geblokkeerd.

- **Oplossing**: meer informatie over [waarom we de FIPS-modus niet meer aanraden](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)en evalueren of u FIPS kunt uitschakelen op uw zelf-hostende IR-computer.

    Als u de uitvoering van FIPS wilt Azure Data Factory overs Laan en de activiteit wilt uitvoeren, gaat u als volgt te werk:

    1. Open de map waarin de zelf-hostende IR is geïnstalleerd. Het pad is meestal *C:\Program Files\Microsoft Integration runtime \<IR version> \Shared*.

    2. Open het *diawp.exe.config* -bestand en voeg aan het einde van de `<runtime>` sectie het `<enforceFIPSPolicy enabled="false"/>` volgende toe, zoals hier wordt weer gegeven:

        ![Scherm afbeelding van een sectie van het diawp.exe.config bestand met FIPS uitgeschakeld.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Sla het bestand op en start de zelf-hostende IR-computer opnieuw op.


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Micro soft Q&een pagina](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)