---
title: Fouttolerantie van kopieeractiviteit in Azure Data Factory
description: Meer informatie over het toevoegen van fout tolerantie aan de Kopieer activiteit in Azure Data Factory door de niet-compatibele gegevens over te slaan.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390960"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Fouttolerantie van kopieeractiviteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Huidige versie](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wanneer u gegevens van de bron naar het doel archief kopieert, biedt Azure Data Factory Kopieer activiteit een bepaald aantal fout toleranties om onderbrekingen van storingen in het midden van de verplaatsing van gegevens te voor komen. U kopieert bijvoorbeeld miljoenen rijen van bron naar doel archief, waar een primaire sleutel is gemaakt in de doel database, maar voor de bron database geen primaire sleutels zijn gedefinieerd. Wanneer u dubbele rijen van de bron naar het doel kopieert, raakt u de fout met de PK-fout op de doel database. Op dit moment biedt Kopieer activiteit u twee manieren om dergelijke fouten af te handelen: 
- U kunt de Kopieer activiteit afbreken zodra er fouten zijn opgetreden. 
- U kunt door gaan met het kopiëren van de rest door fout tolerantie in te scha kelen om de niet-compatibele gegevens over te slaan. U kunt bijvoorbeeld de dubbele rij in dit geval overs Laan. Daarnaast kunt u de overgeslagen gegevens registreren door het sessie logboek in een Kopieer activiteit in te scha kelen. Raadpleeg het [sessie logboek in de Kopieer activiteit](copy-activity-log.md) voor meer informatie.

## <a name="copying-binary-files"></a>Binaire bestanden kopiëren 

ADF ondersteunt de volgende fout tolerante scenario's bij het kopiëren van binaire bestanden. U kunt ervoor kiezen de Kopieer activiteit af te breken of door te gaan met het kopiëren van de rest in de volgende scenario's:

1. De bestanden die door ADF moeten worden gekopieerd, worden op hetzelfde moment verwijderd door andere toepassingen.
2. Voor sommige mappen of bestanden is ADF niet toegestaan, omdat Acl's van deze bestanden of mappen een hoger machtigings niveau hebben dan de verbindings gegevens die in ADF zijn geconfigureerd.
3. Een of meer bestanden worden niet geverifieerd om consistent te zijn tussen het bron-en doel archief als u de instelling voor verificatie van gegevens consistentie in ADF inschakelt.

### <a name="configuration"></a>Configuratie 
Wanneer u binaire bestanden tussen opslag archieven kopieert, kunt u fout tolerantie als volgt inschakelen: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | -------- 
skipErrorFile | Een groep eigenschappen om de typen fouten op te geven die u wilt overs Laan tijdens het verplaatsen van gegevens. | | Nee
fileMissing | Een van de sleutel-waardeparen in skipErrorFile eigenschappen verzameling om te bepalen of u bestanden wilt overs laan die door andere toepassingen worden verwijderd wanneer ADF in de tussen tijd wordt gekopieerd. <br/> -True: u wilt de rest kopiëren door de bestanden die worden verwijderd door andere toepassingen over te slaan. <br/> -False: u wilt de Kopieer activiteit afbreken zodra er bestanden uit het bron archief worden verwijderd in het midden van het verplaatsen van gegevens. <br/>Houd er rekening mee dat deze eigenschap is ingesteld op True als standaard. | True (standaard) <br/>Niet waar | Nee
fileForbidden | Een van de sleutel-waardeparen in de verzameling skipErrorFile om te bepalen of u de specifieke bestanden wilt overs Laan, wanneer de Acl's van deze bestanden of mappen een hoger machtigings niveau hebben dan de verbinding die in ADF is geconfigureerd. <br/> -True: u wilt de rest kopiëren door de bestanden over te slaan. <br/> -False: u wilt de Kopieer activiteit afbreken zodra u het probleem met de machtiging voor mappen of bestanden hebt opgehaald. | Waar <br/>False (standaard) | Nee
dataInconsistency | Een van de sleutel-waardeparen in de skipErrorFile-eigenschappen verzameling om te bepalen of u de inconsistente gegevens tussen de bron-en doel opslag wilt overs Laan. <br/> -True: u wilt de rest kopiëren door inconsistente gegevens over te slaan. <br/> -False: u wilt de Kopieer activiteit afbreken zodra inconsistente gegevens zijn gevonden. <br/>Houd er rekening mee dat deze eigenschap alleen geldig is wanneer u validateDataConsistency instelt als waar. | Waar <br/>False (standaard) | Nee
invalidFileName | Een van de sleutel-waardeparen in de skipErrorFile-eigenschappen verzameling om te bepalen of u de specifieke bestanden wilt overs Laan, wanneer de bestands namen ongeldig zijn voor het doel archief. <br/> -True: u wilt de rest kopiëren door de bestanden met ongeldige bestands namen over te slaan. <br/> -False: u wilt de Kopieer activiteit afbreken zodra alle bestanden Ongeldige bestands namen hebben. <br/>Houd er rekening mee dat deze eigenschap werkt bij het kopiëren van binaire bestanden uit een opslag archief naar het ADLS Gen2 of kopiëren van binaire bestanden van AWS S3 naar een opslag archief. | Waar <br/>False (standaard) | Nee
logSettings  | Een groep eigenschappen die kan worden opgegeven wanneer u de overgeslagen object namen wilt vastleggen. | &nbsp; | Nee
linkedServiceName | De gekoppelde service van [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) voor het opslaan van de sessie logboek bestanden. | De namen van de `AzureBlobStorage` gekoppelde service van een of het `AzureBlobFS` type, die verwijst naar het exemplaar dat u gebruikt voor het opslaan van het logboek bestand. | Nee
leertraject | Het pad van de logboek bestanden. | Geef het pad op dat u gebruikt om de logboek bestanden op te slaan. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

> [!NOTE]
> Hier volgen de vereisten voor het inschakelen van fout tolerantie in Kopieer activiteit bij het kopiëren van binaire bestanden.
> Voor het overs laan van bepaalde bestanden wanneer deze uit het bron archief worden verwijderd:
> - De bron-gegevensset en de Sink-gegevensset moeten een binaire indeling hebben en het compressie type kan niet worden opgegeven. 
> - De ondersteunde typen gegevens opslag zijn Azure Blob-opslag, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, bestands systeem, FTP, SFTP, Amazon S3, Google Cloud Storage en HDFS.
> - Alleen als u meerdere bestanden in de bron-gegevensset opgeeft die een map, Joker teken of lijst met bestanden kunnen zijn, kunt u de specifieke fout bestanden overs Laan. Als er één bestand is opgegeven in de bron-gegevensset die naar het doel moet worden gekopieerd, mislukt de Kopieer activiteit als er een fout is opgetreden.
>
> Voor het overs laan van bepaalde bestanden wanneer de toegang is verboden vanuit het bron archief:
> - De bron-gegevensset en de Sink-gegevensset moeten een binaire indeling hebben en het compressie type kan niet worden opgegeven. 
> - De ondersteunde typen gegevens opslag zijn Azure Blob-opslag, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 en HDFS.
> - Alleen als u meerdere bestanden in de bron-gegevensset opgeeft die een map, Joker teken of lijst met bestanden kunnen zijn, kunt u de specifieke fout bestanden overs Laan. Als er één bestand is opgegeven in de bron-gegevensset die naar het doel moet worden gekopieerd, mislukt de Kopieer activiteit als er een fout is opgetreden.
>
> Voor het overs laan van bepaalde bestanden wanneer ze zijn geverifieerd om inconsistent te zijn tussen de bron-en doel opslag:
> - U vindt [hier](./copy-activity-data-consistency.md)meer informatie over het gegevens consistentie document.

### <a name="monitoring"></a>Bewaking 

#### <a name="output-from-copy-activity"></a>Uitvoer van Kopieer activiteit
U kunt het aantal bestanden dat wordt gelezen, geschreven en overgeslagen, ophalen via de uitvoer van elke Kopieer activiteit. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Sessie logboek van Kopieer activiteit

Als u configureert om de overgeslagen bestands namen te registreren, kunt u het logboek bestand vinden in dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

De logboek bestanden moeten de CSV-bestanden zijn. Het schema van het logboek bestand is als volgt:

Kolom | Beschrijving 
-------- | -----------  
Tijdstempel | Het tijds tempel wanneer ADF het bestand overs laat.
Niveau | Het logboek niveau van dit item. Het wordt weer gegeven in het waarschuwings niveau voor het item met het overs laan van bestanden.
OperationName | Operationeel gedrag van de ADF-Kopieer activiteit op elk bestand. Het wordt ' FileSkip ' om het bestand op te geven dat moet worden overgeslagen.
OperationItem | De namen van de bestanden die moeten worden overgeslagen.
Bericht | Meer informatie om te illustreren waarom het bestand wordt overgeslagen.

Het voor beeld van een logboek bestand is als volgt: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
In het bovenstaande logboek kunt u zien bigfile.csv is overgeslagen omdat een andere toepassing dit bestand heeft verwijderd toen de ADF werd gekopieerd. En 3_nopermission.txt is overgeslagen omdat ADF geen toegang heeft tot de sjabloon vanwege een probleem met de machtiging.


## <a name="copying-tabular-data"></a>Tabellaire gegevens kopiëren 

### <a name="supported-scenarios"></a>Ondersteunde scenario's
De Kopieer activiteit ondersteunt drie scenario's voor het detecteren, overs Laan en het vastleggen van incompatibele tabellaire gegevens:

- **Incompatibiliteit tussen het type bron gegevens en het systeem eigen type Sink**. 

    Bijvoorbeeld: gegevens uit een CSV-bestand in de Blob-opslag kopiëren naar een SQL database met een schema definitie die drie typen kolommen van het type INT bevat. De rijen met het CSV-bestand die numerieke gegevens bevatten, zoals 123.456.789, worden naar de Sink Store gekopieerd. De rijen met niet-numerieke waarden, zoals 123.456, ABC worden echter gedetecteerd als incompatibel en worden overgeslagen.

- **Het aantal kolommen tussen de bron en de Sink komt niet overeen**.

    Bijvoorbeeld: gegevens uit een CSV-bestand in Blob Storage kopiëren naar een SQL database met een schema definitie die zes kolommen bevat. De rijen met het CSV-bestand die zes kolommen bevatten, worden naar de Sink-Store gekopieerd. De rijen met het CSV-bestand met meer dan zes kolommen worden gedetecteerd als incompatibel en worden overgeslagen.

- **Schending van primaire sleutel bij het schrijven naar SQL Server/Azure SQL database/Azure Cosmos DB**.

    Bijvoorbeeld: gegevens kopiëren van een SQL-Server naar een SQL database. Er wordt een primaire sleutel gedefinieerd in de Sink-SQL database, maar er is geen dergelijke primaire sleutel gedefinieerd in de SQL-bron server. De dubbele rijen die aanwezig zijn in de bron, kunnen niet naar de Sink worden gekopieerd. Met de Kopieer activiteit wordt alleen de eerste rij van de bron gegevens naar de Sink gekopieerd. De volgende bron rijen die de dubbele primaire-sleutel waarde bevatten, worden gedetecteerd als incompatibel en worden overgeslagen.

>[!NOTE]
>- Als u gegevens in azure Synapse Analytics wilt laden met poly Base, configureert u de systeem eigen fout tolerantie-instellingen van poly Base door beleid voor afwijzen via '[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)' op te geven in de Kopieer activiteit. U kunt nog steeds niet-compatibele poly base-rijen omleiden naar BLOB of ADLS, zoals hieronder wordt weer gegeven.
>- Deze functie is niet van toepassing wanneer Kopieer activiteit is geconfigureerd voor het aanroepen van [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Deze functie is niet van toepassing wanneer Kopieer activiteit is geconfigureerd voor het aanroepen [van een opgeslagen procedure vanuit een SQL-Sink](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden voor het overs laan van de niet-compatibele rijen in een Kopieer activiteit:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Hiermee wordt aangegeven of niet-compatibele rijen moeten worden overgeslagen tijdens het kopiëren of niet. | Waar<br/>False (standaard) | Nee
logSettings | Een groep eigenschappen die kan worden opgegeven wanneer u de niet-compatibele rijen wilt vastleggen. | &nbsp; | Nee
linkedServiceName | De gekoppelde service van [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) om het logboek op te slaan dat de overgeslagen rijen bevat. | De namen van de `AzureBlobStorage` gekoppelde service van een of het `AzureBlobFS` type, die verwijst naar het exemplaar dat u gebruikt voor het opslaan van het logboek bestand. | Nee
leertraject | Het pad van de logboek bestanden met de overgeslagen rijen. | Geef het pad op dat u wilt gebruiken om de niet-compatibele gegevens te registreren. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

### <a name="monitor-skipped-rows"></a>Overgeslagen rijen bewaken
Nadat het uitvoeren van de Kopieer activiteit is voltooid, kunt u het aantal overgeslagen rijen zien in de uitvoer van de Kopieer activiteit:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Als u configureert om de niet-compatibele rijen te registreren, kunt u het logboek bestand vinden in dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

De logboek bestanden worden de CSV-bestanden. Het schema van het logboek bestand is als volgt:

Kolom | Beschrijving 
-------- | -----------  
Tijdstempel | Het tijds tempel voor het overs laan van de niet-compatibele rijen in de ADF
Niveau | Het logboek niveau van dit item. Het wordt weer gegeven in het waarschuwings niveau als dit item de overgeslagen rijen weergeeft
OperationName | Operationeel gedrag van de ADF-Kopieer activiteit op elke rij. Het is ' TabularRowSkip ' om aan te geven dat de bepaalde incompatibele rij is overgeslagen
OperationItem | De overgeslagen rijen uit de brongegevens opslag.
Bericht | Meer informatie voor het illustreren van de incompatibiliteit van deze rij.


Een voor beeld van de inhoud van het logboek bestand is als volgt:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

In het bovenstaande voor beeld logboek bestand ziet u dat er één rij "bestand1, bestand2, DATA3" is overgeslagen vanwege een probleem met het type conversie van bron naar doel archief. Een andere rij "data4, data5, data6" is overgeslagen vanwege een PK-schendings probleem van de bron naar het doel archief. 


## <a name="copying-tabular-data-legacy"></a>Tabellaire gegevens kopiëren (verouderd):

Hieronder vindt u de verouderde manier om fout tolerantie in te scha kelen voor het kopiëren van gegevens in tabel vorm. Als u een nieuwe pijp lijn of activiteit maakt, wordt u aangeraden om in [plaats hiervan](#copying-tabular-data) te beginnen.

### <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden voor het overs laan van de niet-compatibele rijen in een Kopieer activiteit:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Hiermee wordt aangegeven of niet-compatibele rijen moeten worden overgeslagen tijdens het kopiëren of niet. | Waar<br/>False (standaard) | Nee
redirectIncompatibleRowSettings | Een groep eigenschappen die kan worden opgegeven wanneer u de niet-compatibele rijen wilt vastleggen. | &nbsp; | Nee
linkedServiceName | De gekoppelde service van [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) om het logboek op te slaan dat de overgeslagen rijen bevat. | De namen van een `AzureStorage` of het `AzureDataLakeStore` type gekoppelde service, die verwijst naar het exemplaar dat u wilt gebruiken om het logboek bestand op te slaan. | Nee
leertraject | Het pad naar het logboek bestand dat de overgeslagen rijen bevat. | Geef het pad op dat u wilt gebruiken om de niet-compatibele gegevens te registreren. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

### <a name="monitor-skipped-rows"></a>Overgeslagen rijen bewaken
Nadat het uitvoeren van de Kopieer activiteit is voltooid, kunt u het aantal overgeslagen rijen zien in de uitvoer van de Kopieer activiteit:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Als u configureert om de niet-compatibele rijen te registreren, kunt u het logboek bestand vinden op dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

De logboek bestanden kunnen alleen de CSV-bestanden zijn. De oorspronkelijke gegevens die worden overgeslagen, worden indien nodig geregistreerd met een komma als kolom scheidings teken. We voegen nog twee meer kolommen "error code" en "ErrorMessage" toe aan de oorspronkelijke bron gegevens in het logboek bestand, waar u de hoofd oorzaak van de incompatibiliteit kunt zien. De fout code en ErrorMessage worden tussen dubbele aanhalings tekens geplaatst. 

Een voor beeld van de inhoud van het logboek bestand is als volgt:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Prestaties van kopieeractiviteit](copy-activity-performance.md)