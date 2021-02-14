---
title: Afhankelijkheden voor tumblingvenstertriggers-venster maken
description: Meer informatie over het maken van afhankelijkheden voor een tumblingvenstertriggers-venster trigger in Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361465"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Een afhankelijkheid voor een tumblingvenstertrigger maken
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden de stappen beschreven voor het maken van een afhankelijkheid van een tumblingvenstertriggers-venster trigger. Zie [How to Create tumblingvenstertriggers Window trigger](how-to-create-tumbling-window-trigger.md)(Engelstalig) voor algemene informatie over Tumblingvenstertriggers-venster triggers.

Als u een afhankelijkheids keten wilt maken en ervoor wilt zorgen dat een trigger pas wordt uitgevoerd nadat een andere trigger in de data factory is uitgevoerd, gebruikt u deze geavanceerde functie om een tumblingvenstertriggers-venster afhankelijkheid te maken.

Bekijk de volgende video voor een demonstratie van het maken van afhankelijke pijp lijnen in uw Azure Data Factory met behulp van de venster trigger voor tumblingvenstertriggers:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Een afhankelijkheid maken in de Data Factory-gebruikers interface

Als u afhankelijkheid wilt maken voor een trigger, selecteert u **trigger > geavanceerd > nieuw** en kiest u vervolgens de trigger die afhankelijk is van de juiste offset en grootte. Selecteer **volt ooien** en publiceer de Data Factory wijzigingen voordat de afhankelijkheden van kracht worden.

![Afhankelijkheid maken](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Afhankelijkheid maken")

## <a name="tumbling-window-dependency-properties"></a>Eigenschappen van afhankelijkheids venster tumblingvenstertriggers

Een tumblingvenstertriggers-venster trigger met een afhankelijkheid heeft de volgende eigenschappen:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

De volgende tabel bevat de lijst met kenmerken die nodig zijn voor het definiëren van een Tumblingvenstertriggers-venster afhankelijkheid.

| **Eigenschaps naam** | **Beschrijving**  | **Type** | **Vereist** |
|---|---|---|---|
| type  | Alle bestaande tumblingvenstertriggers-venster triggers worden weer gegeven in deze vervolg keuzelijst. Kies de trigger voor het maken van afhankelijkheden.  | TumblingWindowTriggerDependencyReference of SelfDependencyTumblingWindowTriggerReference | Yes |
| offset | Verschuiving van de afhankelijkheids trigger. Geef een waarde op in de indeling van de tijd en zowel negatieve als positieve verschuivingen zijn toegestaan. Deze eigenschap is verplicht als de trigger afhankelijk is van zichzelf en in alle andere gevallen optioneel is. De Self-afhankelijkheid moet altijd een negatieve verschuiving zijn. Als er geen waarde is opgegeven, is het venster hetzelfde als de trigger. | Periode<br/>(UU: mm: SS) | Zelf afhankelijkheid: Ja<br/>Overige: Nee |
| grootte | Grootte van het tumblingvenstertriggers-venster van de afhankelijkheid. Geef een positieve time span-waarde op. Deze eigenschap is optioneel. | Periode<br/>(UU: mm: SS) | No  |

> [!NOTE]
> Een trigger voor een tumblingvenstertriggers-venster kan afhankelijk zijn van een maximum van vijf andere triggers.

## <a name="tumbling-window-self-dependency-properties"></a>Eigenschappen van tumblingvenstertriggers-venster met eigen afhankelijkheid

In scenario's waarin de trigger niet moet door gaan naar het volgende venster totdat het vorige venster is voltooid, bouwt u een self-afhankelijkheid. Een trigger voor zelf afhankelijkheid die afhankelijk is van het slagen van een eerdere uitvoering van zichzelf binnen het voor gaande uur, heeft de eigenschappen die in de volgende code worden aangegeven.

> [!NOTE]
> Als uw geactiveerde pijp lijn afhankelijk is van de uitvoer van pijp lijnen in eerder geactiveerde Windows, raden we u aan om alleen tumblingvenstertriggers venster trigger-afhankelijkheid te gebruiken. Als u parallelle trigger uitvoeringen wilt beperken, stelt u de gelijktijdigheid van de maximimum-trigger in.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Gebruiks scenario's en voor beelden

Hieronder ziet u een aantal voor beelden van scenario's en het gebruik van eigenschappen van tumblingvenstertriggers-venster afhankelijkheid.

### <a name="dependency-offset"></a>Offset van afhankelijkheid

![Offset-voor beeld](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Offset-voor beeld")

### <a name="dependency-size"></a>Grootte van afhankelijkheid

![Voor beeld van grootte](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Voor beeld van grootte")

### <a name="self-dependency"></a>Zelf afhankelijkheid

![Zelf afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Zelf afhankelijkheid")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Afhankelijkheid van een andere tumblingvenstertriggers-venster trigger

Een dagelijkse telemetrie-verwerkings taak, afhankelijk van een andere dagelijkse taak waarmee de uitvoer van de laatste zeven dagen wordt geaggregeerd en die zeven dagen worden gegenereerd:

![Voor beeld van afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Voor beeld van afhankelijkheid")

### <a name="dependency-on-itself"></a>Afhankelijkheid op zichzelf

Een dagelijkse taak zonder hiaten in de uitvoer stromen van de taak:

![Voor beeld van Self-afhankelijkheid](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Voor beeld van Self-afhankelijkheid")

## <a name="monitor-dependencies"></a>Afhankelijkheden controleren

U kunt de afhankelijkheids keten en de bijbehorende Vensters bewaken vanaf de controle pagina voor het uitvoeren van triggers. Navigeer naar  **bewaking > trigger uitvoeringen**. Als een trigger voor een Tumblingvenstertriggers-venster afhankelijkheden heeft, wordt in de trigger naam een Hyper Link naar de weer gave afhankelijkheids controle vermeld.  

![Triggeruitvoeringen controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Trigger uitvoeringen bewaken-volledig in tumblingvenstertriggers venster afhankelijkheids weergave")

Klik op de naam van de trigger om de afhankelijkheden van de trigger weer te geven. Rechterdeel venster bevat gedetailleerde informatie over het uitvoeren van triggers, zoals RunID, venster tijd, status, enzovoort.

![Lijst weergave afhankelijkheden controleren](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Lijst weergave afhankelijkheden controleren")

U kunt de status van de afhankelijkheden en Windows voor elke afhankelijke trigger bekijken. Als een van de afhankelijkheden is mislukt, moet u deze opnieuw uitvoeren om de afhankelijke trigger uit te voeren.

Een trigger voor een tumblingvenstertriggers-venster wacht op afhankelijkheden voor _zeven dagen_ voordat een time-out optreedt. Na zeven dagen mislukt de uitvoering van de trigger.

Als u een visueel element wilt weer geven, selecteert u de Gantt-weer gave.

![Controle van afhankelijkheden Gantt-diagram](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Bewaken van afhankelijkheden van de Gantt-diagram weergave")

In transparante vakken worden de afhankelijkheids Vensters voor elke vervolg keuzelijst met stroom afhankelijke weer gegeven, terwijl effen gekleurde vakken hierboven afzonderlijke venster uitvoeringen weer geven. Hier volgen enkele tips voor het interpreteren van de Gantt-diagram weergave:

* Transparant vak wordt blauw weer gegeven wanneer afhankelijke Vensters de status in behandeling of actief hebben
* Wanneer alle Windows is geslaagd voor een afhankelijke trigger, wordt het transparante vakje groen
* Transparant vak wordt rood weer gegeven wanneer een deel van een afhankelijk venster mislukt. Zoek naar een effen rood vak om de uitvoering van het fout venster te identificeren

Als u een venster opnieuw wilt uitvoeren in de weer gave Gantt-diagram, selecteert u het vak effen kleuren voor het venster en verschijnt een actie paneel met details en de opties voor opnieuw uitvoeren

## <a name="next-steps"></a>Volgende stappen

* Lees [hoe u een tumblingvenstertriggers-venster trigger maakt](how-to-create-tumbling-window-trigger.md)
