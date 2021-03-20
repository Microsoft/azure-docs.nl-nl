---
title: Gegevens streamen als invoer in Azure Stream Analytics
description: Meer informatie over het instellen van een gegevens verbinding in Azure Stream Analytics. Invoer bevat een gegevens stroom van gebeurtenissen en verwijst ook naar gegevens.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 5f10fed66475cda8fd700a4737727101e2465870
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019360"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Gegevens streamen als invoer in Stream Analytics

Stream Analytics heeft eersteklas integratie met Azure-gegevens stromen als invoer van drie soorten resources:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Deze invoer resources kunnen in hetzelfde Azure-abonnement wonen als uw Stream Analytics-taak of een ander abonnement.

### <a name="compression"></a>Compressie

Stream Analytics ondersteunt compressie voor alle invoer bronnen van de gegevens stroom. Ondersteunde compressie typen zijn: geen-, GZip-en Deflate compressie. Ondersteuning voor compressie is niet beschikbaar voor referentie gegevens. Als de invoer indeling Avro gegevens is die zijn gecomprimeerd, wordt deze transparant verwerkt. U hoeft geen compressie type op te geven met Avro-serialisatie. 

## <a name="create-edit-or-test-inputs"></a>Invoer maken, bewerken of testen

U kunt de [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)en [Visual Studio code](quick-create-visual-studio-code.md) gebruiken om bestaande invoer in uw streaming-taak toe te voegen en weer te geven of te bewerken. U kunt ook invoer verbindingen testen en query's testen vanuit voorbeeld gegevens uit de Azure Portal, [Visual Studio](stream-analytics-vs-tools-local-run.md)en [Visual Studio code](visual-studio-code-local-run.md). Wanneer u een query schrijft, vermeldt u de invoer in de component FROM. U kunt de lijst met beschik bare invoer gegevens ophalen van de **query** pagina in de portal. Als u meerdere invoer wilt gebruiken, kunt u er `JOIN` meerdere query's mee of schrijven `SELECT` .


## <a name="stream-data-from-event-hubs"></a>Gegevens streamen vanuit Event Hubs

Azure Event Hubs biedt een zeer schaal bare gebeurtenis investeerders voor het publiceren van een abonnement. Een Event Hub kan miljoenen gebeurtenissen per seconde verzamelen, zodat u de enorme hoeveel heden gegevens die worden geproduceerd door uw verbonden apparaten en toepassingen kunt verwerken en analyseren. Event Hubs en Stream Analytics bieden samen een end-to-end oplossing voor realtime analyses. Met Event Hubs kunt u in realtime gebeurtenissen in azure feeden en Stream Analytics-taken kunnen deze gebeurtenissen in realtime verwerken. U kunt bijvoorbeeld web klikken, lees acties op de sensor of online logboek gebeurtenissen verzenden naar Event Hubs. U kunt vervolgens Stream Analytics-taken maken om Event Hubs te gebruiken als de invoer gegevens stromen voor realtime filteren, aggregatie en correlatie.

`EventEnqueuedUtcTime` is het tijds tempel van de aankomst van een gebeurtenis in een Event Hub en is het standaard tijds tempel van gebeurtenissen die afkomstig zijn van Event Hubs naar Stream Analytics. Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="event-hubs-consumer-groups"></a>Consumenten groepen Event Hubs

U moet elke Stream Analytics Event Hub invoer configureren voor een eigen consumenten groep. Wanneer een taak een self-koppeling bevat of meerdere invoer heeft, kunnen sommige invoer waarden worden gelezen door meer dan één lezer downstream. Deze situatie is van invloed op het aantal lezers in één consumenten groep. Om te voor komen dat de Event Hubs limiet van vijf lezers per Consumer groep per partitie wordt overschreden, is het een best practice om een consumenten groep voor elke Stream Analytics taak aan te wijzen. Er is ook een limiet van 20 consumenten groepen voor een standaardlaag Event Hub. Zie [problemen met Azure stream Analytics invoer oplossen](stream-analytics-troubleshoot-input.md)voor meer informatie.

### <a name="create-an-input-from-event-hubs"></a>Een invoer maken op basis van Event Hubs

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in het Azure portal voor het streamen van gegevens invoer vanuit een event hub uitgelegd:

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** |Een beschrijvende naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
| **Abonnement** | Kies het abonnement waarin de Event hub-bron zich bevindt. | 
| **Event hub-naam ruimte** | De Event hub-naam ruimte is een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub maakt, maakt u ook de naam ruimte. |
| **Naam van de Event Hub** | De naam van de Event Hub die moet worden gebruikt als invoer. |
| **Naam van het Event Hub-beleid** | Het beleid voor gedeelde toegang dat toegang biedt tot de Event hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Deze optie wordt automatisch ingevuld, tenzij u de optie selecteert om de Event hub-instellingen hand matig op te geven.|
| **Event hub-consumenten groep** (aanbevolen) | Het is raadzaam om voor elke Stream Analytics taak een afzonderlijke consumenten groep te gebruiken. Met deze teken reeks wordt de Consumer groep geïdentificeerd die moet worden gebruikt om gegevens op te nemen van de Event Hub. Als er geen consumenten groep is opgegeven, gebruikt de Stream Analytics-taak de $Default Consumer groep.  |
| **Partitiesleutel** | Dit is een optioneel veld dat alleen beschikbaar is als uw taak is geconfigureerd voor het gebruik van [compatibiliteits niveau](./stream-analytics-compatibility-level.md) 1,2 of hoger. Als uw invoer is gepartitioneerd door een eigenschap, kunt u hier de naam van deze eigenschap toevoegen. Dit wordt gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. Als deze taak gebruikmaakt van het compatibiliteits niveau 1,2 of hoger, wordt in dit veld standaard de waarde ' PartitionId ' gebruikt. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling wordt uitgelijnd met de specificatie en geen voorloop 0 voor decimale getallen bevat. |
| **Codering** | UTF-8 is momenteel de enige coderings indeling die wordt ondersteund. |
| **Type gebeurtenis compressie** | Het compressie type dat wordt gebruikt voor het lezen van de binnenkomende gegevens stroom, zoals geen (standaard), GZip of Deflate. |

Als uw gegevens afkomstig zijn van een event hub-stroom invoer, hebt u toegang tot de volgende meta gegevens velden in uw Stream Analytics query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **EventEnqueuedUtcTime** |De datum en tijd waarop de gebeurtenis is ontvangen door Event Hubs. |
| **PartitionId** |De op nul gebaseerde partitie-ID voor de invoer adapter. |

Als u bijvoorbeeld deze velden gebruikt, kunt u een query schrijven zoals in het volgende voor beeld:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Wanneer u Event hub gebruikt als een eind punt voor IoT Hub routes, hebt u toegang tot de IoT Hub meta gegevens met behulp van de [functie GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Gegevens streamen van IoT Hub

Azure IoT Hub is een uiterst schaal bare publicatie event ingestor geoptimaliseerd voor IoT-scenario's.

De standaardtime Stamp van gebeurtenissen die afkomstig zijn van een IoT Hub in Stream Analytics is het tijds tempel dat de gebeurtenis heeft ontvangen in de IoT Hub `EventEnqueuedUtcTime` . Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruiken.

### <a name="iot-hub-consumer-groups"></a>IOT hub-consumenten groepen

U moet elke Stream Analytics IoT Hub invoer configureren voor een eigen consumenten groep. Wanneer een taak een self-samen voeging bevat of meerdere invoer heeft, kan sommige invoer worden gelezen door meer dan één lezer downstream. Deze situatie is van invloed op het aantal lezers in één consumenten groep. Om te voor komen dat de limiet van de Azure-IoT Hub van vijf lezers per Consumer groep per partitie wordt overschreden, is het een best practice om een consumenten groep voor elke Stream Analytics taak aan te wijzen.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Een IoT Hub configureren als invoer van een gegevens stroom

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in het Azure Portal uitgelegd wanneer u een IOT hub als een stroom invoer configureert.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** | Een beschrijvende naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen.|
| **Abonnement** | Kies het abonnement waarin de IoT Hub resource bestaat. | 
| **IoT Hub** | De naam van de IoT Hub die moet worden gebruikt als invoer. |
| **Eindpunt** | Het eind punt voor de IoT Hub.|
| **Naam van gedeeld toegangsbeleid** | Het beleid voor gedeelde toegang dat toegang biedt tot de IoT Hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| **Sleutel voor gedeeld toegangs beleid** | De gedeelde toegangs sleutel die wordt gebruikt om toegang te verlenen tot de IoT Hub.  Deze optie wordt automatisch ingevuld tenzij u de optie selecteert om de IOT hub-instellingen hand matig op te geven. |
| **Consumenten groep** | Het wordt ten zeerste aangeraden om voor elke Stream Analytics taak een andere consumenten groep te gebruiken. De Consumer groep wordt gebruikt om gegevens op te nemen van de IoT Hub. Stream Analytics gebruikt de $Default-Consumer groep tenzij u anders opgeeft.  |
| **Partitiesleutel** | Dit is een optioneel veld dat alleen beschikbaar is als uw taak is geconfigureerd voor het gebruik van [compatibiliteits niveau](./stream-analytics-compatibility-level.md) 1,2 of hoger. Als uw invoer is gepartitioneerd door een eigenschap, kunt u hier de naam van deze eigenschap toevoegen. Dit wordt gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. Als deze taak gebruikmaakt van het compatibiliteits niveau 1,2 of hoger, wordt in dit veld standaard de waarde ' PartitionId ' gebruikt. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling wordt uitgelijnd met de specificatie en geen voorloop 0 voor decimale getallen bevat. |
| **Codering** | UTF-8 is momenteel de enige coderings indeling die wordt ondersteund. |
| **Type gebeurtenis compressie** | Het compressie type dat wordt gebruikt voor het lezen van de binnenkomende gegevens stroom, zoals geen (standaard), GZip of Deflate. |


Wanneer u gegevens van streams van een IoT Hub gebruikt, hebt u toegang tot de volgende meta gegevens velden in uw Stream Analytics query:

| Eigenschap | Beschrijving |
| --- | --- |
| **EventProcessedUtcTime** | De datum en tijd waarop de gebeurtenis is verwerkt. |
| **EventEnqueuedUtcTime** | De datum en tijd waarop de gebeurtenis is ontvangen door de IoT Hub. |
| **PartitionId** | De op nul gebaseerde partitie-ID voor de invoer adapter. |
| **IoTHub. MessageId** | Een ID die wordt gebruikt voor het correleren van twee richtings communicatie in IoT Hub. |
| **IoTHub. CorrelationId** | Een ID die wordt gebruikt voor antwoorden op berichten en feedback in IoT Hub. |
| **IoTHub. ConnectionDeviceId** | De verificatie-ID die wordt gebruikt om dit bericht te verzenden. Deze waarde wordt gestempeld op servicebound-berichten door de IoT Hub. |
| **IoTHub. ConnectionDeviceGenerationId** | De generatie-ID van het geverifieerde apparaat dat is gebruikt om dit bericht te verzenden. Deze waarde wordt gestempeld op servicebound-berichten door de IoT Hub. |
| **IoTHub. EnqueuedTime** | Het tijdstip waarop het bericht is ontvangen door de IoT Hub. |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>Gegevens uit Blob Storage of Data Lake Storage Gen2 streamen
Voor scenario's met grote hoeveel heden ongestructureerde gegevens die in de cloud worden opgeslagen, biedt Azure Blob Storage of Azure Data Lake Storage Gen2 (ADLS Gen2) een voordelige en schaal bare oplossing. Gegevens in Blob Storage of ADLS Gen2 worden gewoonlijk als gegevens in rust beschouwd; deze gegevens kunnen echter worden verwerkt als een gegevens stroom door Stream Analytics. 

De verwerking van Logboeken is een algemeen gebruikt scenario voor het gebruik van dergelijke invoer met Stream Analytics. In dit scenario zijn telemetriegegevens van een systeem vastgelegd en moeten ze worden geparseerd en verwerkt om zinvolle gegevens op te halen.

De standaardtime Stamp van een Blob Storage-of ADLS Gen2 gebeurtenis in Stream Analytics is de tijds tempel die het laatst is gewijzigd `BlobLastModifiedUtcTime` . Als een BLOB wordt geüpload naar een opslag account op 13:00 en de Azure Stream Analytics-taak is gestart met de optie *nu* op 13:01, wordt deze niet opgehaald als gewijzigde tijd buiten de uitvoerings periode van de taak valt.

Als een BLOB wordt geüpload naar een container voor opslag accounts op 13:00 en de Azure Stream Analytics-taak is gestart met *aangepaste tijd* op 13:00 of eerder, wordt de BLOB opgehaald als gewijzigd tijd binnen de taak uitvoerings periode valt.

Als een Azure Stream Analytics taak *nu* wordt gestart op 13:00 en er een BLOB wordt geüpload naar de container van het opslag account op 13:01, wordt de blob door Azure stream Analytics opgehaald. De tijds tempel die is toegewezen aan elke blob is alleen gebaseerd op `BlobLastModifiedTime` . De map waartoe de BLOB zich bevindt, heeft geen relatie met de toegewezen tijds tempel. Als er bijvoorbeeld een BLOB *2019/10-01/00/b1.txt* is met een `BlobLastModifiedTime` van 2019-11-11, is de tijds tempel die is toegewezen aan deze BLOB 2019-11-11.

Als u de gegevens wilt verwerken als een stroom met behulp van een tijds tempel in de nettolading van de gebeurtenis, moet u het sleutel woord [time stamp by](/stream-analytics-query/stream-analytics-query-language-reference) gebruiken. Een Stream Analytics taak haalt gegevens op uit de Azure Blob-opslag of ADLS Gen2 invoer elke seconde als het blobbestand beschikbaar is. Als het blobbestand niet beschikbaar is, is er een exponentiële uitstel met een maximale tijds vertraging van 90 seconden.

Voor invoer in CSV-indeling is een veldnamenrij vereist om velden voor de gegevensset te definiëren. alle velden in de veldnamenrij moeten uniek zijn.

> [!NOTE]
> Stream Analytics biedt geen ondersteuning voor het toevoegen van inhoud aan een bestaand blob-bestand. Stream Analytics elk bestand slechts één keer weer geven en alle wijzigingen die zich in het bestand voordoen nadat de taak de gegevens heeft gelezen, worden niet verwerkt. De aanbevolen procedure is om alle gegevens voor een blob-bestand tegelijk te uploaden en vervolgens extra nieuwere gebeurtenissen toe te voegen aan een ander, nieuw blob-bestand.

In scenario's waarin veel blobs continu worden toegevoegd en Stream Analytics het verwerken van de blobs wanneer ze worden toegevoegd, is het mogelijk dat sommige blobs in zeldzame gevallen worden overgeslagen vanwege de granulatie van de `BlobLastModifiedTime` . U kunt dit verhelpen door blobs ten minste twee seconden van elkaar te uploaden. Als deze optie niet haalbaar is, kunt u Event Hubs gebruiken om grote hoeveel heden gebeurtenissen te streamen.

### <a name="configure-blob-storage-as-a-stream-input"></a>Blob-opslag configureren als een stroom invoer 

In de volgende tabel wordt elke eigenschap in de **nieuwe invoer** pagina in de Azure Portal beschreven wanneer u Blob-opslag als een stroom invoer configureert.

| Eigenschap | Beschrijving |
| --- | --- |
| **Invoer alias** | Een beschrijvende naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
| **Abonnement** | Kies het abonnement waarin de opslag resource zich bevindt. | 
| **Opslagaccount** | De naam van het opslag account waarin de BLOB-bestanden zich bevinden. |
| **Sleutel van het opslag account** | De geheime sleutel die is gekoppeld aan het opslag account. Deze optie wordt automatisch ingevuld tenzij u de optie selecteert om de instellingen hand matig op te geven. |
| **Container** | Containers bieden een logische groepering voor blobs. U kunt bestaande container **gebruiken** kiezen of  **nieuwe maken** om een nieuwe container gemaakt te krijgen.|
| **Patroon van pad** (optioneel) | Het bestandspad dat wordt gebruikt om de blobs in de opgegeven container te vinden. Als u blobs wilt lezen uit de hoofdmap van de container, moet u geen patroon voor paden instellen. Binnen het pad kunt u een of meer exemplaren van de volgende drie variabelen opgeven: `{date}` , `{time}` , of `{partition}`<br/><br/>Voor beeld 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Voor beeld 2: `cluster1/logs/{date}`<br/><br/>Het `*` teken is geen toegestane waarde voor het voor voegsel van het pad. Alleen geldige <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Azure Blob-tekens</a> zijn toegestaan. Neem geen container namen of bestands namen op. |
| **Datum notatie** (optioneel) | Als u de datum variabele in het pad gebruikt, de datum notatie waarin de bestanden zijn geordend. Voorbeeld: `YYYY/MM/DD` <br/><br/> Wanneer BLOB-invoer `{date}` een of meer `{time}` paden heeft, worden de mappen in oplopende volg orde bekeken.|
| **Tijd notatie** (optioneel) |  Als u de time-variabele in het pad gebruikt, de tijd notatie waarin de bestanden zijn ingedeeld. Momenteel is de enige ondersteunde waarde `HH` voor uren. |
| **Partitiesleutel** | Dit is een optioneel veld dat alleen beschikbaar is als uw taak is geconfigureerd voor het gebruik van [compatibiliteits niveau](./stream-analytics-compatibility-level.md) 1,2 of hoger. Als uw invoer is gepartitioneerd door een eigenschap, kunt u hier de naam van deze eigenschap toevoegen. Dit wordt gebruikt voor het verbeteren van de prestaties van uw query als deze de component PARTITION BY of GROUP BY bevat voor deze eigenschap. Als deze taak gebruikmaakt van het compatibiliteits niveau 1,2 of hoger, wordt in dit veld standaard de waarde ' PartitionId ' gebruikt. |
| **Aantal invoer partities** | Dit veld is alleen aanwezig als {Partition} aanwezig is in het pad patroon. De waarde van deze eigenschap is een geheel getal >= 1. Overal waar {Partition} wordt weer gegeven in pathPattern, wordt een getal tussen 0 en de waarde van dit veld-1 gebruikt. |
| **Serialisatie-indeling voor gebeurtenissen** | De serialisatie-indeling (JSON, CSV, AVRO of [Other (protobuf, XML, bedrijfs gegevens...)](custom-deserializer.md)) van de binnenkomende gegevens stroom.  Zorg ervoor dat de JSON-indeling wordt uitgelijnd met de specificatie en geen voorloop 0 voor decimale getallen bevat. |
| **Codering** | Voor CSV en JSON is UTF-8 momenteel de enige coderings indeling die wordt ondersteund. |
| **Compressie** | Het compressie type dat wordt gebruikt voor het lezen van de binnenkomende gegevens stroom, zoals geen (standaard), GZip of Deflate. |

Als uw gegevens afkomstig zijn uit een Blob-opslag bron, hebt u toegang tot de volgende meta gegevens velden in uw Stream Analytics query:

| Eigenschap | Beschrijving |
| --- | --- |
| **BlobName** |De naam van de invoer-BLOB waarvan de gebeurtenis afkomstig is. |
| **EventProcessedUtcTime** |De datum en tijd waarop de gebeurtenis is verwerkt door Stream Analytics. |
| **BlobLastModifiedUtcTime** |De datum en tijd waarop de BLOB voor het laatst is gewijzigd. |
| **PartitionId** |De op nul gebaseerde partitie-ID voor de invoer adapter. |

Als u bijvoorbeeld deze velden gebruikt, kunt u een query schrijven zoals in het volgende voor beeld:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/