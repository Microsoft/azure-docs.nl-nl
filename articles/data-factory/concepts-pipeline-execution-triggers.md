---
title: Pijplijnen uitvoeren en triggers in Azure Data Factory
description: Dit artikel bevat informatie over het uitvoeren van een pijplijn in Azure Data Factory, op aanvraag of door een trigger te maken.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: c72538de8aba60ce7ed880561b55773c22737f97
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498622"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Pijplijnen uitvoeren en triggers in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-scheduling-and-execution.md)
> * [Huidige versie](concepts-pipeline-execution-triggers.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een _pijplijnuitvoering_ in Azure Data Factory definieert een exemplaar van een pijplijnuitvoering. Stel dat u een pijplijn hebt die wordt uitgevoerd om 8:00 uur, 9:00 uur en 10:00 uur. In dit geval zijn er drie afzonderlijke uitvoeringen van de pijp lijn of pijplijn uitvoeringen. Elke pijplijnuitvoering heeft een unieke id. Een uitvoerings-id is een GUID die de betreffende specifieke pijplijnuitvoering definieert.

Pijplijnuitvoeringen worden doorgaans geïnstantieerd doordat argumenten worden doorgegeven aan parameters die u in de pijplijn definieert. U kunt een pijplijn handmatig uitvoeren of door middel van een _trigger_. Dit artikel bevat informatie over beide manieren om een pijplijn uit te voeren.

## <a name="manual-execution-on-demand"></a>Handmatig uitvoeren (op aanvraag)

De handmatige uitvoering van een pijplijn wordt ook wel een uitvoering _op aanvraag_ genoemd.

Stel dat u een eenvoudige pijplijn met de naam **copyPipeline** hebt die u wilt uitvoeren. De pijplijn heeft één activiteit, waarmee items worden gekopieerd uit een bronmap in Azure Blob Storage naar een doelmap in dezelfde opslagplaats. In de volgende JSON-definitie wordt dit voorbeeld getoond:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

In de JSON-definitie heeft de pijplijn twee parameters: **sourceBlobContainer** en **sinkBlobContainer**. Tijdens runtime geeft u waarden door aan deze parameters.

U kunt de pijplijn handmatig uitvoeren met een van de volgende methoden:
- .NET SDK
- Azure PowerShell-module
- REST-API
- Python-SDK

### <a name="rest-api"></a>REST-API

Met de volgende voorbeeld opdracht ziet u hoe u de pijp lijn kunt uitvoeren met behulp van de REST API hand matig:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Zie [Snelstart: een Azure data factory en pijplijn maken door de REST-API te gebruiken](quickstart-create-data-factory-rest-api.md) voor het volledige voorbeeld.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In de volgende voorbeeldopdracht wordt getoond hoe u de pijplijn handmatig kunt uitvoeren met behulp van Azure PowerShell:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

U kunt parameters doorgeven in de hoofdtekst van de nettolading van de aanvraag. In de .NET SDK, in Azure Powershell en in de Python SDK geeft u waarden in een woordenlijst door als een argument naar de aanroep:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

De nettolading van de reactie is een unieke ID van de pijplijnuitvoering:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Zie [Snelstart: een data factory in Azure maken met behulp van Azure PowerShell](quickstart-create-data-factory-powershell.md) voor een volledig voorbeeld.

### <a name="net-sdk"></a>.NET SDK

In de volgende voorbeeld aanroep ziet u hoe u de pijp lijn kunt uitvoeren met behulp van de .NET SDK hand matig:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Zie [Snelstart: een data factory en pijplijn maken met behulp van .NET SDK](quickstart-create-data-factory-dot-net.md) voor een volledig voorbeeld.

> [!NOTE]
> U kunt de .NET SDK gebruiken om Data Factory pijp lijnen aan te roepen vanuit Azure Functions, van uw webservices, enzovoort.

## <a name="trigger-execution"></a>Uitvoeren van triggers

Triggers zijn een andere manier om een pijplijnuitvoering te starten. Triggers zijn verwerkingseenheden die bepalen wanneer een pijplijnuitvoering moet worden gestart. Data Factory ondersteunt momenteel drie soorten triggers:

- Schematrigger: een trigger die een pijplijn volgens een wandklokschema aanroept.

- Tumblingvenstertrigger: een trigger die volgens een periodiek interval werkt terwijl de status behouden blijft.

- Trigger op basis van gebeurtenissen: een trigger die reageert op een gebeurtenis.

Pijp lijnen en triggers hebben een veel-op-veel-relatie (met uitzonde ring van de venster trigger voor tumblingvenstertriggers). Meerdere triggers kunnen één pijp lijn starten en één trigger kan meerdere pijp lijnen starten. In de volgende triggerdefinitie verwijst de eigenschap **pijplijnen** naar een lijst met pijplijnen die worden geactiveerd door de bijbehorende trigger. In de definitie van de eigenschap zijn waarden opgenomen voor de pijplijnparameters.
### <a name="basic-trigger-definition"></a>Basisdefinitie voor trigger

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Schematrigger
Schematriggers voeren pijplijnen uit volgens een wandklokschema. De trigger ondersteunt periodieke en geavanceerde kalenderopties. De trigger ondersteunt bijvoorbeeld intervallen als 'wekelijks' of 'maandag om 17:00 uur en donderdag om 21:00 uur'. De schematrigger is flexibel omdat het patroon van de gegevensset agnostisch is, dat wil zeggen dat de trigger geen onderscheid maakt tussen gegevens in tijdreeksen en niet-tijdreeksen.

Zie [een trigger maken waarmee een pijp lijn wordt uitgevoerd volgens een planning](how-to-create-schedule-trigger.md)voor meer informatie over het plannen van triggers en voor beelden.

## <a name="schedule-trigger-definition"></a>Schematrigger: definitie
Wanneer u een schematrigger maakt, geeft u het schema en een terugkeerpatroon op met behulp van een JSON-definitie.

Als u wilt dat de schematrigger een pijplijnuitvoering activeert, moet u een verwijzing naar de betreffende pijplijn opnemen in de definitie van de trigger. Pijplijnen en triggers hebben een veel-op-veel-relatie. Meerdere triggers kunnen één pijplijn activeren. Eén trigger kan meerdere pijplijnen activeren.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> De eigenschap **parameters** is een verplichte eigenschap van het element **pijplijnen**. Als de pijplijn geen parameters aanneemt, dient u een lege JSON-definitie op te nemen voor de eigenschap **parameters**.

### <a name="schema-overview"></a>Schema-overzicht
De volgende tabel bevat een overzicht van de belangrijkste schema-elementen die betrekking hebben op het terugkeerpatroon en het schema van een trigger:

| JSON-eigenschap | Beschrijving |
| --- | --- |
| **startTime** | Een datum/tijdwaarde. Voor eenvoudige schema's is de waarde **startTime** van toepassing op de eerste gebeurtenis. In complexe schema's begint de trigger niet eerder dan de opgegeven waarde voor **startTime**. |
| **endTime** | De einddatum en -tijd voor de trigger. De trigger wordt na de opgegeven einddatum en -tijd niet uitgevoerd. De waarde voor de eigenschap kan niet in het verleden liggen. <!-- This property is optional. --> |
| **Tijd zone** | De tijdzone. Zie [een trigger maken waarmee een pijp lijn op een schema wordt uitgevoerd](how-to-create-schedule-trigger.md#time-zone-option)voor een lijst met ondersteunde tijd zones. |
| **optreden** | Een recurrence-object bepaalt de regels voor het terugkeerpatroon van de trigger. Het recurrence-object ondersteunt de elementen **frequency**, **interval**, **endTime**, **count** en **schedule**. Als een recurrence-object wordt gedefinieerd, is het element **frequency** vereist. De overige elementen van het recurrence-object zijn optioneel. |
| **frequency** | Hiermee geeft u de frequentie aan waarmee de trigger wordt uitgevoerd. De ondersteunde waarden omvatten 'minuut', 'uur', 'dag', 'week' en 'maand'. |
| **bereik** | Een positief geheel getal dat het interval voor de waarde **frequency** aangeeft. De waarde **frequency** bepaalt hoe vaak de trigger wordt uitgevoerd. Als **interval** bijvoorbeeld 3 is en **frequency** 'week', dan wordt de trigger elke drie weken uitgevoerd. |
| **planning** | Het terugkeerschema voor de trigger. Een trigger met een opgegeven waarde voor **frequency** wijzigt het terugkeerpatroon op basis van een terugkeerschema. De eigenschap **property** bevat wijzigingen voor het terugkeerpatroon en zijn gebaseerd op minuten, uren, weekdagen, maanddagen en weeknummer. |

### <a name="schedule-trigger-example"></a>Voorbeeld van schedule-trigger

```json
{
  "properties": {
    "name": "MyTrigger",
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": "Hour",
        "interval": 1,
        "startTime": "2017-11-01T09:00:00-08:00",
        "endTime": "2017-11-02T22:00:00-08:00"
      }
    },
    "pipelines": [{
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToBlobPipeline"
        },
        "parameters": {}
      },
      {
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToAzureSQLPipeline"
        },
        "parameters": {}
      }
    ]
  }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Standaardschemawaarden, limieten en voorbeelden

| JSON-eigenschap | Type | Vereist | Standaardwaarde | Geldige waarden | Voorbeeld |
| --- | --- | --- | --- | --- | --- |
| **startTime** | tekenreeks | Ja | Geen | Datums en tijden volgens ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **optreden** | object | Ja | Geen | Een recurrence-object | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **bereik** | getal | Nee | 1 | 1 tot 1000 | `"interval":10` |
| **endTime** | tekenreeks | Ja | Geen | Een datum/tijdwaarde die een toekomstig tijdstip aangeeft | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **planning** | object | Nee | Geen | Een schedule-object | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Eigenschap startTime
In de volgende tabel ziet u hoe de eigenschap **startTime** de uitvoering van een trigger bepaalt:

| startTime-waarde | Recurrence zonder schedule | Recurrence met schedule |
| --- | --- | --- |
| **Starttijd ligt in het verleden** | Berekent de eerstvolgende uitvoering na de starttijd en voert deze op dat moment uit.<br /><br />Voert volgende uitvoeringen uit die zijn bekend op basis van de tijd waarop de laatste uitvoering heeft plaatsgevonden.<br /><br />Zie het voorbeeld onder deze tabel. | De trigger wordt _nooit vóór_ de opgegeven begintijd geactiveerd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd.<br /><br />Volgende uitvoeringen worden op basis van het terugkeerschema uitgevoerd. |
| **Starttijd ligt in de toekomst of op de huidige tijd** | Uitvoering vindt eenmaal plaats op de opgegeven starttijd.<br /><br />Voert volgende uitvoeringen uit die zijn bekend op basis van de tijd waarop de laatste uitvoering heeft plaatsgevonden. | De trigger wordt _niet eerder_ gestart dan de opgegeven begin tijd. De eerste uitvoering is gebaseerd op de planning die wordt berekend op basis van de starttijd.<br /><br />Volgende uitvoeringen worden op basis van het terugkeerschema uitgevoerd. |

Laten we eens kijken naar een voorbeeld van wat er gebeurt wanneer de startTime in het verleden ligt en er een terugkeerpatroon (recurrence), maar geen schema (schedule) is opgegeven. Neem aan dat de huidige tijd 2017-04-08 13:00 is, de starttijd 2017-04-07 14:00 en het terugkeerpatroon om de dag. (De waarde voor het **terugkeer patroon** wordt gedefinieerd door de eigenschap **Frequency** in te stellen op ' dag ' en de eigenschap **interval** op 2.) U ziet dat de waarde voor **StartTime** in het verleden ligt en vóór de huidige tijd plaatsvindt.

Onder deze omstandigheden is de eerste uitvoering 2017-04-09 op 14:00. De scheduler-engine berekent uitvoeringen vanaf de startTime. Alle uitvoeringen in het verleden worden genegeerd. De engine gebruikt de eerstvolgende uitvoering die in de toekomst plaatsvindt. In dit scenario is de starttijd 2017-04-07 om 14:00 uur. De volgende uitvoering is twee dagen daarna op 2017-04-09 om 14:00 uur.

De eerste uitvoeringstijd is de hetzelfde, zelfs als **startTime** 2017-04-05 14:00 of 2017-04-01 14:00 is. Na de eerste uitvoering worden volgende uitvoeringen berekend met behulp van het schema. Daarom vinden de volgende uitvoeringen plaats op 2017-04-11 om 14:00 uur, op 2017-04-13 om 14:00 uur en op 2017-04-15 om 14:00 uur enzovoort.

Ten slotte, als uren of minuten niet worden ingesteld in het schema voor een trigger, worden de uren of minuten van de eerste uitvoering als standaard waarden gebruikt.

### <a name="schedule-property"></a>Eigenschap schedule
U kunt **schedule** gebruiken om het aantal uitvoeringen door een *trigger* te beperken. Als een trigger met de frequency 'maand' bijvoorbeeld een schedule-waarde heeft die alleen wordt uitgevoerd op dag 31, wordt de trigger alleen uitgevoerd in maanden die een 31e dag hebben.

Anderzijds kunt u **schedule** gebruiken om het aantal uitvoeringen door een trigger *uit te breiden*. Bijvoorbeeld: een trigger met een geplande maandfrequentie voor uitvoering op de maanddagen 1 en 2, wordt uitgevoerd op de eerste en tweede dag van de maand, in plaats van eenmaal per maand.

Als er meerdere **plannings** elementen worden opgegeven, is de volg orde van de evaluatie van de grootste naar de kleinste schema-instelling: week nummer, maand dag, weekdag, uur, minuut.

In de volgende tabel worden de **schedule**-elementen in detail beschreven:

| JSON-element | Beschrijving | Geldige waarden |
| --- | --- | --- |
| **wachten** | Minuten van het uur waarop de trigger wordt uitgevoerd. |- Geheel getal<br />- Matrix van gehele getallen |
| **loopt** | Uren van de dag waarop de trigger wordt uitgevoerd. |- Geheel getal<br />- Matrix van gehele getallen |
| **weekDays** | Dagen van de week waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een weekfrequentie.|<br />- maandag<br />- dinsdag<br />- woensdag<br />- donderdag<br />- vrijdag<br />- zaterdag<br />- zondag<br />- Matrix met dagwaarden (maximale grootte van de matrix is 7)<br /><br />Dag-waarden zijn niet hoofdletter gevoelig |
| **monthlyOccurrences** | Dagen van de maand waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een maandfrequentie. |-Matrix van **monthlyOccurrence** -objecten: `{ "day": day, "occurrence": occurrence }`<br />- Het attribuut **day** is de dag van de week waarop de trigger wordt uitgevoerd. Zo betekent de eigenschap **monthlyOccurrences** met een waarde **day** van `{Sunday}` dat er elke zondag van de maand een uitvoering is. Het attribuut **day** is verplicht.<br />- Het attribuut **occurrence** slaat op het uitvoeren van de trigger op de opgegeven dag, **day**, tijdens de maand. Zo betekent de eigenschap **monthlyOccurrences** met de waarden **day** en **occurrence** van `{Sunday, -1}` dat er elke laatste zondag van de maand een uitvoering is. Het attribuut **occurrence** is optioneel. |
| **monthDays** | Dagen van de maand waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een maandfrequentie. |- Alle waarden < = -1 en > =-31<br />- Alle waarden > = 1 en < =31<br />- Matrix met waarden |

## <a name="tumbling-window-trigger"></a>Tumblingvenstertrigger
Tumblingvenstertriggers zijn triggers die vanaf een opgegeven begintijd worden geactiveerd met een periodiek tijdsinterval en die hun status behouden. Tumblingvensters bestaan uit een reeks niet-overlappende en aaneengesloten tijdsintervallen van vaste duur.

Zie [trigger voor het maken van een tumblingvenstertriggers-venster](how-to-create-tumbling-window-trigger.md)voor meer informatie over tumblingvenstertriggers-venster triggers en voor beelden.

## <a name="event-based-trigger"></a>Trigger op basis van gebeurtenissen

Een trigger op basis van een gebeurtenis voert pijp lijnen uit als reactie op een gebeurtenis, zoals de ontvangst van een bestand of het verwijderen van een bestand, in Azure Blob Storage.

Zie [Een trigger maken die een pijplijn uitvoert in reactie op een gebeurtenis](how-to-create-event-trigger.md) voor meer informatie over triggers op basis van gebeurtenissen.

## <a name="examples-of-trigger-recurrence-schedules"></a>Voorbeelden van schema's voor uitvoeringen van triggers
Dit gedeelte bevat voorbeelden van schema's met terugkeerpatronen. Dit artikel gaat over het **schedule**-object en de bijbehorende elementen.

In de voor beelden wordt ervan uitgegaan dat de waarde voor **interval** 1 is en dat de waarde van de **frequentie** juist is volgens de definitie van de planning. De waarde voor **frequency** kan bijvoorbeeld niet tegelijkertijd 'day' zijn én een wijziging **monthDays** in het **schedule**-object hebben. Dit soort beperkingen wordt beschreven in de tabel in het vorige gedeelte.

| Voorbeeld | Beschrijving |
| --- | --- |
| `{"hours":[5]}` | Wordt elke dag om 5:00 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5]}` | Wordt elke dag om 5:15 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5,17]}` | Wordt elke dag om 05:15 en 17:15 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17]}` | Wordt elke dag om 05:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45]}` | Wordt elke 15 minuten uitgevoerd. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Wordt elk uur uitgevoerd.<br /><br />Deze trigger wordt elk uur uitgevoerd. De minuten worden door de waarde **startTime** bepaald (indien opgegeven). Als er geen waarde is opgegeven, worden de minuten bepaald door de aanmaaktijd. Als de starttijd of aanmaaktijd (afhankelijk van wat van toepassing is) bijvoorbeeld 12:25 uur is, wordt de trigger uitgevoerd om 00:25, 01:25, 02:25, ..., en 23:25 uur.<br /><br />Dit schema komt overeen met een trigger met **frequency** 'uur', **interval** 1 en geen waarde voor **schedule**. Dit schema kan worden gebruikt met verschillende waarden voor **frequency** en **interval** om andere triggers te maken. Als bijvoorbeeld de waarde **frequency** 'maand' is, dan wordt het schema slechts eenmaal per maand uitgevoerd in plaats van elke dag (als **frequency** 'dag' is). |
| `{"minutes":[0]}` | Wordt elk uur op het hele uur uitgevoerd.<br /><br />Deze trigger wordt elk uur op het hele uur uitgevoerd, te beginnen om 00:00 uur en vervolgens om 1:00 uur, 2:00 uur enzovoort.<br /><br />Dit schema is gelijkwaardig met een trigger met **frequency** 'uur' en **startTime** nul minuten, en met **schedule** zonder waarde en **frequency** 'dag'. Als de waarde voor **frequency** 'week' of 'maand' is, wordt het schema respectievelijk één dag per week of één dag per maand uitgevoerd. |
| `{"minutes":[15]}` | Wordt 15 minuten na elk uur uitgevoerd.<br /><br />Deze trigger wordt elke 15 minuten na het hele uur uitgevoerd, te beginnen om 00:15 uur, en vervolgens om 1:15 uur, 2:15 uur, met de laatste uitvoering om 23:15 uur. |
| `{"hours":[17], "weekDays":["saturday"]}` | Wordt elke week op zaterdag om 17:00 uur uitgevoerd. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wordt elke week op maandag, woensdag en vrijdag om 17:00 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wordt elke week op maandag, woensdag en vrijdag om 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Wordt op weekdagen elke 15 minuten uitgevoerd. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Wordt op weekdagen elke 15 minuten tussen 9:00 en 16:45 uur uitgevoerd. |
| `{"weekDays":["tuesday", "thursday"]}` | Wordt op dinsdag en donderdag op de opgegeven begintijd uitgevoerd. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Wordt op dag 28 van elke maand om 6:00 uur uitgevoerd (bij een waarde voor **frequency** van 'maand'). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Wordt op de laatste dag van de maand om 6:00 uur uitgevoerd.<br /><br />Als u een trigger wilt uitvoeren op de laatste dag van een maand, gebruik dan -1 in plaats van dag 28, 29, 30 of 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Wordt op de eerste en laatste dag van elke maand om 6:00 uur uitgevoerd. |
| `{monthDays":[1,14]}` | Wordt uitgevoerd op de eerste en veertiende dag van elke maand op de opgegeven begintijd. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Wordt op de eerste vrijdagdag van elke maand om 5:00 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Wordt op de eerste vrijdag van elke maand op de opgegeven begintijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Wordt op de derde vrijdag vanaf het eind van de maand elke maand op de opgegeven begintijd uitgevoerd. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Wordt op de eerste en laatste vrijdagdag van elke maand om 5:15 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Wordt op de eerste en laatste vrijdag van elke maand op de opgegeven begintijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Wordt op de vijfde vrijdag van elke maand op de opgegeven begintijd uitgevoerd.<br /><br />Wanneer er geen vijfde vrijdag in een maand voorkomt, wordt de pijplijn niet uitgevoerd. Als u de trigger op de laatste vrijdag van de maand wilt uitvoeren, kunt u voor **occurrence** de waarde -1 gebruiken in plaats van 5. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Wordt op de laatste vrijdag van de maand elke 15 minuten uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Wordt elke maand op de derde woensdag om 5:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |

## <a name="trigger-type-comparison"></a>Vergelijkingen tussen triggertypen
Tumblingvenstertriggers en de schematrigger werken beide volgens tijdschema's. Hoe verschillen ze van elkaar?

> [!NOTE]
> De trigger voor het tumblingvenstertriggers-venster wordt uitgevoerd, *wacht tot de geactiveerde pijplijn uitvoering* is voltooid. De uitvoerings status weerspiegelt de status van de geactiveerde pijplijn uitvoering. Als bijvoorbeeld een geactiveerde pijplijn uitvoering wordt geannuleerd, wordt de bijbehorende trigger voor het tumblingvenstertriggers-venster geactiveerd als geannuleerd gemarkeerd. Dit wijkt af van het gedrag ' brand and vergeet ' van de Schedule-trigger. Dit is gemarkeerd als een pijp lijn gestart.

In de volgende tabel wordt een vergelijking weergegeven tussen de tumblingvenstertrigger en de schematrigger:

| Item | Tumblingvenstertrigger | Schematrigger |
| --- | --- | --- |
| **Backfill-scenario's** | Ondersteund. Pijplijnuitvoeringen kunnen voor tijdvensters in het verleden worden gepland. | Niet ondersteund. Pijplijnuitvoeringen kunnen alleen worden uitgevoerd in perioden vanaf de huidige tijd. |
| **Betrouwbaarheid** | 100% betrouwbaarheid. Pijplijnuitvoeringen kunnen vanaf een bepaalde begindatum zonder onderbrekingen worden uitgevoerd voor alle tijdvensters. | Minder betrouwbaar. |
| **Mogelijkheid voor nieuwe uitvoerpogingen** | Ondersteund. Nieuwe pogingen van pijplijnuitvoeringen vinden plaats volgens het standaardbeleid van 0 of volgens een beleid dat de gebruiker in de triggerdefinitie heeft opgegeven. Wordt automatisch opnieuw geprobeerd wanneer de pijp lijn wordt uitgevoerd vanwege gelijktijdigheids-, server-en beperkings limieten (dat wil zeggen, status codes 400: gebruikers fout, 429: te veel aanvragen en 500: interne server fout). | Niet ondersteund. |
| **Gelijktijdigheid** | Ondersteund. Gebruikers kunnen expliciet gelijktijdigheidsbeperkingen voor de trigger instellen. Het is mogelijk om tussen de 1 en 50 door triggers geactiveerde pijplijnuitvoeringen gelijktijdig uit te voeren. | Niet ondersteund. |
| **Systeem variabelen** | In combi natie met @trigger (). scheduledTime en @trigger (). StartTime, wordt ook het gebruik van de systeem variabelen **WindowStart** en **WindowEnd** ondersteund. Gebruikers kunnen voor de triggerdefinitie gebruikmaken van `triggerOutputs().windowStartTime` en `triggerOutputs().windowEndTime` als systeemvariabelen in de trigger. De waarden worden respectievelijk als de begin- en eindtijd van het tijdvenster gebruikt. Voor bijvoorbeeld een tumblingvenstertrigger die elk uur wordt uitgevoerd in het tijdvenster 1:00 uur tot 2:00 uur, is de definitie `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` en `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Ondersteunt alleen default @trigger (). scheduledTime en @trigger (). StartTime-variabelen. |
| **Pipeline-trigger-relatie** | Ondersteunt een een-op-een-relatie. Slechts één pijplijn kan worden geactiveerd. | Ondersteunt veel-op-veel-relaties. Meerdere triggers kunnen één pijplijn activeren. Eén trigger kan meerdere pijplijnen activeren. |

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies:

- [Snelstart: Een data factory en pijplijn maken met behulp van .NET SDK](quickstart-create-data-factory-dot-net.md)
- [Een planningstrigger maken](how-to-create-schedule-trigger.md)
- [Een tumblingvenstertrigger maken](how-to-create-tumbling-window-trigger.md)
