---
title: Sessie logboek in Kopieer activiteit
description: Meer informatie over het inschakelen van sessie logboek in een Kopieer activiteit in Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385265"
---
#  <a name="session-log-in-copy-activity"></a>Sessie logboek in Kopieer activiteit

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt de gekopieerde bestands namen in de Kopieer activiteit registreren, zodat u er verder van kunt zorgen dat de gegevens niet worden gekopieerd van de bron naar het doel archief, maar ook consistent zijn tussen het bron-en doel Archief door de gekopieerde bestanden in de sessie logboeken van de Kopieer activiteit te controleren.  

Wanneer u de instelling fout tolerantie in Kopieer activiteit inschakelt om defecte gegevens over te slaan, kunnen de overgeslagen bestanden en overgeslagen rijen ook worden vastgelegd.  U kunt meer informatie krijgen over [fout tolerantie in de Kopieer activiteit](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden om het sessie logboek in te scha kelen in de Kopieer activiteit: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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
enableCopyActivityLog | Wanneer u deze instelt op True, hebt u de mogelijkheid om gekopieerde bestanden, overgeslagen bestanden of overgeslagen rijen te registreren.  | Waar<br/>False (standaard) | No
logLevel | Met "info" worden alle gekopieerde bestanden, overgeslagen bestanden en overgeslagen rijen geregistreerd. Als u ' waarschuwing ' wilt, worden overgeslagen bestanden en overgeslagen rijen alleen door het logboek vastgelegd.  | Info<br/>Waarschuwing (standaard) | No
enableReliableLogging | Als deze waarde True is, worden logboeken onmiddellijk door de Kopieer activiteit uit de betrouw bare modus gewist zodra elk bestand naar de bestemming is gekopieerd.  Wanneer u grote hoeveel heden bestanden kopieert met een betrouw bare logboek registratie modus die is ingeschakeld in de Kopieer activiteit, zou u kunnen verwachten dat de door Voer van de Kopieer bewerking wordt beïnvloed, omdat dubbele schrijf bewerkingen vereist zijn voor elk gekopieerde bestand. Er is een aanvraag naar het doel archief en een andere aanvraag is aan de archief opslag van het logboek.  Met de Kopieer activiteit in de modus Best effort worden logboeken met Batch records verwijderd binnen een bepaalde periode, waarbij de door Voer van de Kopieer bewerking veel minder gevolgen heeft. De volledigheid en het moment van vastleggen van logboek registraties worden niet gegarandeerd in deze modus, omdat er een aantal mogelijkheden zijn dat de laatste batch met logboek gebeurtenissen niet is leeg gemaakt naar het logboek bestand als de Kopieer activiteit is mislukt. Op dit moment ziet u een aantal bestanden die naar de bestemming zijn gekopieerd, worden niet geregistreerd.  | Waar<br/>False (standaard) | No
logLocationSettings | Een groep eigenschappen die kan worden gebruikt om de locatie op te geven voor het opslaan van de sessie Logboeken. | | No
linkedServiceName | De gekoppelde service van [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) voor het opslaan van de sessie logboek bestanden. | De namen van een `AzureBlobStorage` of meer `AzureBlobFS` gekoppelde service, die verwijst naar het exemplaar dat u gebruikt om de logboek bestanden op te slaan. | No
leertraject | Het pad van de logboek bestanden. | Geef het pad op waarin u de logboek bestanden wilt opslaan. Als u geen pad opgeeft, maakt de service een container voor u. | No


## <a name="monitoring"></a>Bewaking

### <a name="output-from-copy-activity"></a>Uitvoer van Kopieer activiteit
Nadat de Kopieer activiteit volledig is uitgevoerd, ziet u het pad van de logboek bestanden uit de uitvoer van elke uitvoering van de Kopieer activiteit. U kunt de logboek bestanden vinden op het pad: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  De logboek bestanden worden de CSV-bestanden. 

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

> [!NOTE]
> Wanneer de `enableCopyActivityLog` eigenschap is ingesteld op `Enabled` , worden de namen van het logboek bestand door het systeem gegenereerd.

### <a name="the-schema-of-the-log-file"></a>Het schema van het logboek bestand

Hier volgt het schema van een logboek bestand.

Kolom | Beschrijving 
-------- | -----------  
Tijdstempel | De tijds tempel wanneer ADF het object leest, schrijft of overs laat.
Niveau | Het logboek niveau van dit item. Dit kan ' warning ' of ' info ' zijn.
OperationName | Operationeel gedrag van de ADF-Kopieer activiteit op elk object. Dit kan ' FileRead ', ' FileWrite ', ' FileSkip ' of ' TabularRowSkip ' zijn.
OperationItem | De bestands namen of overgeslagen rijen.
Bericht | Meer informatie om weer te geven als het bestand is gelezen uit het bron archief of is geschreven naar het doel archief. Het kan ook zijn dat het bestand of de rijen worden overgeslagen.

Hier volgt een voor beeld van een logboek bestand. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
In het bovenstaande logboek bestand ziet u dat sample1.csv is overgeslagen, omdat het niet is gelukt om te controleren of het bron-en doel archief consistent zijn. U kunt meer informatie krijgen over waarom sample1.csv inconsistent wordt omdat deze wordt gewijzigd door andere toepassingen wanneer de activiteit ADF kopiëren op hetzelfde moment wordt gekopieerd. U kunt ook zien sample2.csv is gekopieerd van bron naar doel archief.

U kunt meerdere analyse-engines gebruiken om de logboek bestanden verder te analyseren.  Hieronder volgen enkele voor beelden van het gebruik van SQL query voor het analyseren van het logboek bestand door CSV-logboek bestand te importeren naar SQL database waarbij de tabel naam kan worden SessionLogDemo.  

-   Geef me de lijst met gekopieerde bestanden weer. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Geef aan dat de lijst met bestanden binnen een bepaald tijds bereik is gekopieerd. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Geef me een bepaald bestand met de gekopieerde tijd en meta gegevens. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Ik wil een lijst met bestanden waarvan de meta gegevens zijn gekopieerd binnen een tijds bereik. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Geef me de lijst overgeslagen bestanden weer. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Geef me de reden waarom een bepaald bestand is overgeslagen. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Geef me een lijst van bestanden die zijn overgeslagen vanwege dezelfde reden: het BLOB-bestand bestaat niet. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Geef me de bestands naam op waarvoor de langste tijd nodig is om te kopiëren.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Fout tolerantie van Kopieer activiteit](copy-activity-fault-tolerance.md)
- [Gegevens consistentie van Kopieer activiteit](copy-activity-data-consistency.md)
