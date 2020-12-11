---
title: Problemen met Azure Data Factory-connectors oplossen
description: Meer informatie over het oplossen van connector problemen in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: a7a81a742922d45be965c7f73e3cb910d0ef989a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109284"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Problemen met Azure Data Factory-connectors oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden voor connectors in Azure Data Factory besproken.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Fout code: AzureBlobOperationFailed

- **Bericht**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Oorzaak**: probleem met het aanraken van Blob-opslag.

- **Aanbeveling**: Raadpleeg de fout voor meer informatie. Raadpleeg het Help-document van blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Neem contact op met het opslag team als u hulp nodig hebt.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Fout code: AzureBlobServiceNotReturnExpectedDataLength

- **Bericht**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Fout code: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Bericht**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Fout code: AzureStorageOperationFailedConcurrentWrite

- **Bericht**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="invalid-property-during-copy-activity"></a>Ongeldige eigenschap tijdens de Kopieer activiteit

- **Bericht**:  `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Oorzaak**: het type dat in de gegevensset is gedefinieerd, is inconsistent met het bron/Sink-type dat is gedefinieerd in de Kopieer activiteit.

- **Oplossing**: Bewerk de JSON-definitie van de gegevensset of pijp lijn om de typen consistent te maken en de implementatie opnieuw uit te voeren.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fout bericht: de omvang van de aanvraag is te groot

- **Symptomen**: u kunt gegevens naar Azure Cosmos DB kopiëren met de standaard grootte voor schrijven en de fout melding ' de grootte van de ***aanvraag is te groot**'*.

- **Oorzaak**: Cosmos DB beperkt de grootte van één aanvraag tot 2 MB. De formule is, aanvraag grootte = één document grootte * Batch grootte schrijven. Als de grootte van uw document groot is, wordt het standaard gedrag veroorzaakt door te grote aanvraag grootte. U kunt de grootte van de schrijf batch afstemmen.

- **Oplossing**: in Sink voor kopieer activiteiten kunt u de waarde ' Batch grootte schrijven ' verlagen (de standaard waarde is 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Fout bericht: schending van een unieke index beperking

- **Symptomen**: wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Oorzaak**: er zijn twee mogelijke oorzaken:

    - Als u **Invoegen** als schrijf gedrag gebruikt, betekent dit dat de bron gegevens rijen/objecten met dezelfde id hebben.

    - Als u **Upsert** als schrijf gedrag gebruikt en u een andere unieke sleutel instelt op de container, betekent dit dat de bron gegevens rijen/objecten hebben met verschillende id's, maar dezelfde waarde hebben voor de gedefinieerde unieke sleutel.

- **Oplossing**: 

    - Stel voor Cause1 **Upsert** in als schrijf gedrag.
    - Zorg ervoor dat elk document voor oorzaak 2 een andere waarde heeft voor de gedefinieerde unieke sleutel.

### <a name="error-message-request-rate-is-large"></a>Fout bericht: de aanvraag frequentie is groot

- **Symptomen**: wanneer u gegevens naar Cosmos DB kopieert, raakt u de volgende fout melding:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Oorzaak**: de gebruikte aanvraag eenheden is groter dan de beschik bare ru die is geconfigureerd in Cosmos db. Meer informatie over hoe Cosmos DB RU van [hieruit](../cosmos-db/request-units.md#request-unit-considerations)berekent.

- **Oplossing**: Hier volgen twee oplossingen:

    1. **Verhoog de container ru** naar een grotere waarde in Cosmos DB, waardoor de prestaties van de Kopieer activiteit worden verbeterd, hoewel er meer kosten in Cosmos DB ontstaan. 

    2. Verklein **writeBatchSize** naar een kleinere waarde (zoals 1000) en stel **parallelCopies** in op een kleinere waarde, zoals 1, waardoor de prestaties van het kopiëren worden verergerd dan de huidige, maar er worden geen kosten in rekening gebracht in Cosmos db.

### <a name="column-missing-in-column-mapping"></a>Kolom ontbreekt in kolom toewijzing

- **Symptomen**: bij het importeren van het schema voor Cosmos DB voor kolom toewijzing, ontbreken sommige kolommen. 

- **Oorzaak**: ADF leidt het schema af van de eerste tien Cosmos DB documenten. Als sommige kolommen/eigenschappen geen waarde hebben in deze documenten, wordt deze niet gedetecteerd met ADF. dit wordt niet weer gegeven.

- **Oplossing**: u kunt de query zoals hieronder afstemmen om kolom af te dwingen om weer te geven in de resultatenset met een lege waarde: (Stel dat er geen kolom ' onmogelijk ' is in de eerste tien documenten). U kunt ook de kolom voor toewijzing hand matig toevoegen.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fout bericht: de GuidRepresentation voor de lezer is CSharpLegacy

- **Symptomen**: bij het kopiëren van gegevens uit Cosmos DB MongoAPI/MongoDb met het veld uuid, raakt u de volgende fout melding:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Oorzaak**: er zijn twee manieren om uuid in BSON-UuidStardard en UuidLegacy aan te duiden. UuidLegacy wordt standaard gebruikt om gegevens te lezen. U krijgt een fout als uw UUID-gegevens in MongoDB UuidStandard zijn.

- **Oplossing**: Voeg In MongoDb Connection String de optie "**uuidRepresentation = Standard**" toe. Zie [MongoDB Connection String](connector-mongodb.md#linked-service-properties)voor meer informatie.
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Fout code: AdlsGen2OperationFailed

- **Bericht**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Oorzaak**: ADLS Gen2 genereert de fout die aangeeft dat de bewerking is mislukt.

- **Aanbeveling**: Raadpleeg het gedetailleerde fout bericht dat wordt gegenereerd door ADLS Gen2. Als dit wordt veroorzaakt door een tijdelijke fout, probeer het dan opnieuw. Als u meer hulp nodig hebt, neemt u contact op met de ondersteuning van Azure Storage en geeft u de aanvraag-ID op in een fout bericht.

- **Oorzaak**: wanneer het fout bericht ' verboden ' bevat, is voor de service-principal of beheerde identiteit die u gebruikt mogelijk niet voldoende machtigingen voor toegang tot de ADLS Gen2.

- **Aanbeveling**: Raadpleeg het Help-document: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Oorzaak**: wanneer het fout bericht ' InternalServerError ' bevat, wordt de fout geretourneerd door ADLS Gen2.

- **Aanbeveling**: dit wordt mogelijk veroorzaakt door een tijdelijke fout, probeer het opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met Azure Storage ondersteuning en geeft u de aanvraag-ID op in het fout bericht.


### <a name="error-code--adlsgen2invalidurl"></a>Fout code: AdlsGen2InvalidUrl

- **Bericht**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Fout code: AdlsGen2InvalidFolderPath

- **Bericht**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Fout code: AdlsGen2OperationFailedConcurrentWrite

- **Bericht**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Fout code: AdlsGen2TimeoutError

- **Bericht**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Fout bij het aanvragen van een time-out voor de ADLS Gen2-account

- **Bericht**: fout code = `UserErrorFailedBlobFSOperation` , fout bericht = `BlobFS operation failed for: A task was canceled` .

- **Oorzaak**: het probleem wordt veroorzaakt door de time-outfout van de ADLS Gen2-sink, wat meestal gebeurt op de zelf-hostende IR-computer.

- **Aanbeveling**: 

    1. Plaats uw zelf-hostende IR-computer en het doel ADLS Gen2 account in dezelfde regio, indien mogelijk. Dit kan een wille keurige time-outfout voor komen en betere prestaties hebben.

    1. Controleer of er een speciale netwerk instelling is, zoals ExpressRoute, en zorg ervoor dat het netwerk voldoende band breedte heeft. U kunt het beste de zelf-hostende IR-instelling voor gelijktijdige taken verlagen wanneer de totale band breedte laag is, waardoor netwerk resource-concurrentie voor meerdere gelijktijdige taken kan voor komen.

    1. Kleinere blok grootte voor niet-binaire kopieën gebruiken om een dergelijke time-outfout te verhelpen als de bestands grootte gemiddeld of klein is. Raadpleeg [Blob Storage put-blok](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Als u de aangepaste blok grootte wilt opgeven, kunt u de eigenschap bewerken in. json-editor:
    ```
    "sink": {
        "type": "DelimitedTextSink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings",
            "blockSizeInMB": 8
        }
    }
    ```


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fout bericht: de onderliggende verbinding is gesloten: kan geen vertrouwens relatie tot stand brengen voor het beveiligde SSL/TLS-kanaal.

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Oorzaak**: de validatie van het certificaat is mislukt tijdens de TLS-handshake.

- **Oplossing**: tijdelijke oplossing: gebruik gefaseerde kopie om de TLS-validatie voor ADLS gen1 over te slaan. U moet dit probleem reproduceren en netmon-tracering verzamelen en vervolgens uw netwerk team benaderen om de configuratie van het lokale netwerk te controleren.

    ![Problemen met ADLS Gen1 oplossen](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fout bericht: de externe server heeft een fout geretourneerd: (403) verboden

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Oorzaak**: een mogelijke oorzaak is dat de service-principal of beheerde identiteit die u gebruikt, geen toegang heeft tot de bepaalde map/het bestand.

- **Oplossing**: verleen de bijbehorende machtigingen voor alle mappen en submappen die u wilt kopiëren. Raadpleeg [dit document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fout bericht: kan geen toegangs Token ophalen met behulp van de Service-Principal. ADAL-fout: service_unavailable

- **Symptomen**: de Kopieer activiteit mislukt met de volgende fout:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Oorzaak**: wanneer de STS (Service token server) die eigendom is van Azure Active Directory niet beschikbaar is, dat wil zeggen, een HTTP-fout 503 wordt geretourneerd. 

- **Oplossing**: Voer de Kopieer activiteit na enkele minuten opnieuw uit.
                  

## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Fout code: SqlFailedToConnect

- **Bericht**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Oorzaak**: als het fout bericht ' SQLException ' bevat, genereert SQL database de fout die aangeeft dat een bepaalde bewerking is mislukt.

- **Aanbeveling**: Zoek op SQL-fout code in dit referentie document voor meer informatie: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Als u meer hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: als het fout bericht ' client met IP-adres '... ' bevat heeft geen toegang tot de server, en u probeert verbinding te maken met Azure SQL Database. dit wordt meestal veroorzaakt door Azure SQL Database firewall probleem.

- **Aanbeveling**: Schakel in de configuratie van de logische SQL Server-firewall de optie ' Azure-Services en-bronnen toestaan voor toegang tot deze server ' in. Verwijzings document: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Fout code: SqlOperationFailed

- **Bericht**: `A database operation failed. Please search error to get more details.`

- **Oorzaak**: als het fout bericht ' SQLException ' bevat, genereert SQL database de fout die aangeeft dat een bepaalde bewerking is mislukt.

- **Aanbeveling**: als SQL-fout niet duidelijk is, probeert u de data base te wijzigen in het meest recente compatibiliteits niveau ' 150 '. Het kan de meest recente versie van SQL-fouten genereren. Raadpleeg het [detail document](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Zoek in dit referentie document naar SQL-fout code voor meer informatie over het oplossen van problemen met SQL: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Als u meer hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: als het fout bericht ' PdwManagedToNativeInteropException ' bevat, wordt dit meestal veroorzaakt door een niet-overeenkomende waarde voor de grootte van de bron-en Sink-kolom.

- **Aanbeveling**: Controleer de grootte van de kolommen bron en Sink. Als u meer hulp nodig hebt, neemt u contact op met Azure SQL-ondersteuning.

- **Oorzaak**: als het fout bericht ' InvalidOperationException ' bevat, wordt dit meestal veroorzaakt door ongeldige invoer gegevens.

- **Aanbeveling**: als u wilt identificeren in welke rij het probleem zich voordoet, schakelt u de functie fout tolerantie in op Kopieer activiteit, waarmee u problematische rij (en) kunt omleiden naar de opslag voor verdere onderzoek. Verwijzings document: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .



### <a name="error-code--sqlunauthorizedaccess"></a>Fout code: SqlUnauthorizedAccess

- **Bericht**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Oorzaak**: de referentie is onjuist of het aanmeldings account heeft geen toegang tot SQL database.

- **Aanbeveling**: Controleer of het aanmeldings account voldoende machtigingen heeft om toegang te krijgen tot de SQL database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Fout code: SqlOpenConnectionTimeout

- **Bericht**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Oorzaak**: kan SQL database tijdelijke fout zijn.

- **Aanbeveling**: opnieuw proberen om de gekoppelde Service Connection String bij te werken met een grotere time-outwaarde voor de verbinding.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Fout code: SqlAutoCreateTableTypeMapFailed

- **Bericht**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Oorzaak**: het automatisch maken van de tabel kan niet voldoen aan de bron vereiste.

- **Aanbeveling**: werk het kolom Type bij in toewijzingen of maak de Sink-tabel hand matig op doel server.


### <a name="error-code--sqldatatypenotsupported"></a>Fout code: SqlDataTypeNotSupported

- **Bericht**: `A database operation failed. Check the SQL errors.`

- **Oorzaak**: als het probleem zich voordoet op de SQL-bron en de fout is gerelateerd aan SqlDateTime overflow, ligt de gegevens waarde boven het logische type bereik (1/1/1753 12:00:00 uur 12/31/9999 11:59:59 pm).

- **Aanbeveling**: Converteer het type naar een teken reeks in de bron SQL-query, of wijzig de kolom Type in de kolom kopiëren van de Kopieer activiteit in ' teken reeks '.

- **Oorzaak**: als het probleem zich voordoet op SQL-Sink en de fout is gerelateerd aan SqlDateTime overflow, ligt de gegevens waarde boven het toegestane bereik in de Sink-tabel.

- **Aanbeveling**: werk het overeenkomstige kolom Type bij naar het type ' DATETIME2 ' in de Sink-tabel.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Fout code: SqlInvalidDbStoredProcedure

- **Bericht**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Oorzaak**: de opgegeven opgeslagen procedure is ongeldig. Dit kan worden veroorzaakt doordat de opgeslagen procedure geen gegevens retourneert.

- **Aanbeveling**: Valideer de opgeslagen procedure door SQL-hulpprogram ma's. Zorg ervoor dat de opgeslagen procedure gegevens kan retour neren.


### <a name="error-code--sqlinvaliddbquerystring"></a>Fout code: SqlInvalidDbQueryString

- **Bericht**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Oorzaak**: de opgegeven SQL-query is ongeldig. Dit kan worden veroorzaakt doordat de query geen gegevens retourneert

- **Aanbeveling**: Valideer de SQL-query door SQL-hulpprogram ma's. Zorg ervoor dat de query gegevens kan retour neren.


### <a name="error-code--sqlinvalidcolumnname"></a>Fout code: SqlInvalidColumnName

- **Bericht**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Oorzaak**: de kolom is niet gevonden. Mogelijke configuratie is onjuist.

- **Aanbeveling**: Controleer de kolom in de query ' Structure ' in dataset en ' mappings ' in de activiteit.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Fout code: SqlColumnNameMismatchByCaseSensitive

- **Bericht**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Fout code: SqlBatchWriteTimeout

- **Bericht**: `Timeouts in SQL write operation.`

- **Oorzaak**: kan SQL database tijdelijke fout zijn.

- **Aanbeveling**: Probeer het opnieuw. Als probleem reproduceren, neemt u contact op met Azure SQL-ondersteuning.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Fout code: SqlBatchWriteTransactionFailed

- **Bericht**: `SQL transaction commits failed`

- **Oorzaak**: als uitzonderings Details de time-out van de trans actie voortdurend vertelt, is de netwerk latentie tussen Integration runtime en data base hoger dan de standaard drempel waarde van 30 seconden.

- **Aanbeveling**: werk de gekoppelde SQL-Service Connection String met een time-out voor de verbinding is gelijk aan 120 of hoger en voer de activiteit opnieuw uit.

- **Oorzaak**: als de uitzonderings Details van onregelmatigheden SqlConnection zijn, kan dit gewoon leiden tot tijdelijke netwerk storingen of SQL Databasee zijde

- **Aanbeveling**: Voer de activiteit opnieuw uit en controleer de metrische gegevens van SQL database zijde.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Fout code: SqlBulkCopyInvalidColumnLength

- **Bericht**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Oorzaak**: het bulksgewijs kopiëren van SQL is mislukt omdat er een ongeldige kolom lengte is ontvangen van de BCP-client.

- **Aanbeveling**: als u wilt identificeren in welke rij het probleem zich voordoet, schakelt u de functie fout tolerantie in op Kopieer activiteit, waarmee u problematische rij (en) kunt omleiden naar de opslag voor verdere onderzoek. Verwijzings document: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Fout code: SqlConnectionIsClosed

- **Bericht**: `The connection is closed by SQL Database.`

- **Oorzaak**: de SQL-verbinding wordt gesloten door SQL database wanneer de verbinding hoog gelijktijdig wordt uitgevoerd en de server is beëindigd.

- **Aanbeveling**: externe server heeft de SQL-verbinding gesloten. Voeren. Als probleem reproduceren, neemt u contact op met Azure SQL-ondersteuning.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Fout code: SqlCreateTableFailedUnsupportedType

- **Bericht**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fout bericht: de conversie is mislukt tijdens het converteren van een teken reeks naar een unieke id

- **Symptomen**: wanneer u gegevens uit tabellaire gegevens bron (zoals SQL Server) naar Azure Synapse Analytics kopieert met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Oorzaak**: Azure Synapse Analytics poly Base kan geen lege teken reeks naar GUID converteren.

- **Oplossing**: Stel in Sink voor kopieer activiteiten onder poly base-instellingen de optie **type standaard gebruiken** in op ONWAAR.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fout bericht: verwacht gegevens type: decimaal (x, x), foutieve waarde

- **Symptomen**: wanneer u gegevens uit tabellaire gegevens bron (zoals SQL Server) naar Azure Synapse Analytics kopieert met behulp van gefaseerde kopie en poly Base, wordt de volgende fout weer gegeven:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Oorzaak**: Azure Synapse Analytics poly Base kan geen lege teken reeks (null-waarde) invoegen in een decimale kolom.

- **Oplossing**: Stel in Sink voor kopieer activiteiten onder poly base-instellingen de optie **type standaard gebruiken** in op ONWAAR.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fout bericht: Java-uitzonderings bericht: HdfsBridge:: CreateRecordReader

- **Symptomen**: u kopieert gegevens naar Azure Synapse Analytics met poly base en de volgende fout wordt weer gegeven:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Oorzaak**: de mogelijke oorzaak is dat het schema (totale kolom breedte) te groot is (groter dan 1 MB). Controleer het schema van de doel tabel van de Azure Synapse-analyse door de grootte van alle kolommen toe te voegen:

    - Int-> 4 bytes
    - Bigint-> 8 bytes
    - Varchar (n), char (n), binary (n), varbinary (n)-> n bytes
    - Nvarchar (n), nchar (n)-> n * 2 bytes
    - Datum-> 6 bytes
    - Datetime/(2), smalldatetime-> 16 bytes
    - Date time offset-> 20 bytes
    - Decimaal-> 19 bytes
    - Zwevende > 8 bytes
    - Money-> 8 bytes
    - Smallmoney-> 4 bytes
    - Real-> 4 bytes
    - Smallint-> 2 bytes
    - Tijd-> 12 bytes
    - Tinyint-> 1 byte

- **Oplossing**: Verklein de kolom breedte kleiner dan 1 MB

- Of gebruik bulksgewijze invoeg aanpak door poly Base uit te scha kelen


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fout bericht: er is niet voldaan aan de voor waarde die is opgegeven met een HTTP-header (n)

- **Symptomen**: met SQL query haalt u gegevens op uit Azure Synapse Analytics en houdt u de volgende fout op:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Oorzaak**: probleem met treffer van Azure Synapse Analytics bij het uitvoeren van een query op de externe tabel in azure Storage.

- **Oplossing**: Voer dezelfde query uit in SSMS en controleer of hetzelfde resultaat wordt weer geven. Als dit het geval is, opent u een ondersteunings ticket voor Azure Synapse Analytics en geeft u de naam van uw Azure Synapse Analytics-server en-Data Base op om verder te kunnen oplossen
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Lage prestaties bij het laden van gegevens in Azure SQL

- **Symptomen**: het kopiëren van gegevens naar Azure SQL verloopt langzaam.

- **Oorzaak**: de hoofd oorzaak van het probleem wordt meestal veroorzaakt door het knel punt van de Azure SQL-zijde. Hier volgen enkele mogelijke oorzaken:

    1. De Azure DB-laag is niet hoog genoeg.

    1. Het gebruik van Azure DB-DTU is bijna 100%. U kunt [de prestaties bewaken](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) en overwegen om de DB-laag bij te werken.

    1. De indexen zijn niet juist ingesteld. Verwijder alle indexen voordat u de gegevens laadt en maak deze opnieuw nadat het laden is voltooid.

    1. WriteBatchSize is niet groot genoeg om de grootte van de schemarij ruimte aan te passen. Probeer de eigenschap voor het probleem te verg Roten.

    1. In plaats van bulksgewijs inkrimpen, wordt opgeslagen procedure gebruikt, die naar verwachting de prestaties verergert. 

- **Oplossing**: Raadpleeg de TSG voor de prestaties van de [Kopieer activiteit](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>De prestatie tier is laag en het kopiëren van de fout is mislukt

- **Symptomen**: hieronder is een fout bericht opgetreden bij het kopiëren van gegevens naar Azure SQL: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Oorzaak**: Azure SQL S1 wordt gebruikt, waardoor de i/o-limieten in dergelijke gevallen worden bereikt.

- **Oplossing**: Voer een upgrade uit voor de Azure SQL-prestatie tier om het probleem op te lossen. 


### <a name="sql-table-cannot-be-found"></a>Kan de SQL-tabel niet vinden 

- **Symptomen**: er is een fout opgetreden bij het kopiëren van gegevens van hybride naar een on-premises SQL Server tabel:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Oorzaak**: het huidige SQL-account heeft onvoldoende machtigingen voor het uitvoeren van aanvragen die zijn uitgegeven door .net SqlBulkCopy. WriteToServer.

- **Oplossing**: Ga naar een meer PRIVILEGEd SQL-account.


### <a name="string-or-binary-data-would-be-truncated"></a>Teken reeks of binaire gegevens worden afgekapt

- **Symptomen**: er is een fout opgetreden bij het kopiëren van gegevens naar een on-premises/Azure SQL Server-tabel: 

- **Oorzaak**: de schema definitie van de CX SQL-tabel bevat een of meer kolommen met een korte lengte van verwachting.

- **Oplossing**: Voer de volgende stappen uit om het probleem op te lossen:

    1. Pas [fout tolerantie](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)toe, met name ' redirectIncompatibleRowSettings ', om problemen op te lossen met de rijen die het probleem hebben.

    1. Controleer de omgeleide gegevens met de kolom lengte van SQL-tabel schema om te zien welke kolom (len) moeten worden bijgewerkt.

    1. Update het tabel schema dienovereenkomstig.


## <a name="delimited-text-format"></a>Tekst indeling met scheidings tekens

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Fout code: DelimitedTextColumnNameNotAllowNull

- **Bericht**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Oorzaak**: wanneer ' firstRowAsHeader ' in de activiteit wordt ingesteld, wordt de eerste rij als kolom naam gebruikt. Deze fout geeft aan dat de eerste rij een lege waarde bevat. Bijvoorbeeld: ' koloma, ColumnB '.

- **Aanbeveling**: Controleer de eerste rij en los de waarde op als er een lege waarde is.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Fout code: DelimitedTextMoreColumnsThanDefined

- **Bericht**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Oorzaak**: het aantal kolommen in de problematische rij is groter dan het aantal kolommen in de eerste rij. Dit kan worden veroorzaakt door een gegevens probleem of door een onjuiste instelling van het scheidings teken voor kolom/aanhalings tekens.

- **Aanbeveling**: het aantal rijen in het fout bericht ophalen, de kolom van de rij controleren en de gegevens herstellen.

- **Oorzaak**: als het verwachte aantal kolommen in fout bericht ' 1 ' is, is het mogelijk dat u verkeerde compressie-of notatie-instellingen hebt opgegeven. De bestanden worden dus onjuist geparseerd.

- **Aanbeveling**: Controleer de indelings instellingen om er zeker van te zijn dat deze overeenkomt met uw bron bestand (en).

- **Oorzaak**: als uw bron een map is, is het mogelijk dat de bestanden in de opgegeven map een ander schema hebben.

- **Aanbeveling**: Zorg ervoor dat de bestanden in de opgegeven map een identiek schema hebben.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Fout code: DelimitedTextIncorrectRowDelimiter

- **Bericht**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Fout code: DelimitedTextTooLargeColumnCount

- **Bericht**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Fout code: DelimitedTextInvalidSettings

- **Bericht**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Fout code: DynamicsCreateServiceClientError

- **Bericht**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Oorzaak**: dit is een tijdelijk probleem op Dynamics Server-zijde.

- **Aanbeveling**: Voer de pijp lijn opnieuw uit. Als het probleem blijft optreden, kunt u proberen om de parallelle uitvoering te verminderen. Als de service nog steeds niet werkt, neemt u contact op met Dynamics-ondersteuning.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Er ontbreken kolommen wanneer een voor beeld van een schema wordt weer gegeven/geïmporteerd

- **Symptomen**: sommige kolommen worden niet meer weer gegeven bij het importeren van schema of het voorbekijken van gegevens. Fout bericht: `The valid structure information (column name and type) are required for Dynamics source.`

- **Oorzaak**: dit probleem is in principe gepaard met het ontwerp, omdat ADF geen kolommen kan weer geven die geen waarde in de eerste tien records bevatten. Controleer of de kolommen die u hebt toegevoegd, de juiste indeling hebben. 

- **Aanbeveling**: Voeg hand matig de kolommen toe op het tabblad toewijzing.


## <a name="excel-format"></a>Excel-indeling

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Time-out of langzame prestaties bij het parseren van een groot Excel-bestand

- **Symptomen**:

    1. Wanneer u Excel-gegevensset maakt en een schema importeert vanuit verbinding/archief, voor beeld van gegevens, lijst of werk bladen vernieuwen, kunt u een time-outfout opvragen als het Excel-bestand groot is.

    1. Wanneer u de Kopieer activiteit gebruikt om gegevens te kopiëren van een groot Excel-bestand (>= 100 MB) naar een ander gegevens archief, kunnen er trage prestaties of OOM problemen optreden.

- **Oorzaak**: 

    1. Voor bewerkingen zoals het importeren van schema, het weer geven van voor beelden van gegevens en het vermelden van werk bladen in Excel-gegevensset, is de time-out 100 en static. Voor een groot Excel-bestand kunnen deze bewerkingen niet worden voltooid binnen de time-outwaarde.

    2. Met de ADF Copy-activiteit wordt het hele Excel-bestand in het geheugen gelezen en vervolgens vindt het opgegeven werk blad en de cellen om gegevens te lezen. Dit gedrag is te wijten aan het gebruik van de onderliggende SDK.

- **Oplossing**: 

    1. Voor het importeren van schema kunt u een kleiner voorbeeld bestand genereren dat een subset is van het oorspronkelijke bestand en kiest u schema importeren uit voorbeeld bestand in plaats van schema importeren uit verbinding/archief.

    2. Voor het werk blad overzichten kunt u in de vervolg keuzelijst voor het werk blad op bewerken klikken en in plaats daarvan de blad naam/index invoeren.

    3. Als u een groot Excel-bestand (>100 MB) wilt kopiëren naar een ander archief, kunt u de Excel-bron gegevens stroom gebruiken die door sport streaming wordt gelezen en beter presteert.


## <a name="hdinsight"></a>HDInsight

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>SSL-fout bij het koppelen van een ADF-service met het HDInsight ESP-cluster

- **Bericht**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Oorzaak**: het probleem heeft waarschijnlijk te maken met het vertrouwens archief van het systeem.

- **Oplossing**: u kunt navigeren naar het pad **micro soft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** en DriverConfiguration64.exe openen om de instelling te wijzigen.

    ![Schakel het selectie vakje systeem vertrouwens opslag gebruiken uit](./media/connector-troubleshoot-guide/system-trust-store-setting.png)


## <a name="json-format"></a>JSON-indeling

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Fout code: JsonInvalidArrayPathDefinition

- **Bericht**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Fout code: JsonEmptyJObjectData

- **Bericht**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Fout code: JsonNullValueInPathDefinition

- **Bericht**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Fout code: JsonUnsupportedHierarchicalComplexValue

- **Bericht**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Fout code: JsonConflictPartitionDiscoverySchema

- **Bericht**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Fout code: JsonInvalidDataFormat

- **Bericht**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Fout code: JsonInvalidDataMixedArrayAndObject

- **Bericht**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Fout code: ArgumentOutOfRangeException

- **Bericht**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Oorzaak**: in ADF worden datetime-waarden ondersteund in het bereik van 0001-01-01 00:00:00 tot 9999-12-31 23:59:59. Oracle ondersteunt echter een breder bereik van datum/tijd-waarden (zoals BC Century of min/SEC>59), wat leidt tot een fout in ADF.

- **Aanbeveling**: 

    Voer uit `select dump(<column name>)` om te controleren of de waarde in Oracle binnen het bereik van ADF is. 

    Als u de byte volgorde in het resultaat wilt weten, controleert u https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="parquet-format"></a>Parquet-indeling

### <a name="error-code--parquetjavainvocationexception"></a>Fout code: ParquetJavaInvocationException

- **Bericht**: `An error occurred when invoking java, message: %javaException;.`

- **Oorzaak**: wanneer het fout bericht ' Java. lang. OutOfMemory ', ' Java heap Space ' en ' doubleCapacity ' bevat, is dit meestal een geheugen beheer probleem in de oude versie van Integration runtime.

- **Aanbeveling**: als u gebruikmaakt van zelf-hostende Integration runtime en de versie eerder is dan 3.20.7159.1, wordt u geadviseerd om een upgrade naar de meest recente versie uit te laten gaan.

- **Oorzaak**: wanneer het fout bericht ' Java. lang. OutOfMemory ' bevat, heeft de Integration runtime onvoldoende bronnen om de bestanden te verwerken.

- **Aanbeveling**: Beperk de gelijktijdige uitvoeringen van de Integration runtime. Voor zelf-hostende Integration Runtime schaalt u omhoog naar een krachtige computer met geheugen dat groter is dan of gelijk is aan 8 GB.

- **Oorzaak**: wanneer het fout bericht ' NullPointerReference ' bevat, is er mogelijk een tijdelijke fout.

- **Aanbeveling**: Probeer het opnieuw. Neem contact op met de ondersteuning als het probleem zich blijft voordoen.


### <a name="error-code--parquetinvalidfile"></a>Fout code: ParquetInvalidFile

- **Bericht**: `File is not a valid Parquet file.`

- **Oorzaak**: probleem met Parquet-bestand.

- **Aanbeveling**: Controleer of de invoer een geldig Parquet-bestand is.


### <a name="error-code--parquetnotsupportedtype"></a>Fout code: ParquetNotSupportedType

- **Bericht**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Oorzaak**: de Parquet-indeling wordt niet ondersteund in azure Data Factory.

- **Aanbeveling**: dubbel Controleer de bron gegevens. Raadpleeg het document: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Fout code: ParquetMissedDecimalPrecisionScale

- **Bericht**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Oorzaak**: Probeer de precisie en schaal van het getal te parseren, maar deze informatie wordt niet verstrekt.

- **Aanbeveling**: ' source ' retourneert niet de juiste precisie en schaal. Controleer de kolom precisie en schaal van het probleem.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Fout code: ParquetInvalidDecimalPrecisionScale

- **Bericht**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Oorzaak**: het schema is ongeldig.

- **Aanbeveling**: Controleer de probleem kolom nauw keurigheid en schaal.


### <a name="error-code--parquetcolumnnotfound"></a>Fout code: ParquetColumnNotFound

- **Bericht**: `Column %column; does not exist in Parquet file.`

- **Oorzaak**: het bron schema komt niet overeen met het sink-schema.

- **Aanbeveling**: Controleer the'mappings ' in ' activiteit '. Zorg ervoor dat de bron kolom kan worden toegewezen aan de rechter Sink-kolom.


### <a name="error-code--parquetinvaliddataformat"></a>Fout code: ParquetInvalidDataFormat

- **Bericht**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Oorzaak**: de gegevens kunnen niet worden geconverteerd naar het type dat is opgegeven in de toewijzingen. source

- **Aanbeveling**: dubbel Controleer de bron gegevens of geef het juiste gegevens type voor deze kolom op in de kolom toewijzing van de Kopieer activiteit. Raadpleeg het document: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Fout code: ParquetDataCountNotMatchColumnCount

- **Bericht**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Oorzaak**: aantal bron kolommen en aantal Sink-kolommen komen niet overeen

- **Aanbeveling**: dubbel aantal bron kolommen controleren is hetzelfde als het aantal Sink-kolommen in mapping.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Fout code: ParquetDataTypeNotMatchColumnType

- **Bericht**: het gegevens type% srcType; komt niet overeen met het opgegeven kolom Type% dstType; bij kolom% column index;.

- **Oorzaak**: gegevens van bron kunnen niet worden geconverteerd naar getypte definitie in Sink

- **Aanbeveling**: Geef een juist type op in mapping. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Fout code: ParquetBridgeInvalidData

- **Bericht**: `%message;`

- **Oorzaak**: gegevens waarde over de beperking

- **Aanbeveling**: Probeer het opnieuw. Als het probleem zich blijft voordoen, kunt u contact met ons opnemen.


### <a name="error-code--parquetunsupportedinterpretation"></a>Fout code: ParquetUnsupportedInterpretation

- **Bericht**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Oorzaak**: niet-ondersteund scenario

- **Aanbeveling**: ' ParquetInterpretFor ' mag niet ' sparkSql ' zijn.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Fout code: ParquetUnsupportFileLevelCompressionOption

- **Bericht**: `File level compression is not supported for Parquet.`

- **Oorzaak**: niet-ondersteund scenario

- **Aanbeveling**: Verwijder ' CompressionType ' in de nettolading.


### <a name="error-code--usererrorjniexception"></a>Fout code: UserErrorJniException

- **Bericht**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Oorzaak**: JVM kan niet worden gemaakt omdat sommige ongeldige (globale) argumenten zijn ingesteld.

- **Aanbeveling**: Meld u aan bij de computer die als host fungeert voor **elk knoop punt** van uw zelf-hostende IR. Controleer of de systeem variabele juist is ingesteld: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Start alle IR-knoop punten opnieuw op en voer de pijp lijn opnieuw uit.


### <a name="arithmetic-overflow"></a>Reken kundige overloop

- **Symptomen**: er is een fout bericht opgetreden bij het kopiëren van Parquet-bestanden: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Oorzaak**: momenteel alleen een decimale komma <= 38 en de lengte van het gehele getal onderdeel <= 20 wordt ondersteund bij het kopiëren van bestanden van Oracle naar Parquet. 

- **Oplossing**: u kunt kolommen met een dergelijk probleem omzetten in varchar2 als tijdelijke oplossing.


### <a name="no-enum-constant"></a>Geen Enum-constante

- **Symptomen**: er is een fout bericht opgetreden bij het kopiëren van gegevens naar de Parquet-indeling: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , of: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Oorzaak**: 

    Het probleem kan worden veroorzaakt door spaties of niet-ondersteunde tekens (zoals,; {} () \n\t =) in kolom naam, omdat Parquet dergelijke indeling niet ondersteunt. 

    Bijvoorbeeld: kolom naam, zoals *Contoso (test)* , parseert het type tussen vier Kante haken van [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . De fout wordt gegenereerd omdat er geen dergelijk test type is.

    Als u de ondersteunde typen wilt controleren, kunt u deze [hier](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)controleren.

- **Oplossing**: 

    1. Controleer of er spaties in de kolom naam van de Sink staan.

    1. Controleer of de eerste rij met spaties als kolom naam wordt gebruikt.

    1. Controleer of het type OriginalType wel of niet wordt ondersteund. Probeer deze speciale symbolen te vermijden `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="unexpected-network-response-from-rest-connector"></a>Onverwachte netwerk reactie van REST-connector

- **Symptomen**: het eind punt ontvangt soms een onverwachte reactie (400/401/403/500) van de rest-connector.

- **Oorzaak**: de rest-bron connector gebruikt de URL en de HTTP-methode/koptekst/hoofd code van de gekoppelde service/gegevensset/Kopieer bron als para meters bij het samen stellen van een HTTP-aanvraag. Het probleem wordt waarschijnlijk veroorzaakt door enkele fouten in een of meer opgegeven para meters.

- **Oplossing**: 
    - Gebruik ' krul ' in het CMD-venster om te controleren of para meter de oorzaak is of niet (**accepteren** en **gebruikers agent** headers moeten altijd worden opgenomen):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Als de opdracht dezelfde onverwachte reactie retourneert, moet u de bovenstaande para meters met ' krul ' herstellen totdat het verwachte antwoord wordt geretourneerd. 

      U kunt ook ' krul--Help ' gebruiken voor meer geavanceerd gebruik van de opdracht.

    - Neem contact op met micro soft ondersteuning als alleen een ADF-REST-connector een onverwachte reactie retourneert.
    
    - Houd er rekening mee dat ' krul ' mogelijk niet geschikt is om het probleem met het valideren van het SSL-certificaat te reproduceren. In sommige scenario's is de opdracht ' krul ' uitgevoerd zonder dat er een probleem is met het valideren van een SSL-certificaat. Maar wanneer dezelfde URL wordt uitgevoerd in de browser, wordt er in de eerste plaats geen SSL-certificaat geretourneerd, zodat de client geen vertrouwens relatie met de server tot stand kan brengen.

      Hulpprogram ma's zoals **postman** en **Fiddler** worden aanbevolen voor het bovenstaande geval.


## <a name="sftp"></a>SFTP

### <a name="invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Er is een ongeldige SFTP-referentie gegeven voor het verificatie type SSHPublicKey

- **Symptomen**: de open bare SSH-sleutel verificatie wordt gebruikt terwijl een ongeldige SFTP-referentie wordt aangeboden voor het verificatie type SshPublicKey.

- **Oorzaak**: deze fout kan drie mogelijke oorzaken hebben:

    1. De inhoud van een persoonlijke sleutel wordt opgehaald uit Azure/SDK, maar is niet juist gecodeerd.

    1. De indeling van de onjuiste sleutel inhoud is gekozen.

    1. Ongeldige inhoud voor referentie of persoonlijke sleutel.

- **Oplossing**: 

    1. Voor **Oorzaak 1**:

       Als de inhoud van een persoonlijke sleutel afkomstig is uit Azure en het oorspronkelijke sleutel bestand kan worden gebruikt als de klant deze rechtstreeks naar een gekoppelde SFTP-service uploadt

       Raadpleeg https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication de privateKey-inhoud is een base64-gecodeerde SSH-inhoud met een persoonlijke sleutel.

       Versleutel **de volledige inhoud van de oorspronkelijke persoonlijke sleutel** met base64-code ring en sla de versleutelde teken reeks op in Azure. De oorspronkelijke persoonlijke sleutel is de naam die kan worden gebruikt voor de gekoppelde service van SFTP als u op uploaden van bestand klikt.

       Hier volgen enkele voor beelden die worden gebruikt voor het genereren van de teken reeks:

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

       - Hulp programma voor Base64-conversie van derden gebruiken

         Hulpprogram ma's zoals https://www.base64encode.org/ aanbevolen.

    1. Voor **oorzaak 2**:

       Als PKCS # 8-indeling SSH-persoonlijke sleutel wordt gebruikt

       PKCS # 8-persoonlijke SSH-sleutel (beginnen met '-----beginnen met de versleutelde persoonlijke sleutel-----') wordt momenteel niet ondersteund voor toegang tot de SFTP-server in ADF. 

       Voer de onderstaande opdrachten uit om de sleutel om te zetten in een traditionele SSH-sleutel notatie (begin met '-----BEGIN RSA-sleutel-----'):

       ```
       openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
       chmod 600 traditional_format_key_file
       ssh-keygen -f traditional_format_key_file -p
       ```
    1. Voor **oorzaak 3**:

       Controleer of het sleutel bestand of het wacht woord juist is.


### <a name="incorrect-linked-service-type-is-used"></a>Er wordt een onjuist gekoppeld Service type gebruikt

- **Symptomen**: FTP/SFTP-server kan niet worden bereikt.

- **Oorzaak**: onjuist gekoppeld Service type wordt gebruikt voor FTP-of SFTP-server, zoals het gebruik van een met FTP gekoppelde service om verbinding te maken met een SFTP-server of omgekeerd.

- **Oplossing**: Controleer de poort van de doel server. FTP maakt standaard gebruik van poort 21 en SFTP gebruikt poort 22.


### <a name="sftp-copy-activity-failed"></a>SFTP-Kopieer activiteit is mislukt

- **Symptomen**: fout code: UserErrorInvalidColumnMappingColumnNotFound. Fout bericht: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Oorzaak**: de bron bevat geen kolom met de naam ' AccMngr '.

- **Oplossing**: dubbel Controleer hoe uw gegevensset is geconfigureerd door de kolom doel gegevensset te koppelen om te controleren of er een dergelijke ' AccMngr-kolom is.


### <a name="sftp-server-connection-throttling"></a>Verbinding beperken met SFTP-server

- **Symptomen**: Server respons bevat geen SSH-protocol-id en kan niet worden gekopieerd.

- **Oorzaak**: ADF maakt het mogelijk om meerdere verbindingen te maken die worden gedownload van de sftp-server en soms door de sftp-server beperking te bereiken. Vrijwel elke server retourneert een andere fout wanneer de beperking wordt bereikt.

- **Oplossing**: 

    Geef het maximale aantal gelijktijdige verbindingen van de SFTP-gegevensset op 1 en voer de kopie opnieuw uit. Als de bewerking is geslaagd, kunnen we ervoor zorgen dat beperking de oorzaak is.

    Als u de lage door voer wilt verhogen, neemt u contact op met de SFTP-beheerder om de limiet voor gelijktijdige verbindingen te verhogen of voegt u het volgende IP-adres toe aan de lijst met toegestane items:

    - Als u beheerde IR gebruikt, moet u [IP-adresbereiken voor Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)toevoegen.
      U kunt ook zelf-hostende IR installeren als u geen grote lijst met IP-adresbereiken wilt toevoegen aan de acceptatie lijst van de SFTP-server.

    - Als u een zelf-hostende IR gebruikt, voegt u het IP-adres van de machine toe die is geïnstalleerd op de acceptatie lijst SHIR.


### <a name="error-code-sftprenameoperationfail"></a>Fout code: SftpRenameOperationFail

- **Symptomen**: de pijp lijn kan geen gegevens kopiëren van de BLOB naar SFTP met de volgende fout: `Operation on target Copy_5xe failed: Failure happened on 'Sink' side. ErrorCode=SftpRenameOperationFail,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException` .

- **Oorzaak**: de optie useTempFileRename is ingesteld als waar bij het kopiëren van de gegevens. Hiermee kan het proces tijdelijke bestanden gebruiken. De fout wordt geactiveerd als een of meer tijdelijke bestanden zijn verwijderd voordat de hele gegevens worden gekopieerd.

- **Oplossing**: Stel de optie useTempFileName in op false.


## <a name="general-copy-activity-error"></a>Fout met algemene Kopieer activiteit

### <a name="error-code--jrenotfound"></a>Fout code: JreNotFound

- **Bericht**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Oorzaak**: de zelf-hostende Integration runtime kan Java runtime niet vinden. De Java-runtime is vereist voor het lezen van een bepaalde bron.

- **Aanbeveling**: Controleer uw Integration runtime-omgeving, het referentie document: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Fout code: WildcardPathSinkNotSupported

- **Bericht**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Oorzaak**: Sink-gegevensset biedt geen ondersteuning voor joker tekens.

- **Aanbeveling**: Controleer de Sink-gegevensset en herstel het pad zonder joker waarde.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Fout code: MappingInvalidPropertyWithEmptyValue

- **Bericht**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Fout code: MappingInvalidPropertyWithNamePathAndOrdinal

- **Bericht**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Fout code: MappingDuplicatedOrdinal

- **Bericht**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Fout code: MappingInvalidOrdinalForSinkColumn

- **Bericht**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
