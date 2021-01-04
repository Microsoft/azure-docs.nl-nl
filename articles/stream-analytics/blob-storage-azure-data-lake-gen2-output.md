---
title: Blob-opslag en Azure Data Lake Gen2 uitvoer van Azure Stream Analytics
description: In dit artikel vindt u informatie over Blob Storage en Azure Data Lake gen 2 als uitvoer voor Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ba64f5972d77637ec830fe6b872af66363d6c1bd
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740042"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Blob-opslag en Azure Data Lake Gen2 uitvoer van Azure Stream Analytics

Data Lake Storage Gen2 maakt van Azure Storage de basis voor het bouwen van zakelijke data lakes op Azure. Data Lake Storage Gen2 is ontworpen met het starten van meerdere PETA bytes aan gegevens terwijl er honderden gigabits van de door voer worden gehouden, en waarmee u eenvoudig enorme hoeveel heden gegevens kunt beheren. Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een hiërarchische naam ruimte aan Blob Storage.

Azure Blob-opslag biedt een voordelige en schaal bare oplossing voor het opslaan van grote hoeveel heden ongestructureerde gegevens in de Cloud. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor een inleiding in Blob Storage en het gebruik ervan.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een BLOB of ADLS Gen2 uitvoer.

| Naam van eigenschap       | Beschrijving                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Uitvoeralias        | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Blob-opslag te sturen. |
| Storage-account     | De naam van het opslag account waarin u uw uitvoer wilt verzenden.               |
| Sleutel van het opslag account | De geheime sleutel die is gekoppeld aan het opslag account.                              |
| Opslag container   | Een logische groepering voor blobs die zijn opgeslagen in de Azure-Blob service. Wanneer u een BLOB uploadt naar de Blob service, moet u een container voor die BLOB opgeven. |
| Padpatroon | Optioneel. Het patroon van het bestandspad dat wordt gebruikt voor het schrijven van uw blobs binnen de opgegeven container. <br /><br /> In het pad patroon kunt u kiezen voor het gebruik van een of meer exemplaren van de variabelen datum en tijd om de frequentie op te geven waarmee blobs worden geschreven: <br /> {date}, {time} <br /><br />U kunt aangepaste BLOB-partitionering gebruiken om één aangepaste {Field}-naam van uw gebeurtenis gegevens op te geven voor het partitioneren van blobs. De naam van het veld is alfanumeriek en kan spaties, afbreek streepjes en onderstrepings tekens bevatten. De volgende beperkingen zijn van toepassing op aangepaste velden: <ul><li>Veld namen zijn niet hoofdletter gevoelig. De service kan bijvoorbeeld geen onderscheid maken tussen de kolom-ID en de kolom-id.</li><li>Geneste velden zijn niet toegestaan. Gebruik in plaats daarvan een alias in de taak query om het veld af te vlakken.</li><li>Expressies kunnen niet worden gebruikt als veld naam.</li></ul> <br />Met deze functie kunt u het gebruik van aangepaste configuraties voor datum/tijd-indeling in het pad. Aangepaste datum-en tijd notaties moeten een voor een worden opgegeven, omsloten door het sleutel woord {DateTime: \<specifier> }. Toegestane invoer voor \<specifier> zijn jjjj, mm, M, dd, d, hh, H, mm, M, SS of s. Het sleutel woord {DateTime: \<specifier> } kan meermaals worden gebruikt in het pad naar aangepaste datum/tijd-configuraties. <br /><br />Voorbeelden: <ul><li>Voor beeld 1: cluster1/logboeken/{date}/{time}</li><li>Voor beeld 2: cluster1/logboeken/{date}</li><li>Voor beeld 3: cluster1/{client_id}/{date}/{time}</li><li>Voor beeld 4: cluster1/{DateTime: SS}/{myField} waarbij de query is: SELECT data. myField AS myField FROM input;</li><li>Voor beeld 5: cluster1/Year = {DateTime: jjjj}/month = {DateTime: MM}/Day = {DateTime: dd}</ul><br />De tijds tempel van de gemaakte mappen structuur volgt UTC en niet lokale tijd. [System. time stamp](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-time-handling#choose-the-best-starting-time) is de tijd die wordt gebruikt voor alle op tijd gebaseerde partitionering.<br /><br />Bestands namen gebruiken de volgende Conventie: <br /><br />{Path-voorvoegsel patroon}/schemaHashcode_Guid_Number. extensie<br /><br /> Deze GUID vertegenwoordigt de unieke id die is toegewezen aan een interne schrijver die is gemaakt om te schrijven naar een blob-bestand. Het nummer vertegenwoordigt de index van het BLOB-blok. <br /><br /> Voorbeeld uitvoer bestanden:<ul><li>MYOUTPUT/20170901/00/45434_gguid_1.csv</li>  <li>MYOUTPUT/20170901/01/45434_gguid_1.csv</li></ul> <br />Zie [Azure stream Analytics aangepaste BLOB-uitvoer partitionering](stream-analytics-custom-path-patterns-blob-storage-output.md)voor meer informatie over deze functie. |
| Datum notatie | Optioneel. Als het datum token wordt gebruikt in het voorvoegsel pad, kunt u de datum notatie selecteren waarin uw bestanden zijn ingedeeld. Voor beeld: JJJJ/MM/DD |
| Tijd notatie | Optioneel. Als de time-token wordt gebruikt in het pad van het voor voegsel, geeft u de tijd notatie op waarin uw bestanden zijn geordend. Op dit moment is de enige ondersteunde waarde HH. |
| Serialisatie-indeling voor gebeurtenissen | Serialisatie-indeling voor uitvoer gegevens. JSON, CSV, AVRO en Parquet worden ondersteund. |
|Minimum aantal rijen |Het aantal minimum rijen per batch. Voor Parquet maakt elke batch een nieuw bestand. De huidige standaard waarde is 2.000 rijen en het toegestane maximum is 10.000 rijen.|
|Maximale tijd |De maximale wacht tijd per batch. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. De huidige standaard waarde is 1 minuut en het toegestane maximum is 2 uur. Als uw BLOB-uitvoer een patroon frequentie voor het pad heeft, kan de wacht tijd niet hoger zijn dan het tijds bereik van de partitie.|
| Encoding    | Als u de CSV-of JSON-indeling gebruikt, moet u een code ring opgeven. UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund. |
| Scheidingsteken   | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal algemene scheidings tekens voor het serialiseren van CSV-gegevens. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling      | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden** selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. Deze matrix wordt alleen gesloten wanneer de taak wordt gestopt of Stream Analytics is verplaatst naar het volgende tijd venster. Over het algemeen is het raadzaam om met regel-gescheiden JSON te gebruiken, omdat hiervoor geen speciale verwerking is vereist terwijl het uitvoer bestand nog wordt geschreven. |

## <a name="blob-output-files"></a>BLOB-uitvoer bestanden

Wanneer u Blob Storage als uitvoer gebruikt, wordt er in de volgende gevallen een nieuw bestand in de BLOB gemaakt:

* Als het bestand het maximum aantal toegestane blokken (momenteel 50.000) overschrijdt. U kunt het Maxi maal toegestane aantal blokken bereiken zonder de Maxi maal toegestane Blob-grootte te bereiken. Als de uitvoer frequentie bijvoorbeeld hoog is, kunt u meer bytes per blok weer geven en is de bestands grootte groter. Als de uitvoer frequentie laag is, heeft elk blok minder gegevens en is de bestands grootte kleiner.
* Als er een schema wijziging in de uitvoer is, en de uitvoer indeling vereist een vast schema (CSV en AVRO).
* Als een taak opnieuw wordt gestart, hetzij extern door een gebruiker die deze stopt en start, of intern voor systeem onderhoud of fout herstel.
* Als de query volledig is gepartitioneerd en er een nieuw bestand wordt gemaakt voor elke uitvoer partitie.
* Als de gebruiker een bestand of container van het opslag account verwijdert.
* Als de uitvoer is gepartitioneerd met behulp van het pad voorvoegsel patroon en er wordt een nieuwe BLOB gebruikt wanneer de query naar het volgende uur wordt verplaatst.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld en er een nieuwe BLOB wordt gemaakt per partitie sleutel als deze niet bestaat.
* Als de uitvoer wordt gepartitioneerd door een aangepast veld waarbij de kardinaliteit van de partitie sleutel groter is dan 8.000 en er een nieuwe BLOB wordt gemaakt per partitie sleutel.

## <a name="partitioning"></a>Partitionering

Gebruik voor partitie sleutel {date} en {time} tokens uit uw gebeurtenis velden in het pad patroon. Kies de datum notatie, zoals JJJJ/MM/DD, DD/MM/JJJJ of MM-DD-JJJJ. HH wordt gebruikt voor de tijd notatie. BLOB-uitvoer kan worden gepartitioneerd met één aangepast gebeurtenis kenmerk {FieldName} of {datetime: \<specifier> }. Het aantal schrijvers van de uitvoer volgt de invoer partities voor [volledig kan worden opgestart-query's](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Zie [Azure Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)voor de maximale bericht grootte. De maximale grootte van het BLOB-blok is 4 MB en het maximum aantal BLOB-Bock is 50.000. |

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteit (preview) gebruiken om uw Azure Stream Analytics-taak te verifiëren bij Azure Blob Storage](blob-output-managed-identity.md)
* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
