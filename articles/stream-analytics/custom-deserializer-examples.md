---
title: Invoer in elke indeling lezen met behulp van aangepaste .net-deserialisatie in azure stream Analytics
description: In dit artikel worden de serialisatie-indeling en de interfaces beschreven die aangepaste .NET-deserialers definiëren voor Azure Stream Analytics Cloud-en Edge-taken.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018153"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Invoer in elke indeling lezen met aangepaste .net-deserialisatie

Aangepaste deserializers van .NET bieden uw Azure Stream Analytics-taak de mogelijkheid om gegevens te lezen uit indelingen buiten de drie [ingebouwde gegevens indelingen](stream-analytics-parsing-json.md). In dit artikel worden de serialisatie-indeling en de interfaces beschreven die aangepaste .net-deserialisatie definiëren voor Azure stream Analytics Cloud-en Edge-taken. Er zijn ook voor beelden van deserializers voor protocol buffer en CSV-indeling.

## <a name="net-custom-deserializer"></a>Aangepaste .NET-deserialisatie

De volgende code voorbeelden zijn de interfaces die de aangepaste deserializer definiëren en implementeren `StreamDeserializer<T>` .

`UserDefinedOperator` is de basis klasse voor alle aangepaste streaming-Opera tors. Het wordt geïnitialiseerd `StreamingContext` , wat een context biedt met het mechanisme voor het publiceren van diagnostische gegevens waarvoor u fouten wilt opsporen in problemen met uw deserializer.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Het volgende code fragment is de deserialisatie voor het streamen van gegevens. 

Skippable-fouten moeten worden verzonden met `IStreamingDiagnostics` door gegeven via `UserDefinedOperator` de initialisatie methode. Alle uitzonde ringen worden beschouwd als fouten en de deserializer wordt opnieuw gemaakt. Na een bepaald aantal fouten wordt de taak naar de status mislukt.

`StreamDeserializer<T>` deserialeert een stroom naar een object van het type `T` . Aan de volgende voor waarden moet worden voldaan:

1. T is een klasse of een struct.
1. Alle open bare velden in T zijn
    1. Een van [sbyte, byte, short, USHORT, int, uint, Long, DateTime, String, float, Double] of hun null-equivalenten.
    1. Een andere struct of klasse volgens dezelfde regels.
    1. Matrix van `T2` het type dat dezelfde regels volgt.
    1. IList `T2` waarbij T2 dezelfde regels volgt.
    1. Heeft geen recursieve typen.

De para meter `stream` is de stroom die het geserialiseerde object bevat. `Deserialize` retourneert een verzameling `T` exemplaren.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` biedt context met het mechanisme voor het publiceren van diagnostische gegevens voor de gebruikers operator.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` is de diagnostische gegevens voor door de gebruiker gedefinieerde Opera Tors, zoals serialisatiefunctie, deserializer en door de gebruiker gedefinieerde functies.

`WriteError` schrijft een fout bericht naar bron logboeken en stuurt de fout naar diagnostische gegevens.

`briefMessage` is een kort fout bericht. Dit bericht wordt weer gegeven in diagnostische gegevens en wordt door het product team gebruikt voor fout opsporing. Neem geen gevoelige informatie op en behoud het bericht van minder dan 200 tekens

`detailedMessage` is een gedetailleerd fout bericht dat alleen wordt toegevoegd aan de bron Logboeken in uw opslag. Dit bericht moet kleiner zijn dan 2000 tekens.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Voor beelden van deserialiseren

In deze sectie wordt beschreven hoe u aangepaste deserializers schrijft voor protobuf en CSV. Ga voor meer voor beelden, zoals de AVRO-indeling voor Event hub Capture, naar [Azure stream Analytics op github](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>De protobuf-indeling (protocol buffer)

Dit is een voor beeld van protocol buffer indeling.

Neem de volgende protocol buffer definitie op.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

`protoc.exe`Vanuit de **Google. protobuf. tools** NuGet wordt een. CS-bestand met de definitie gegenereerd. Het gegenereerde bestand wordt hier niet weer gegeven. U moet ervoor zorgen dat de versie van protobuf Nuget die u in uw Stream Analytics-project gebruikt, overeenkomt met de protobuf-versie die is gebruikt voor het genereren van de invoer. 

Het volgende code fragment is de implementatie van de deserializer, ervan uitgaande dat het gegenereerde bestand is opgenomen in het project. Deze implementatie is een smalle wrapper op het gegenereerde bestand.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Het volgende code fragment is een eenvoudige CSV-deserializer die ook het door geven van fouten illustreert.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Serialisatie-indeling voor REST-Api's

Elke Stream Analytics invoer heeft een **serialisatie-indeling**. Zie de documentatie over [invoer rest API](/rest/api/streamanalytics/2016-03-01/inputs) voor meer informatie over invoer opties.

De volgende Java script-code is een voor beeld van de serialisatie-indeling voor .NET-deserialisatie bij gebruik van de REST API:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` moet een klasse zijn die wordt geïmplementeerd `StreamDeserializer<T>` . Dit wordt beschreven in de volgende sectie.

## <a name="region-support"></a>Ondersteuning voor regio

Deze functie is beschikbaar in de volgende regio's:

* VS - west-centraal
* Europa - noord
* VS - oost
* VS - west
* VS - oost 2
* Europa -west

U kunt [ondersteuning aanvragen](https://aka.ms/ccodereqregion) voor extra regio's.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Wanneer is deze functie beschikbaar in alle Azure-regio's?

Deze functie is beschikbaar in [6 regio's](#region-support). Als u deze functionaliteit in een andere regio wilt gebruiken, kunt u [een aanvraag indienen](https://aka.ms/ccodereqregion). Ondersteuning voor alle Azure-regio's bevindt zich op het schema.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kan ik toegang krijgen tot MetadataPropertyValue van mijn invoer vergelijkbaar met de functie GetMetadataPropertyValue?

Deze functionaliteit wordt niet ondersteund. Als u deze mogelijkheid nodig hebt, kunt u voor deze aanvraag stemmen op [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kan ik mijn implementatie voor deserialisatie delen met de community zodat anderen kunnen profiteren?

Wanneer u uw deserializer hebt geïmplementeerd, kunt u anderen helpen door deze te delen met de community. Verzend uw code naar het [Azure stream Analytics github opslag plaats](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Wat zijn de andere beperkingen van het gebruik van aangepaste deserialers in Stream Analytics?

Als de invoer van de protobuf-indeling met een schema met `MapField` type is, kunt u geen aangepaste deserialisatie implementeren. Aangepaste deserialers ondersteunen ook geen voorbeeld gegevens of voorbeeld gegevens. 

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste .NET-deserialisatie voor Azure Stream Analytics Cloud taken](custom-deserializer.md)
