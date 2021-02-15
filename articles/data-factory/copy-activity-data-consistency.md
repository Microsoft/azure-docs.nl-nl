---
title: Verificatie van de gegevens consistentie in de Kopieer activiteit
description: Meer informatie over het inschakelen van de verificatie van gegevens consistentie in de Kopieer activiteit in Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373013"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Verificatie van de gegevens consistentie in de Kopieer activiteit

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wanneer u gegevens van de bron naar het doel archief verplaatst, biedt Azure Data Factory Kopieer activiteit een optie waarmee u aanvullende gegevens consistentie verificatie kunt uitvoeren om ervoor te zorgen dat de gegevens niet alleen worden gekopieerd van de bron naar het doel archief, maar ook zijn geverifieerd om consistent te zijn tussen de bron-en doel opslag. Zodra er inconsistente bestanden zijn gevonden tijdens het verplaatsen van gegevens, kunt u de Kopieer activiteit afbreken of door gaan met het kopiëren van de rest door de instelling fout tolerantie in te scha kelen om inconsistente bestanden over te slaan. U kunt de overgeslagen bestands namen ophalen door de instelling voor sessie logboek in de Kopieer activiteit in te scha kelen. Raadpleeg het [sessie logboek in de Kopieer activiteit](copy-activity-log.md) voor meer informatie.

## <a name="supported-data-stores-and-scenarios"></a>Ondersteunde gegevens archieven en-scenario's

-   Verificatie van gegevens consistentie wordt ondersteund door alle connectors behalve FTP, sFTP en HTTP. 
-   Verificatie van gegevens consistentie wordt niet ondersteund in het faserings kopie scenario.
-   Bij het kopiëren van binaire bestanden is verificatie van gegevens consistentie alleen beschikbaar wanneer het gedrag PreserveHierarchy is ingesteld in de Kopieer activiteit.
-   Bij het kopiëren van meerdere binaire bestanden in één Kopieer activiteit met gegevens consistentie verificatie ingeschakeld, hebt u de mogelijkheid om de Kopieer activiteit af te breken of door te gaan met het kopiëren van de rest door de instelling fout tolerantie in te scha kelen om inconsistente bestanden over te slaan. 
-   Bij het kopiëren van een tabel in één Kopieer activiteit met gegevens consistentie verificatie is ingeschakeld, mislukt de Kopieer activiteit als het aantal rijen dat uit de bron is gelezen, afwijkt van het aantal rijen dat is gekopieerd naar de bestemming plus het aantal incompatibele rijen dat is overgeslagen.


## <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden om de verificatie van gegevens consistentie in de Kopieer activiteit in te scha kelen: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | Als u waar voor deze eigenschap instelt, worden bij het kopiëren van binaire bestanden de bestands grootte, de lastModifiedDate en de MD5-controlesom van de bron naar het doel archief gecontroleerd om de gegevens consistentie tussen het bron-en doel archief te controleren. Bij het kopiëren van tabellaire gegevens wordt het totale aantal rijen gecontroleerd nadat de taak is voltooid om ervoor te zorgen dat het totale aantal van de bron opgehaalde rij gelijk is aan het aantal rijen dat is gekopieerd naar de bestemming plus het aantal incompatibele rijen dat is overgeslagen. Houd er rekening mee dat de Kopieer prestaties worden beïnvloed door deze optie in te scha kelen.  | Waar<br/>False (standaard) | No
dataInconsistency | Een van de sleutel-waardeparen in de skipErrorFile-eigenschappen verzameling om te bepalen of u de inconsistente bestanden wilt overs Laan. <br/> -True: u wilt de rest kopiëren door inconsistente bestanden over te slaan.<br/> -False: u wilt de Kopieer activiteit afbreken nadat het inconsistente bestand is gevonden.<br/>Houd er rekening mee dat deze eigenschap alleen geldig is wanneer u binaire bestanden kopieert en stel validateDataConsistency in op True.  | Waar<br/>False (standaard) | No
logSettings | Een groep eigenschappen die kan worden opgegeven om het sessie logboek in te scha kelen voor het registreren van bestanden die zijn overgeslagen. | | No
linkedServiceName | De gekoppelde service van [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) voor het opslaan van de sessie logboek bestanden. | De namen van een `AzureBlobStorage` of meer `AzureBlobFS` gekoppelde service, die verwijst naar het exemplaar dat u gebruikt om de logboek bestanden op te slaan. | No
leertraject | Het pad van de logboek bestanden. | Geef het pad op waarin u de logboek bestanden wilt opslaan. Als u geen pad opgeeft, maakt de service een container voor u. | No

>[!NOTE]
>- Bij het kopiëren van binaire bestanden vanuit of naar Azure Blob of Azure Data Lake Storage Gen2, heeft ADF de MD5-controlesom verificatie op blok niveau met behulp van de [Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) en de [Azure data Lake Storage Gen2-API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Als ContentMD5 op bestanden bestaan in Azure Blob of als gegevens bronnen worden Azure Data Lake Storage Gen2, heeft ADF de MD5-controlesom controle op bestands niveau na het lezen van de bestanden. Na het kopiëren van bestanden naar Azure Blob of Azure Data Lake Storage Gen2 als gegevens doel, schrijft ADF ContentMD5 naar Azure Blob of Azure Data Lake Storage Gen2 die verder kan worden gebruikt door downstream-toepassingen voor de verificatie van gegevens consistentie.
>- ADF heeft bestands grootte verificatie bij het kopiëren van binaire bestanden tussen opslag archieven.

## <a name="monitoring"></a>Bewaking

### <a name="output-from-copy-activity"></a>Uitvoer van Kopieer activiteit
Nadat de Kopieer activiteit volledig is uitgevoerd, kunt u het resultaat van de verificatie van de gegevens consistentie controleren door de uitvoer van elke Kopieer activiteit uit te voeren:

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
U kunt de details van de verificatie van gegevens consistentie van de eigenschap dataConsistencyVerification bekijken.

Waarde van **VerificationResult**: 
-   **Gecontroleerd**: de gekopieerde gegevens zijn geverifieerd om consistent te zijn tussen het bron-en doel archief. 
-   **NotVerified**: de gekopieerde gegevens zijn niet geverifieerd om consistent te zijn omdat u de validateDataConsistency niet hebt ingeschakeld in de Kopieer activiteit. 
-   Niet **ondersteund**: de gekopieerde gegevens zijn niet gecontroleerd om consistent te zijn omdat de gegevens consistentie verificatie niet wordt ondersteund voor dit specifieke Kopieer paar. 

Waarde van **InconsistentData**: 
-   **Found**: de activiteit ADF Copy heeft inconsistente gegevens gevonden. 
-   **Overgeslagen**: de activiteit voor het kopiëren van ADF heeft inconsistente gegevens gevonden en overgeslagen. 
-   **Geen**: er zijn geen inconsistente gegevens gevonden met de ADF Copy-activiteit. Dit kan zijn omdat uw gegevens zijn geverifieerd om consistent te zijn tussen de bron-en doel opslag of omdat u validateDataConsistency hebt uitgeschakeld in de Kopieer activiteit. 

### <a name="session-log-from-copy-activity"></a>Sessie logboek van Kopieer activiteit

Als u configureert om het inconsistente bestand te registreren, kunt u het logboek bestand vinden in dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  De logboek bestanden worden de CSV-bestanden. 

Het schema van een logboek bestand is als volgt:

Kolom | Beschrijving 
-------- | -----------  
Tijdstempel | De tijds tempel wanneer ADF de inconsistente bestanden overs laat.
Niveau | Het logboek niveau van dit item. Het wordt weer gegeven in het waarschuwings niveau voor het item met het overs laan van bestanden.
OperationName | Operationeel gedrag van de ADF-Kopieer activiteit op elk bestand. Het wordt ' FileSkip ' om het bestand op te geven dat moet worden overgeslagen.
OperationItem | De naam van het bestand dat moet worden overgeslagen.
Bericht | Meer informatie om te illustreren waarom bestanden worden overgeslagen.

Het voor beeld van een logboek bestand is als volgt: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
In het bovenstaande logboek bestand ziet u dat sample1.csv is overgeslagen, omdat het niet is gelukt om te controleren of het bron-en doel archief consistent zijn. U kunt meer informatie krijgen over waarom sample1.csv inconsistent wordt omdat deze wordt gewijzigd door andere toepassingen wanneer de activiteit ADF kopiëren op hetzelfde moment wordt gekopieerd. 



## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Fout tolerantie van Kopieer activiteit](copy-activity-fault-tolerance.md)