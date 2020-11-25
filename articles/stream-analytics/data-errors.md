---
title: Azure Stream Analytics bron logboek gegevens fouten
description: In dit artikel worden de verschillende invoer-en uitvoer gegevens fouten beschreven die kunnen optreden bij het gebruik van Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 416e6cb29ab2816d53cb837f72233a9fe098f659
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011418"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics gegevens fouten

Gegevens fouten zijn fouten die optreden tijdens het verwerken van de gegevens.  Deze fouten worden meestal veroorzaakt tijdens het deserialiseren van gegevens, de serialisatie en schrijf bewerkingen.  Wanneer er gegevens fouten optreden, schrijft Stream Analytics gedetailleerde informatie en voor beelden van gebeurtenissen naar de bron Logboeken. Schakel Diagnostische logboeken in uw taak in om deze aanvullende gegevens op te halen. In sommige gevallen wordt een samen vatting van deze informatie ook verstrekt via Portal meldingen.

In dit artikel vindt u een overzicht van de verschillende fout typen, oorzaken en resource logboek Details voor invoer-en uitvoer gegevens fouten.

## <a name="resource-logs-schema"></a>Schema voor bron logboeken

Zie [problemen met Azure stream Analytics oplossen met behulp van Diagnostische logboeken](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) om het schema voor resource logboeken te bekijken. De volgende JSON is een voorbeeld waarde voor het veld **Eigenschappen** van een bron logboek voor een gegevens fout.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Invoer gegevens fouten

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Oorzaak: het geselecteerde invoer compressie type komt niet overeen met de gegevens.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: berichten met eventuele fouten in de deserialisatie, inclusief het ongeldige compressie type, worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. Voor Event hub is de id het PartitionId-, offset-en sequence-nummer.

**Foutbericht**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Oorzaak: de header van de invoer gegevens is ongeldig. Een CSV bevat bijvoorbeeld kolommen met dubbele namen.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: berichten met een eventuele deserialisatie fout, inclusief een ongeldige header, worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. 
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Oorzaak: de invoer kolommen die zijn gedefinieerd met CREATE TABLE of via Time Stamp, bestaan niet.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: gebeurtenissen met ontbrekende kolommen worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. 
   * De namen van de ontbrekende kolommen. 
   * De werkelijke Payload tot een aantal kilo bytes.

**Foutberichten**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Oorzaak: kan de invoer niet converteren naar het type dat is opgegeven in de CREATE TABLE-instructie.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: gebeurtenissen met de type conversie fout worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. 
   * De naam van de kolom en het verwachte type.

**Foutberichten**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Oorzaak: de invoer gegevens hebben niet de juiste indeling. De invoer is bijvoorbeeld geen geldige JSON.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle gebeurtenissen in het bericht nadat een ongeldige gegevens fout is opgetreden, worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. 
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutberichten**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Oorzaak: de waarde van de time stamp BY-expressie kan niet worden geconverteerd naar DateTime.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: gebeurtenissen met een ongeldige invoer-tijds tempel worden verwijderd uit de invoer.
* Logboek Details
   * Invoer bericht-id. 
   * Fout bericht. 
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Oorzaak: de waarde van Time Stamp met meer dan timestampColumn is NULL.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: gebeurtenissen met een ongeldige invoer tijds tempel sleutel worden verwijderd uit de invoer.
* Logboek Details
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Oorzaak: het verschil tussen de toepassings tijd en aankomst tijd is groter dan het tolerantie venster late aankomst.
* Portal-melding meegeleverd: Nee
* Niveau van bron logboek: informatie
* Impact: latere invoer gebeurtenissen worden verwerkt op basis van de instelling andere gebeurtenissen afhandelen in de sectie gebeurtenis volgorde van de taak configuratie. Zie [time handling policies](/stream-analytics-query/time-skew-policies-azure-stream-analytics)(Engelstalig) voor meer informatie.
* Logboek Details
   * Tijd van de toepassing en aankomst tijd. 
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Oorzaak: het verschil tussen de tijd van de toepassing en de aankomst tijd is langer dan vijf minuten.
* Portal-melding meegeleverd: Nee
* Niveau van bron logboek: informatie
* Impact: vroege invoer gebeurtenissen worden verwerkt op basis van de instelling andere gebeurtenissen afhandelen in de sectie gebeurtenis volgorde van de taak configuratie. Zie [time handling policies](/stream-analytics-query/time-skew-policies-azure-stream-analytics)(Engelstalig) voor meer informatie.
* Logboek Details
   * Tijd van de toepassing en aankomst tijd. 
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Oorzaak: de gebeurtenis wordt in de juiste volg orde beschouwd volgens het venster voor de buiten-volg orde van tolerantie gedefinieerd.
* Portal-melding meegeleverd: Nee
* Niveau van bron logboek: informatie
* Impact: aflopende gebeurtenissen worden verwerkt op basis van de instelling andere gebeurtenissen afhandelen in de sectie gebeurtenis volgorde van de taak configuratie. Zie [time handling policies](/stream-analytics-query/time-skew-policies-azure-stream-analytics)(Engelstalig) voor meer informatie.
* Logboek Details
   * De werkelijke Payload tot slechts enkele kilo bytes.

**Foutbericht**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Resultaten van uitvoer gegevens

Azure Stream Analytics kunnen uitvoer gegevens fouten identificeren met of zonder een I/O-aanvraag aan de uitvoer sink, afhankelijk van de configuratie. Bijvoorbeeld: ontbrekende een vereiste kolom, zoals  `PartitionKey` , wanneer u Azure Table output gebruikt, kan worden geïdentificeerd zonder een I/O-aanvraag. Beperkings schendingen in SQL-uitvoer vereisen echter een I/O-aanvraag.

Er zijn verschillende gegevens fouten die alleen kunnen worden gedetecteerd na het aanroepen van de uitvoer sink, waardoor de verwerking kan vertragen. U kunt dit oplossen door de configuratie van uw taak of de query die de gegevens fout veroorzaakt, te wijzigen.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Oorzaak: de vereiste kolom voor de uitvoer bestaat niet. Een kolom die is gedefinieerd als Azure Table PartitionKey does't bestaat bijvoorbeeld.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle conversie fouten van uitvoer gegevens, inclusief ontbrekende vereiste kolom worden verwerkt volgens de instelling voor het [uitvoer gegevens beleid](./stream-analytics-output-error-policy.md) .
* Logboek Details
   * De naam van de kolom en de record-id of een deel van de record.

**Foutbericht**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Oorzaak: de kolom waarde komt niet overeen met de uitvoer. De kolom naam is bijvoorbeeld geen geldige kolom van Azure Table.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle conversie fouten van uitvoer gegevens, inclusief de ongeldige kolom naam, worden verwerkt volgens de instelling voor het [uitvoer gegevens beleid](./stream-analytics-output-error-policy.md) .
* Logboek Details
   * De naam van de kolom en ofwel de record-id of het deel van de record.

**Foutbericht**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Oorzaak: een kolom kan niet worden geconverteerd naar een geldig type in de uitvoer. De waarde van column is bijvoorbeeld incompatibel met beperkingen of het type dat in de SQL-tabel is gedefinieerd.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle conversie fouten van uitvoer gegevens, waaronder type conversie fout, worden verwerkt op basis van de [beleids instelling uitvoer gegevens](./stream-analytics-output-error-policy.md) .
* Logboek Details
   * De naam van de kolom.
   * De record-id of een deel van de record.

**Foutbericht**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Oorzaak: de waarde van het bericht is groter dan de ondersteunde uitvoer grootte. Een record is bijvoorbeeld groter dan 1 MB voor een event hub-uitvoer.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle conversie fouten van uitvoer gegevens, waaronder record overschrijding van de grootte, worden verwerkt volgens de [beleids instelling uitvoer gegevens](./stream-analytics-output-error-policy.md) .
* Logboek Details
   * De record-id of een deel van de record.

**Foutbericht**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Oorzaak: een record bevat al een kolom met dezelfde naam als een systeem kolom. Bijvoorbeeld CosmosDB uitvoer met een kolom met de naam ID wanneer de kolom ID een andere kolom is.
* Portal melding meegeleverd: Ja
* Niveau van bron logboek: waarschuwing
* Impact: alle conversie fouten van uitvoer gegevens, inclusief dubbele sleutels, worden verwerkt volgens de [beleids instelling uitvoer gegevens](./stream-analytics-output-error-policy.md) .
* Logboek Details
   * De naam van de kolom.
   * De record-id of een deel van de record.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Stream Analytics oplossen met Diagnostische logboeken](stream-analytics-job-diagnostic-logs.md)

* [Meer informatie over Stream Analytics taak bewaking en het bewaken van query's](stream-analytics-monitoring.md)