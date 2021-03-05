---
title: Schema triggers maken in Azure Data Factory
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijp lijn uitvoert volgens een schema.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: f10dac4e70a1edb05f2f2c02c48b9ae16c4f6823
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177807"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Een trigger maken voor het uitvoeren van een pijplijn volgens een planning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dit artikel bevat informatie over de plannings trigger en de stappen voor het maken, starten en bewaken van een plannings trigger. Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md)voor andere soorten triggers.

Wanneer u een plannings trigger maakt, geeft u een planning op (Start datum, terugkeer patroon, eind datum, enzovoort) voor de trigger en koppelt u deze aan een pijp lijn. Pijplijnen en triggers hebben een veel-op-veel-relatie. Meerdere triggers kunnen één pijplijn activeren. Eén trigger kan meerdere pijplijnen activeren.

De volgende secties bevatten stappen voor het maken van een plannings trigger op verschillende manieren. 

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

U kunt een **schema trigger** maken om een pijp lijn te plannen die regel matig wordt uitgevoerd (elk uur, dagelijks, enzovoort). 

> [!NOTE]
> Voor een volledig overzicht van het maken van een pijp lijn en een plannings trigger, waarmee de trigger wordt gekoppeld aan de pijp lijn en de pijp lijn wordt uitgevoerd en bewaakt, raadpleegt [u Quick Start: een Data Factory maken met behulp van Data Factory-gebruikers interface](quickstart-create-data-factory-portal.md).

1. Schakel over naar het tabblad **bewerken** , weer gegeven met een potlood symbool. 

    ![Naar het tabblad Bewerken gaan](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Selecteer **activeren** in het menu en selecteer vervolgens **Nieuw/bewerken**. 

    ![Menu Nieuwe trigger](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. Selecteer op de pagina **triggers toevoegen** de optie **trigger kiezen...** en selecteer **+ Nieuw**. 

    ![Triggers toevoegen - nieuwe trigger](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Voer op de pagina **nieuwe trigger** de volgende stappen uit: 

    1. Controleer of het **schema** is geselecteerd voor het **type**.
    1. Geef de begin datum/tijd van de trigger op voor de **Start** tijd. Deze wordt standaard ingesteld op de huidige datum/tijd in Coordinated Universal Time (UTC).
    1. Geef de tijd zone op waarin de trigger wordt gemaakt. De tijd zone-instelling is van toepassing op de **begin datum**, **eind datum** en het **plannen van uitvoerings tijden** in geavanceerde terugkeer opties. Als u de instelling voor de tijd zone wijzigt, wordt de begin datum niet automatisch gewijzigd. Zorg ervoor dat de begin datum in de opgegeven tijd zone juist is. Houd er rekening mee dat de geplande uitvoerings tijd van de trigger wordt beschouwd als de begin datum (Controleer of de begin datum ten minste 1minute lager is dan de uitvoerings tijd, anders wordt de pijp lijn geactiveerd tijdens het volgende terugkeer patroon). 

        > [!NOTE]
        > Voor tijd zones die in de zomer-en winter tijd worden geobserveerd, wordt het tijdstip waarop de activering wordt uitgevoerd, automatisch aangepast. Als u zich wilt afmelden bij de zomer-en winter wijzigingen, selecteert u een tijd zone die niet wordt ondersteund voor zomer-en winter-opslag, bijvoorbeeld UTC

    1. Geef het **terugkeer patroon** voor de trigger op. Selecteer een van de waarden in de vervolg keuzelijst (elke minuut, elk uur, dagelijks, wekelijks en maandelijks). Voer de vermenigvuldiger in het tekstvak in. Bijvoorbeeld, als u wilt dat de trigger elke 15 minuten eenmaal wordt uitgevoerd, selecteert u **elke minuut** en voert u **15** in het tekstvak in. 
    1. Als u een eind datum wilt opgeven, selecteert u **een eind datum opgeven** en geeft u _eindigt op op_. Selecteer vervolgens **OK**. Er zijn kosten gekoppeld aan elke uitvoering van de pijplijn. Als u wilt testen, kunt u ervoor zorgen dat de pijp lijn slechts een paar keer wordt geactiveerd. Zorg er echter wel voor dat er voldoende tijd is om de pijplijn uit te voeren tussen de publicatietijd en de eindtijd. De trigger gaat pas van kracht nadat u de oplossing hebt gepubliceerd in Data Factory, niet wanneer u de trigger opslaat in de UI.

        ![Triggerinstellingen](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Trigger instellingen voor eind datum](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. Selecteer in het venster **nieuwe trigger** **Ja** in de optie **geactiveerd** en selecteer vervolgens **OK**. U kunt dit selectie vakje gebruiken om de trigger later te deactiveren. 

    ![Triggerinstellingen - knop Volgende](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. Bekijk in het venster **nieuwe trigger** het waarschuwings bericht en selecteer vervolgens **OK**.

    ![Triggerinstellingen - knop Voltooien](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Selecteer **Alles publiceren** om de wijzigingen te publiceren naar Data Factory. Totdat u de wijzigingen naar Data Factory publiceert, start de trigger niet de activering van de pijp lijn. 

    ![De knop Publiceren](./media/how-to-create-schedule-trigger/publish-2.png)

1. Ga naar het tabblad **pijplijn uitvoeringen** aan de linkerkant en selecteer vervolgens **vernieuwen** om de lijst te vernieuwen. U ziet de pijplijn uitvoeringen die worden geactiveerd door de geplande trigger. Let op de waarden in de kolom **geactiveerd door** . Als u de optie **nu activeren** gebruikt, ziet u de hand matige trigger wordt uitgevoerd in de lijst. 

    ![Geactiveerde uitvoeringen controleren](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Schakel over naar de **trigger**  \  **plannings** weergave. 

    ![Triggeruitvoeringen controleren](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze sectie wordt beschreven hoe u Azure PowerShell kunt gebruiken om een plannings trigger te maken, te starten en te bewaken. Als u dit voor beeld wilt weer geven, gaat u eerst naar de [Snelstartgids: een Data Factory maken met behulp van Azure PowerShell](quickstart-create-data-factory-powershell.md). Voeg vervolgens de volgende code toe aan de methode Main, waarmee een plannings trigger wordt gemaakt en gestart die elke 15 minuten wordt uitgevoerd. De trigger is gekoppeld aan een pijp lijn met de naam **Adfv2QuickStartPipeline** die u maakt als onderdeel van de Quick Start.

1. Maak een JSON-bestand met de naam **MyTrigger.js** in de map C:\ADFv2QuickStartPSH\ met de volgende inhoud:

    > [!IMPORTANT]
    > Voordat u het JSON-bestand opslaat, stelt u de waarde van het element **StartTime** in op de huidige UTC-tijd. Stel de waarde van het element **EndTime** in op één uur na de huidige UTC-tijd.

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    In het JSON-code fragment:
    - Het element **type** van de trigger is ingesteld op ' ScheduleTrigger '.
    - Het element **Frequency** wordt ingesteld op ' minuut ' en het **interval** element is ingesteld op 15. Als zodanig voert de trigger de pijp lijn elke 15 minuten tussen de begin-en eind tijd.
    - Het element **time** zone specificeert de tijd zones waarin de trigger is gemaakt. Deze instelling is van invloed op **StartTime** en **EndTime**.
    - Het element **EndTime** is één uur na de waarde van het element **StartTime** . Als zodanig voert de trigger de pijp lijn 15 minuten, 30 minuten en 45 minuten na de begin tijd. Vergeet niet om de begin tijd bij te werken naar de huidige UTC-tijd en de eind tijd op één uur na de begin tijd. 

        > [!IMPORTANT]
        > Voor UTC-tijd zone moet de startTime en endTime de notatie ' JJJJ-MM-DDTuu: mm: SS **Z**' hebben, terwijl voor andere tijd zones, StartTime en EndTime ' jjjj-mm-ddTuu: mm: SS ' volgen. 
        > 
        > Per ISO 8601-standaard, het _Z_ -achtervoegsel van de tijds tempel Markeer de datum/tijd in UTC-tijd zone en het veld tijd zone weer geven onbruikbaar. Hoewel er geen _Z_ -ACHTERVOEGSEL voor UTC-tijd zone ontbreekt, resulteert dit in een fout tijdens de _Activering_ van de trigger.

    - De trigger is gekoppeld aan de **Adfv2QuickStartPipeline** -pijp lijn. Voeg meer **pipelineReference** -secties toe om meerdere pijp lijnen aan een trigger te koppelen.
    - De pijp lijn in de Quick Start heeft twee **parameter** waarden: **inputPath** en **outputPath**. En u geeft waarden voor deze para meters door vanuit de trigger.

1. Een trigger maken met behulp van de cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Controleer of de status van de trigger is **gestopt** met behulp van de cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Start de trigger met behulp van de cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Controleer of de status van de trigger is **gestart** met behulp van de cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  De trigger wordt uitgevoerd in Azure PowerShell met behulp van de cmdlet **Get-AzDataFactoryV2TriggerRun** . Voer regel matig de volgende opdracht uit om de informatie over de triggers-uitvoeringen weer te geven. Werk de waarden **TriggerRunStartedAfter** en **TriggerRunStartedBefore** bij zodat deze overeenkomen met de waarden in de trigger definitie:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Trigger tijd van schema-triggers worden opgegeven in UTC-tijds tempel. _TriggerRunStartedAfter_ en _TRIGGERRUNSTARTEDBEFORE_ verwacht ook UTC-tijds tempel

    Zie [pijplijn uitvoeringen controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)als u de uitvoering van triggers en pijplijn uitvoeringen wilt bewaken in de Azure Portal.

## <a name="net-sdk"></a>.NET SDK

In deze sectie wordt beschreven hoe u de .NET SDK gebruikt om een trigger te maken, te starten en te bewaken. Als u dit voor beeld wilt weer geven, gaat u eerst naar de [Snelstartgids: een Data Factory maken met behulp van de .NET SDK](quickstart-create-data-factory-dot-net.md). Voeg vervolgens de volgende code toe aan de methode Main, waarmee een plannings trigger wordt gemaakt en gestart die elke 15 minuten wordt uitgevoerd. De trigger is gekoppeld aan een pijp lijn met de naam **Adfv2QuickStartPipeline** die u maakt als onderdeel van de Quick Start.

Als u een plannings trigger wilt maken en starten die elke 15 minuten wordt uitgevoerd, voegt u de volgende code toe aan de methode Main:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Als u triggers wilt maken in een andere tijd zone dan UTC, zijn de volgende instellingen vereist:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Als u de uitvoering van een trigger wilt bewaken, voegt u de volgende code toe vóór de laatste `Console.WriteLine` instructie in het voor beeld:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Zie [pijplijn uitvoeringen controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)als u de uitvoering van triggers en pijplijn uitvoeringen wilt bewaken in de Azure Portal.

## <a name="python-sdk"></a>Python-SDK

In deze sectie wordt beschreven hoe u de python-SDK gebruikt om een trigger te maken, te starten en te bewaken. Als u dit voor beeld wilt weer geven, gaat u eerst naar de [Snelstartgids: een Data Factory maken met behulp van de python-SDK](quickstart-create-data-factory-python.md). Voeg vervolgens het volgende code blok toe na het code blok van de pijplijn uitvoering bewaken in het python-script. Met deze code wordt een schema trigger gemaakt die elke 15 minuten tussen de opgegeven begin-en eind tijd wordt uitgevoerd. Werk de variabele **start_time** bij naar de huidige UTC-tijd en de **end_time** variabele tot één uur na de huidige UTC-tijd.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Zie [pijplijn uitvoeringen controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)als u de uitvoering van triggers en pijplijn uitvoeringen wilt bewaken in de Azure Portal.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

U kunt een Azure Resource Manager-sjabloon gebruiken om een trigger te maken. Zie [een Azure-Data Factory maken met behulp van een resource manager-sjabloon](quickstart-create-data-factory-resource-manager-template.md)voor stapsgewijze instructies.  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Start tijd van trigger door geven aan een pijp lijn

Azure Data Factory versie 1 ondersteunt het lezen of schrijven van gepartitioneerde gegevens met behulp van de systeem variabelen: **slice start**, **SliceEnd**, **WindowStart** en **WindowEnd**. In de huidige versie van Azure Data Factory kunt u dit gedrag doen met behulp van een pijplijn parameter. De begin tijd en de geplande tijd voor de trigger worden ingesteld als de waarde voor de pijplijn parameter. In het volgende voor beeld wordt de geplande tijd voor de trigger door gegeven als een waarde voor de pijplijn **scheduledRunTime** para meter:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-schema

De volgende JSON-definitie laat zien hoe u een plannings trigger maakt met planning en terugkeer patroon:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  De eigenschap **parameters** is een verplichte eigenschap van het element **pijplijnen**. Als de pijplijn geen parameters aanneemt, dient u een lege JSON-definitie op te nemen voor de eigenschap **parameters**.


### <a name="schema-overview"></a>Schema-overzicht

De volgende tabel bevat een overzicht van de belangrijkste schema-elementen die betrekking hebben op het terugkeerpatroon en het schema van een trigger:

| JSON-eigenschap | Beschrijving |
|:--- |:--- |
| **startTime** | Een datum/tijdwaarde. Voor eenvoudige schema's is de waarde **startTime** van toepassing op de eerste gebeurtenis. In complexe schema's begint de trigger niet eerder dan de opgegeven waarde voor **startTime**. <br> De notatie voor UTC-tijd zone is `'yyyy-MM-ddTHH:mm:ssZ'` voor een andere tijd zone `'yyyy-MM-ddTHH:mm:ss'` . |
| **Tijd** | De einddatum en -tijd voor de trigger. De trigger wordt na de opgegeven einddatum en -tijd niet uitgevoerd. De waarde voor de eigenschap kan niet in het verleden liggen. Deze eigenschap is optioneel.  <br> De notatie voor UTC-tijd zone is `'yyyy-MM-ddTHH:mm:ssZ'` voor een andere tijd zone `'yyyy-MM-ddTHH:mm:ss'` . |
| **Tijd zone** | De tijd zone waarin de trigger wordt gemaakt. Deze instelling heeft invloed op **StartTime**, **EndTime** en **Schedule**. [Lijst met ondersteunde tijd zones](#time-zone-option) weer geven |
| **optreden** | Een recurrence-object bepaalt de regels voor het terugkeerpatroon van de trigger. Het recurrence-object ondersteunt de elementen **frequency**, **interval**, **endTime**, **count** en **schedule**. Als een recurrence-object wordt gedefinieerd, is het element **frequency** vereist. De overige elementen van het recurrence-object zijn optioneel. |
| **frequency** | Hiermee geeft u de frequentie aan waarmee de trigger wordt uitgevoerd. De ondersteunde waarden omvatten 'minuut', 'uur', 'dag', 'week' en 'maand'. |
| **bereik** | Een positief geheel getal dat het interval voor de waarde **frequency** aangeeft. Het bepaalt hoe vaak de trigger wordt uitgevoerd. Als **interval** bijvoorbeeld 3 is en **frequency** 'week', dan wordt de trigger elke 3 weken uitgevoerd. |
| **planning** | Het terugkeerschema voor de trigger. Een trigger met een opgegeven waarde voor **frequency** wijzigt het terugkeerpatroon op basis van een terugkeerschema. De eigenschap **property** bevat wijzigingen voor het terugkeerpatroon en zijn gebaseerd op minuten, uren, weekdagen, maanddagen en weeknummer.

> [!IMPORTANT]
> Voor UTC-tijd zone moet de startTime en endTime de notatie ' JJJJ-MM-DDTuu: mm: SS **Z**' hebben, terwijl voor andere tijd zones, StartTime en EndTime ' jjjj-mm-ddTuu: mm: SS ' volgen. 
> 
> Per ISO 8601-standaard, het _Z_ -achtervoegsel van de tijds tempel Markeer de datum/tijd in UTC-tijd zone en het veld tijd zone weer geven onbruikbaar. Hoewel er geen _Z_ -ACHTERVOEGSEL voor UTC-tijd zone ontbreekt, resulteert dit in een fout tijdens de _Activering_ van de trigger.

### <a name="schema-defaults-limits-and-examples"></a>Standaardschemawaarden, limieten en voorbeelden

| JSON-eigenschap | Type | Vereist | Standaardwaarde | Geldige waarden | Voorbeeld |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Tekenreeks | Ja | Geen | Datums en tijden volgens ISO 8601 | voor UTC-tijd zone `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> voor een andere tijd zone `"2013-01-09T09:30:00-08:00"` |
| **Tijd zone** | Tekenreeks | Ja | Geen | [Waarden van tijd zone](#time-zone-option)  | `"UTC"` |
| **optreden** | Object | Ja | Geen | Recurrence-object | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **bereik** | Aantal | Nee | 1 | 1 tot 1000 | `"interval":10` |
| **Tijd** | Tekenreeks | Ja | Geen | Een datum/tijdwaarde die een toekomstig tijdstip voorstelt. | voor UTC-tijd zone `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> voor een andere tijd zone `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **planning** | Object | Nee | Geen | Schedule-object | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Optie tijd zone

Hier volgen enkele tijd zones die worden ondersteund voor scheduler-triggers:

| Tijdzone | UTC-afwijking (niet-zomer-en winter tijd) | Waarde van tijd zone | Observeren met zomer-en winter tijd | Notatie van tijds tempel |
| :--- | :--- | :--- | :--- | :--- |
| UTC (Coordinated Universal Time) | 0 | `UTC` | Nee | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Pacific Time (PT) | -8 | `Pacific Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Central Time (CT) | -6 | `Central Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Eastern Time (ET) | 5 | `Eastern Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwich Mean Time (GMT) | 0 | `GMT Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Centraal-Europa (standaard tijd) | +1 | `W. Europe Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| India (standaard tijd) (IST) | + 5:30 | `India Standard Time` | Nee | `'yyyy-MM-ddTHH:mm:ss'` |
| China (standaard tijd) | + 8 | `China Standard Time` | Nee | `'yyyy-MM-ddTHH:mm:ss'` |

Deze lijst is niet volledig. Voor een volledige lijst met opties voor de tijd zone bekijkt u in Data Factory Portal [activeren pagina maken](#data-factory-ui)

### <a name="starttime-property"></a>Eigenschap startTime
In de volgende tabel ziet u hoe de eigenschap **startTime** de uitvoering van een trigger bepaalt:

| startTime-waarde | Recurrence zonder schedule | Recurrence met schedule |
|:--- |:--- |:--- |
| Starttijd in het verleden | Berekent de eerstvolgende uitvoering na de starttijd en voert deze op dat moment uit.<br/><br/>Voert volgende uitvoeringen uit op basis van de tijd waarop de laatste uitvoering heeft plaatsgevonden.<br/><br/>Zie het voorbeeld onder deze tabel. | De trigger wordt _nooit vóór_ de opgegeven begintijd geactiveerd. De eerste uitvoering is gebaseerd op het schema dat wordt berekend op basis van de starttijd.<br/><br/>Volgende uitvoeringen worden op basis van het terugkeerschema uitgevoerd. |
| De starttijd ligt in de toekomst, of op dit moment | Uitvoering vindt eenmaal plaats op de opgegeven starttijd.<br/><br/>Voert volgende uitvoeringen uit op basis van de tijd waarop de laatste uitvoering heeft plaatsgevonden. | De trigger wordt _niet eerder_ gestart dan de opgegeven begin tijd. De eerste uitvoering is gebaseerd op het schema dat wordt berekend op basis van de starttijd.<br/><br/>Volgende uitvoeringen worden op basis van het terugkeerschema uitgevoerd. |

We bekijken een voorbeeld van wat er gebeurt wanneer de startTime in het verleden ligt en er een terugkeerpatroon (recurrence), maar geen schema (schedule) is opgegeven. Neem aan dat de huidige tijd `2017-04-08 13:00` is, de starttijd `2017-04-07 14:00` en het terugkeerpatroon om de dag. (De waarde voor het **terugkeer patroon** wordt gedefinieerd door de eigenschap **Frequency** in te stellen op ' dag ' en de eigenschap **interval** op 2.) U ziet dat de waarde voor **StartTime** in het verleden ligt en vóór de huidige tijd plaatsvindt.

Onder deze omstandigheden bevindt de eerste uitvoering zich op `2017-04-09` om `14:00` . De scheduler-engine berekent uitvoeringen vanaf de startTime. Alle uitvoeringen in het verleden worden genegeerd. De engine gebruikt de eerstvolgende uitvoering die in de toekomst plaatsvindt. In dit scenario is de start tijd `2017-04-07` op `2:00pm` . Daarom is de volgende instantie twee dagen vanaf die tijd, die is `2017-04-09` op `2:00pm` .

De eerste uitvoeringstijd is dezelfde, ook als **startTime**`2017-04-05 14:00` of `2017-04-01 14:00` is. Na de eerste uitvoering worden volgende uitvoeringen berekend met behulp van het schema. De volgende uitvoeringen zijn daarom op `2017-04-11` om `2:00pm` , vervolgens `2017-04-13` op, `2:00pm` vervolgens op `2017-04-15` `2:00pm` , enzovoort.

Als de uren of minuten niet in het schema voor een trigger zijn ingesteld, worden deze waarden van de eerste uitvoering als standaardwaarden gebruikt.

### <a name="schedule-property"></a>Eigenschap schedule

Enerzijds kan met een schema (schedule) het aantal uitvoeringen door een trigger worden beperkt. Als een trigger met de frequency 'maand' bijvoorbeeld een schedule-waarde heeft die alleen wordt uitgevoerd op dag 31, wordt de trigger alleen uitgevoerd in maanden die een 31e dag hebben.

Anderzijds kan een schedule het aantal uitvoeringen door een trigger ook uitbreiden. Bijvoorbeeld: een trigger met een geplande maandfrequentie voor uitvoering op de maanddagen 1 en 2, wordt uitgevoerd op de eerste en tweede dag van de maand, in plaats van eenmaal per maand.

Als meerdere **schedule**-elementen worden opgegeven, is de volgorde voor de evaluatie van de hoogste naar de laagste instelling voor de waarde schedule. De evaluatie begint met weeknummer, waarna maanddag, weekdag, uur en minuut volgen.

In de volgende tabel worden de **schedule**-elementen in detail beschreven:

| JSON-element | Beschrijving | Geldige waarden |
|:--- |:--- |:--- |
| **wachten** | Minuten van het uur waarop de trigger wordt uitgevoerd. | <ul><li>Geheel getal</li><li>Matrix van gehele getallen</li></ul>
| **loopt** | Uren van de dag waarop de trigger wordt uitgevoerd. | <ul><li>Geheel getal</li><li>Matrix van gehele getallen</li></ul> |
| **weekDays** | Dagen van de week waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een weekfrequentie. | <ul><li>Maandag, dinsdag, woensdag, donderdag, vrijdag, zaterdag, zondag</li><li>Array met dagwaarden (maximale grootte van de array is 7)</li><li>Dagwaarden zijn niet hoofdlettergevoelig</li></ul> |
| **monthlyOccurrences** | Dagen van de maand waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een maandfrequentie. | <ul><li>Matrix van **monthlyOccurrence** -objecten: `{ "day": day,  "occurrence": occurrence }` .</li><li>Het attribuut **day** is de dag van de week waarop de trigger wordt uitgevoerd. Zo betekent de eigenschap **monthlyOccurrences** met een waarde **day** van `{Sunday}` dat er elke zondag van de maand een uitvoering is. Het attribuut **day** is verplicht.</li><li>Het attribuut **occurrence** slaat op het uitvoeren van de trigger op de opgegeven dag, **day**, tijdens de maand. Zo betekent de eigenschap **monthlyOccurrences** met de waarden **day** en **occurrence** van `{Sunday, -1}` dat er elke laatste zondag van de maand een uitvoering is. Het attribuut **occurrence** is optioneel.</li></ul> |
| **monthDays** | Dagen van de maand waarop de trigger wordt uitgevoerd. De waarde kan alleen worden opgegeven met een maandfrequentie. | <ul><li>Alle waarden < = -1 en > =-31</li><li>Alle waarden > = -1 en < =-31</li><li>Array met waarden</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Voorbeelden van schema's voor uitvoeringen van triggers

Deze sectie bevat voorbeelden van terugkeerschema's en is gericht op het object **schedule** en de bijbehorende elementen.

In het voorbeeld wordt ervan uitgegaan dat de waarde **interval** 1 is en de waarde **frequency** correct is volgens de definitie van het schema. U kunt bijvoorbeeld niet **de waarde '** Day ' hebben en ook een ' monthDays-wijziging hebben in het object **Schedule** . Dergelijke beperkingen staan vermeld in de tabel in de vorige sectie.

| Voorbeeld | Beschrijving |
|:--- |:--- |
| `{"hours":[5]}` | Wordt elke dag om 5:00 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5]}` | Wordt elke dag om 5:15 uur uitgevoerd. |
| `{"minutes":[15], "hours":[5,17]}` | Wordt elke dag om 05:15 en 17:15 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17]}` | Wordt elke dag om 05:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45]}` | Wordt elke 15 minuten uitgevoerd. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Wordt elk uur uitgevoerd. Deze trigger wordt elk uur uitgevoerd. De minuten worden door de waarde **startTime** bepaald (indien opgegeven). Als er geen waarde is opgegeven, worden de minuten bepaald door de aanmaaktijd. Als de starttijd of aanmaaktijd (afhankelijk van wat van toepassing is) bijvoorbeeld 12:25 uur is, wordt de trigger uitgevoerd om 00:25, 01:25, 02:25, ..., en 23:25 uur.<br/><br/>Dit schema komt overeen met een trigger met de **frequentie** waarde ' hour ', een **interval** waarde van 1 en geen **schema**.  Dit schema kan worden gebruikt met verschillende waarden voor **frequency** en **interval** om andere triggers te maken. Als de **frequentie** waarde bijvoorbeeld ' maand ' is, wordt de planning slechts eenmaal per maand uitgevoerd, in plaats van elke dag, wanneer de waarde van de **frequentie** ' dag ' is. |
| `{"minutes":[0]}` | Wordt elk uur op het hele uur uitgevoerd. Deze trigger wordt elk uur op het hele uur uitgevoerd, te beginnen om 00:00 uur en vervolgens om 1:00 uur, 2:00 uur enzovoort.<br/><br/>Dit schema is gelijkwaardig met een trigger met **frequency** 'uur' en **startTime** nul minuten, of met **schedule** zonder waarde en **frequency** 'dag'. Als de waarde voor **Frequency** ' week ' of ' maand ' is, wordt het schema respectievelijk één dag per week of één dag per maand uitgevoerd. |
| `{"minutes":[15]}` | Wordt 15 minuten na elk uur uitgevoerd. Deze trigger wordt elke 15 minuten na het hele uur uitgevoerd, te beginnen om 00:15 uur, en vervolgens om 1:15 uur, 2:15 uur, met de laatste uitvoering om 23:15 uur. |
| `{"hours":[17], "weekDays":["saturday"]}` | Wordt elke week op zaterdag om 17:00 uur uitgevoerd. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wordt elke week op maandag, woensdag en vrijdag om 17:00 uur uitgevoerd. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Wordt elke week op maandag, woensdag en vrijdag om 17:15 en 17:45 uur uitgevoerd. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Wordt op weekdagen elke 15 minuten uitgevoerd. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Wordt op weekdagen elke 15 minuten tussen 9:00 en 16:45 uur uitgevoerd. |
| `{"weekDays":["tuesday", "thursday"]}` | Wordt op dinsdag en donderdag op de opgegeven begintijd uitgevoerd. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Wordt op dag 28 van elke maand om 6:00 uur uitgevoerd (bij een waarde voor **frequency** van 'maand'). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Wordt op de laatste dag van de maand om 6:00 uur uitgevoerd. Als u een trigger wilt uitvoeren op de laatste dag van een maand, gebruik dan -1 in plaats van dag 28, 29, 30 of 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Wordt op de eerste en laatste dag van elke maand om 6:00 uur uitgevoerd. |
| `{monthDays":[1,14]}` | Wordt op de eerste en veertiende dag van elke maand op de opgegeven begintijd uitgevoerd. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Wordt op de eerste vrijdagdag van elke maand om 5:00 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Wordt op de eerste vrijdag van elke maand op de opgegeven begintijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Wordt op de derde vrijdag vanaf het eind van de maand elke maand op de opgegeven begintijd uitgevoerd. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Wordt op de eerste en laatste vrijdagdag van elke maand om 5:15 uur uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Wordt op de eerste en laatste vrijdag van elke maand op de opgegeven begintijd uitgevoerd. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Wordt op de vijfde vrijdag van elke maand op de opgegeven begintijd uitgevoerd. Als een maand geen vijfde vrijdag heeft, wordt de pijplijn niet uitgevoerd, omdat deze is ingesteld om alleen op de vijfde vrijdag te worden uitgevoerd. Als u de trigger op de laatste vrijdag van de maand wilt uitvoeren, kunt u voor **occurrence** de waarde -1 gebruiken in plaats van 5. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Wordt op de laatste vrijdag van de maand elke 15 minuten uitgevoerd. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Wordt elke maand op de derde woensdag om 5:15, 5:45, 17:15 en 17:45 uur uitgevoerd. |

## <a name="next-steps"></a>Volgende stappen

- Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor meer informatie over triggers.
- Meer informatie over het verwijzen naar trigger-meta gegevens in de pijp lijn. Zie [Naslag informatie voor triggers in pijplijn uitvoeringen](how-to-use-trigger-parameterization.md)
