---
title: Trigger gegevens door geven aan pijp lijn
description: Meer informatie over het verwijzen naar trigger-meta gegevens in een pijp lijn
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 50a9f9cd59ebeecae89580c878442eb20788f462
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593642"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Meta gegevens van de referentie trigger in pijplijn uitvoeringen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe meta gegevens van triggers, zoals begin tijd trigger, kunnen worden gebruikt bij het uitvoeren van de pijp lijn.

Het is soms nodig om in de pijp lijn meta gegevens te begrijpen en te lezen uit trigger waarmee deze wordt aangeroepen. Bijvoorbeeld: als de Tumblingvenstertriggers-venster trigger wordt uitgevoerd op basis van de begin-en eind tijd van het venster, verwerken pijp lijn verschillende gegevens segmenten of mappen. In Azure Data Factory gebruiken we de parameterisering-en [systeem variabele](control-flow-system-variables.md) om meta gegevens door te geven van triggers aan pijp lijn.

Dit patroon is vooral nuttig voor de trigger van het [tumblingvenstertriggers-venster](how-to-create-tumbling-window-trigger.md), waarbij trigger de start-en eind tijd van het venster biedt en [aangepaste gebeurtenis trigger](how-to-create-custom-event-trigger.md), waarbij het parseren en verwerken van waarden in het [aangepaste gedefinieerde _gegevens_ veld](../event-grid/event-schema.md)wordt geactiveerd.

> [!NOTE]
> Een ander trigger type biedt verschillende meta gegevens informatie. Zie [systeem variabele](control-flow-system-variables.md) voor meer informatie.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie wordt beschreven hoe u gegevens van meta gegevens van triggers naar pijp lijn doorgeeft in de Azure Data Factory gebruikers interface.

1. Ga naar het **ontwerp canvas** en bewerk een pijp lijn

1. Klik op het lege canvas om de pijplijn instellingen te openen. Selecteer geen activiteit. Mogelijk moet u het instellingen paneel vanuit de onderkant van het canvas ophalen, omdat het mogelijk is samengevouwen

1. Selecteer de sectie **para meters** en selecteer **+ New** om para meters toe te voegen

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Scherm opname van de pijplijn instelling waarin wordt getoond hoe u para meters in de pijp lijn definieert.":::

1. Voeg triggers toe aan de pijp lijn door te klikken op **+ trigger**.

1. Maak of koppel een trigger aan de pijp lijn en klik op **OK**

1. Vul op de volgende pagina trigger-meta gegevens voor elke para meter in. Gebruik de indeling die in de [systeem variabele](control-flow-system-variables.md) is gedefinieerd om de trigger gegevens op te halen. U hoeft de gegevens voor alle para meters niet in te vullen, maar alleen de waarden die van kracht zijn voor de meta gegevens van de trigger. Hier geven we bijvoorbeeld de begin tijd voor het uitvoeren van de trigger aan *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Scherm afbeelding van de trigger definitie pagina waarin wordt getoond hoe trigger gegevens worden door gegeven aan pijplijn parameters.":::

1. Als u de waarden in de pijp lijn wilt gebruiken, gebruikt u para meters _@pipeline (). para meters. para_ meter, __niet__ systeem variabele, in pijplijn definities. Als u bijvoorbeeld de begin tijd van de trigger wilt lezen, verwijzen we naar @pipeline () .parameters.parameter_1.

## <a name="json-schema"></a>JSON-schema

Als u gegevens van triggers wilt door geven aan pijp lijnen, moeten zowel de trigger als de JSON van de pijp lijn worden bijgewerkt met _para meters_ .

### <a name="pipeline-definition"></a>Pijplijn definitie

Voeg in de sectie **Eigenschappen** parameter definities toe aan de sectie **para meters**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Trigger definitie

Wijs in het gedeelte **pijp lijnen** parameter waarden toe in het gedeelte **para meters** . U hoeft de gegevens voor alle para meters niet in te vullen, maar alleen de waarden die van kracht zijn voor de meta gegevens van de trigger.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Trigger informatie gebruiken in pijp lijn

Als u de waarden in de pijp lijn wilt gebruiken, gebruikt u para meters _@pipeline (). para meters. para_ meter, __niet__ systeem variabele, in pijplijn definities.

## <a name="next-steps"></a>Volgende stappen

Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor meer informatie over triggers.
