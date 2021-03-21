---
title: Tumblingvenstertriggers-venster triggers maken in Azure Data Factory
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijp lijn uitvoert in een tumblingvenstertriggers-venster.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: b961939516ac4848da00a3cd01c754c90da805cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177722"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Een trigger maken die een pijplijn uitvoert op een tumblingvenster

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dit artikel bevat stappen voor het maken, starten en bewaken van een trigger voor een tumblingvenstertriggers-venster. Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md)voor algemene informatie over triggers en de ondersteunde typen.

Tumblingvenstertriggers zijn triggers die vanaf een opgegeven begintijd worden geactiveerd met een periodiek tijdsinterval en die hun status behouden. Tumblingvensters bestaan uit een reeks niet-overlappende en aaneengesloten tijdsintervallen van vaste duur. Een tumblingvenstertriggers-venster trigger heeft een een-op-een-relatie met een pijp lijn en kan alleen verwijzen naar een enkelvoudige pijp lijn. De tumblingvenstertriggers-venster trigger is een meer zwarer alternatief voor Schedule trigger die een reeks functies voor complexe scenario's biedt ([afhankelijkheid van andere tumblingvenstertriggers-venster triggers](#tumbling-window-trigger-dependency), [het opnieuw uitvoeren van een mislukte taak](tumbling-window-trigger-dependency.md#monitor-dependencies) en [opnieuw instellen van de gebruiker voor pijp lijnen](#user-assigned-retries-of-pipelines)). Als u meer inzicht wilt krijgen in het verschil tussen plannings trigger en tumblingvenstertriggers venster trigger, kunt u [hier](concepts-pipeline-execution-triggers.md#trigger-type-comparison)terecht.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

1. Als u een trigger voor een tumblingvenstertriggers-venster wilt maken in de Data Factory-gebruikers interface, selecteert u het tabblad **Triggers** en selecteert u vervolgens **Nieuw**. 
1. Nadat het deel venster trigger configuratie is geopend, selecteert u **Tumblingvenstertriggers venster** en definieert u de trigger eigenschappen van het tumblingvenstertriggers-venster. 
1. Selecteer **Opslaan** als u klaar bent.

![Een trigger voor een tumblingvenstertriggers-venster maken in de Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Eigenschappen van trigger type voor tumblingvenstertriggers-venster

Een tumblingvenstertriggers-venster heeft de volgende trigger type-eigenschappen:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
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
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

De volgende tabel bevat een overzicht op hoog niveau van de belangrijkste JSON-elementen die betrekking hebben op het terugkeer patroon en de planning van een tumblingvenstertriggers-venster trigger:

| JSON-element | Beschrijving | Type | Toegestane waarden | Vereist |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Het type van de trigger. Het type is de vaste waarde ' TumblingWindowTrigger '. | Tekenreeks | "TumblingWindowTrigger" | Ja |
| **runtimeState** | De huidige status van de uitvoerings tijd van de trigger.<br/>**Opmerking**: dit element is \<readOnly> . | Tekenreeks | "Gestart," "gestopt," "uitgeschakeld" | Ja |
| **frequency** | Een teken reeks die de frequentie-eenheid (minuten of uren) aangeeft waarmee de trigger wordt herhaald. Als de **datum waarden** voor de start tijd meer nauw keuriger zijn dan de **frequentie** waarde, worden de datums van de **StartTime** meegenomen wanneer de venster grenzen worden berekend. Als de waarde van de **frequentie** bijvoorbeeld elk uur is en de waarde voor **StartTime** is 2017-09-01T10:10:10Z, is het eerste venster (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Tekenreeks | "minuut," uur "  | Ja |
| **bereik** | Een positief geheel getal dat het interval voor de waarde **frequency** aangeeft. Het bepaalt hoe vaak de trigger wordt uitgevoerd. Als het **interval** bijvoorbeeld 3 is en de **frequentie** is ingesteld op ' uur ', wordt de trigger elke drie uur herhaald. <br/>**Opmerking**: het minimale venster interval is 5 minuten. | Geheel getal | Een positief geheel getal. | Ja |
| **startTime**| De eerste instantie, die in het verleden kan zijn. Het eerste trigger interval is (**StartTime**, **StartTime**  +  -**interval**). | DateTime | Een datum/tijd-waarde. | Ja |
| **Tijd**| De laatste instantie, die in het verleden kan zijn. | DateTime | Een datum/tijd-waarde. | Ja |
| **spoedig** | De hoeveelheid tijd die het starten van de gegevens verwerking voor het venster moet vertragen. De pijplijn uitvoering wordt gestart na de verwachte uitvoerings tijd plus de hoeveelheid **vertraging**. De **vertraging** bepaalt hoe lang de trigger na de eind tijd wacht voordat een nieuwe uitvoering wordt geactiveerd. De **vertraging** wijzigt de **StartTime** van het venster niet. Een **vertragings** waarde van 00:10:00 impliceert bijvoorbeeld een vertraging van 10 minuten. | Periode<br/>(UU: mm: SS)  | Een time span-waarde waarbij de standaard instelling is 00:00:00. | Nee |
| **maxConcurrency** | Het aantal gelijktijdige trigger uitvoeringen dat wordt geactiveerd voor Windows die gereed zijn. Als u bijvoorbeeld per uur back-upvulling voor gisteren wilt uitvoeren, worden er 24 vensters weer gegeven. Als **maxConcurrency** = 10, worden trigger gebeurtenissen alleen geactiveerd voor de eerste 10 windows (00:00-01:00-09:00-10:00). Nadat de eerste tien geactiveerde pijplijn uitvoeringen zijn voltooid, worden de trigger uitvoeringen geactiveerd voor de volgende 10 Windows (10:00-11:00-19:00-20:00). Als u doorgaat met dit voor beeld van **maxConcurrency** = 10, zijn er 10 Windows klaar, dan zijn er 10 totale pijplijn uitvoeringen. Als er slechts één venster gereed is, is er slechts één pijplijn uitvoering. | Geheel getal | Een geheel getal tussen 1 en 50. | Ja |
| **retryPolicy: aantal** | Het aantal nieuwe pogingen voordat de pijplijn uitvoering is gemarkeerd als ' mislukt '.  | Geheel getal | Een geheel getal, waarbij de standaard waarde is 0 (geen nieuwe pogingen). | Nee |
| **retryPolicy: intervalInSeconds** | De vertraging tussen nieuwe pogingen opgegeven in seconden. | Geheel getal | Het aantal seconden, waarbij de standaard waarde is 30. | Nee |
| **dependsOn: type** | Het type TumblingWindowTriggerReference. Vereist als er een afhankelijkheid is ingesteld. | Tekenreeks |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nee |
| **dependsOn: grootte** | De grootte van het tumblingvenstertriggers-venster van de afhankelijkheid. | Periode<br/>(UU: mm: SS)  | Een positieve time span-waarde waarbij de standaard instelling is de venster grootte van de onderliggende trigger  | Nee |
| **dependsOn: offset** | De verschuiving van de afhankelijkheids trigger. | Periode<br/>(UU: mm: SS) |  Een time span-waarde die negatief moet zijn in een self-afhankelijkheid. Als er geen waarde is opgegeven, is het venster hetzelfde als de trigger. | Zelf afhankelijkheid: Ja<br/>Overige: Nee  |

> [!NOTE]
> Nadat de trigger voor een tumblingvenstertriggers-venster is gepubliceerd, kunnen het **interval** en de **frequentie** niet worden bewerkt.

### <a name="windowstart-and-windowend-system-variables"></a>Systeem variabelen WindowStart en WindowEnd

U kunt de systeem variabelen **WindowStart** en **WindowEnd** van de tumblingvenstertriggers-venster trigger gebruiken in uw **pijplijn** definitie (dat wil zeggen, voor een deel van een query). Geef de systeem variabelen op als para meters voor de pijp lijn in de **trigger** definitie. Het volgende voor beeld laat zien hoe u deze variabelen kunt door geven als para meters:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Als u de waarden van de systeem variabelen **WindowStart** en **WindowEnd** in de pijplijn definitie wilt gebruiken, gebruikt u de para meters ' MyWindowStart ' en ' MyWindowEnd ' dienovereenkomstig.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Uitvoerings volgorde van Windows in een backfill-scenario

Als de start tijd van de trigger zich in het verleden bevindt, wordt op basis van deze formule, M = (CurrentTime-TriggerStartTime)/TumblingWindowSize, de trigger een {M}-backfill (in het verleden) gegenereerd, waarna de gelijktijdige uitvoeringen worden uitgevoerd. De volg orde van de uitvoering voor Windows is deterministisch, van oudste naar nieuwste intervallen. Dit gedrag kan op dit moment niet worden aangepast.

### <a name="existing-triggerresource-elements"></a>Bestaande TriggerResource-elementen

De volgende punten zijn van toepassing op het bijwerken van bestaande **TriggerResource** -elementen:

* De waarde voor het **frequentie** -element (of de venster grootte) van de trigger samen met het **interval** element kan niet worden gewijzigd nadat de trigger is gemaakt. Dit is vereist voor een goede werking van triggerRun-reruns en afhankelijkheids evaluaties
* Als de waarde voor het element **EndTime** van de trigger verandert (toegevoegd of bijgewerkt), wordt de status van de Windows die al is verwerkt, *niet* opnieuw ingesteld. De trigger voldoet aan de nieuwe **EndTime** -waarde. Als de nieuwe **EndTime** -waarde voor de Windows is die al wordt uitgevoerd, wordt de trigger gestopt. Anders stopt de trigger wanneer de nieuwe **EndTime** -waarde wordt aangetroffen.

### <a name="user-assigned-retries-of-pipelines"></a>Door de gebruiker toegewezen nieuwe pogingen van pijp lijnen

In het geval van pijp lijn fouten kan de tumblingvenstertriggers-venster trigger automatisch de uitvoering van de pijp lijn waarnaar wordt verwezen, opnieuw proberen, zonder tussen komst van de gebruiker. Dit kan worden opgegeven met de eigenschap ' retryPolicy ' in de trigger definitie.

### <a name="tumbling-window-trigger-dependency"></a>Afhankelijkheid van tumblingvenstertriggers-venster trigger

Als u er zeker van wilt zijn dat een trigger voor een tumblingvenstertriggers-venster wordt uitgevoerd nadat de uitvoering van een andere tumblingvenstertriggers-venster trigger in de data factory is voltooid, [maakt u een tumblingvenstertriggers-venster trigger](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>Het venster tumblingvenstertriggers annuleren uitvoeren

U kunt uitvoeringen voor een tumblingvenstertriggers-venster trigger annuleren als het specifieke venster zich in de _wacht_ stand bevindt, _wacht op afhankelijkheid_ of de status _wordt uitgevoerd_

* Als de status van het venster **actief** is, annuleert u de bijbehorende uitvoering van de _pijp lijn_ en wordt het uitvoeren van de trigger gemarkeerd als _geannuleerd_ .
* Als het venster in **afwachting** is of **wacht op afhankelijkheids** status, kunt u het venster annuleren vanuit bewaking:

![Een trigger voor een tumblingvenstertriggers-venster annuleren vanuit de bewakings pagina](media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png)

U kunt ook een geannuleerd venster opnieuw uitvoeren. Bij het opnieuw uitvoeren worden de _meest recente_ gepubliceerde definities van de trigger uitgevoerd en worden de afhankelijkheden voor het opgegeven venster _opnieuw geëvalueerd_ wanneer het opnieuw wordt uitgevoerd

![Een trigger voor een tumblingvenstertriggers-venster opnieuw uitvoeren voor eerder geannuleerde uitvoeringen](media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png)

## <a name="sample-for-azure-powershell"></a>Voor beeld voor Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze sectie wordt beschreven hoe u Azure PowerShell kunt gebruiken om een trigger te maken, te starten en te bewaken.

1. Maak een JSON-bestand met de naam **MyTrigger.js** in de map C:\ADFv2QuickStartPSH\ met de volgende inhoud:

    > [!IMPORTANT]
    > Voordat u het JSON-bestand opslaat, stelt u de waarde van het element **StartTime** in op de huidige UTC-tijd. Stel de waarde van het element **EndTime** in op één uur na de huidige UTC-tijd.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Een trigger maken met behulp van de cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Controleer of de status van de trigger is **gestopt** met behulp van de cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Start de trigger met behulp van de cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Controleer of de status van de trigger is **gestart** met behulp van de cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. De trigger wordt uitgevoerd in Azure PowerShell met behulp van de cmdlet **Get-AzDataFactoryV2TriggerRun** . Voer regel matig de volgende opdracht uit om informatie te krijgen over de uitvoeringen van triggers. Werk de waarden **TriggerRunStartedAfter** en **TriggerRunStartedBefore** bij zodat deze overeenkomen met de waarden in de trigger definitie:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

Zie [pijplijn uitvoeringen controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)als u de uitvoering van triggers en pijplijn uitvoeringen wilt bewaken in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor meer informatie over triggers.
* [Maak een tumblingvenstertriggers-venster trigger-afhankelijkheid](tumbling-window-trigger-dependency.md).
* Meer informatie over het verwijzen naar trigger-meta gegevens in de pijp lijn. Zie [Naslag informatie voor triggers in pijplijn uitvoeringen](how-to-use-trigger-parameterization.md)
