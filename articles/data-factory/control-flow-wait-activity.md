---
title: Wachten op activiteit in Azure Data Factory
description: De wait-activiteit pauzeert de uitvoering van de pijp lijn voor de opgegeven periode.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 9271723e68e5edd6805f0197bca9cdefc3c5e921
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388325"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Wacht activiteit uitvoeren in Azure Data Factory
Als u een Wait Activity in een pijplijn gebruikt, wacht de pijplijn tot de opgegeven periode voorbij is voordat de volgende activiteiten worden uitgevoerd. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
naam | De naam van de `Wait` activiteit. | Tekenreeks | Ja
type | Moet worden ingesteld op **wait**. | Tekenreeks | Ja
waitTimeInSeconds | Het aantal seconden dat de pijp lijn wacht voordat de verwerking doorgaat. | Geheel getal | Yes

## <a name="example"></a>Voorbeeld

> [!NOTE]
> Deze sectie bevat JSON-definities en voor beelden van Power shell-opdrachten voor het uitvoeren van de pijp lijn. Zie [zelf studie: een Data Factory maken met behulp van Azure PowerShell](quickstart-create-data-factory-powershell.md)voor een overzicht met stapsgewijze instructies voor het maken van een Data Factory pijp lijn met behulp van Azure PowerShell-en JSON-definities.

### <a name="pipeline-with-wait-activity"></a>Pijp lijn met wachtende activiteit
In dit voor beeld heeft de pijp lijn twee activiteiten: **tot** en met een **ogen blik geduld**. De wacht activiteit is zo geconfigureerd dat deze gedurende één seconde wordt gewacht. De pijp lijn voert de Web-activiteit in een lus uit met één seconde wacht tijd tussen elke uitvoering. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Volgende stappen
Zie andere controle stroom activiteiten die door Data Factory worden ondersteund: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Pijplijn activiteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)
