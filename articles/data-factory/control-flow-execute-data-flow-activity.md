---
title: Gegevensstroom activiteit
description: Gegevensstromen uitvoeren vanuit een data factory pijplijn.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/16/2021
ms.openlocfilehash: da8d193d140d96d9742666429ebc85672c71ad4e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567261"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Gegevensstroom activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de Gegevensstroom om gegevens te transformeren en te verplaatsen via toewijzingsgegevensstromen. Als u geen tijd hebt voor gegevensstromen, zie [Toewijzingsoverzicht Gegevensstroom](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Typeeigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
gegevensstroom | De verwijzing naar de Gegevensstroom wordt uitgevoerd | DataFlowReference | Ja
integrationRuntime | De rekenomgeving waarin de gegevensstroom wordt uitgevoerd. Als dit niet wordt opgegeven, wordt de Azure Integration Runtime automatisch oplossen gebruikt. | IntegrationRuntimeReference | Nee
compute.coreCount | Het aantal kernen dat wordt gebruikt in het Spark-cluster. Kan alleen worden opgegeven als de Azure Integration Runtime automatisch wordt opgelost | 8, 16, 32, 48, 80, 144, 272 | Nee
compute.computeType | Het type rekenkracht dat wordt gebruikt in het Spark-cluster. Kan alleen worden opgegeven als de Azure Integration Runtime automatisch wordt opgelost | "Algemeen", "ComputeOptimized", "MemoryOptimized" | Nee
staging.linkedService | Als u een bron of sink Azure Synapse Analytics, geeft u het opslagaccount op dat wordt gebruikt voor polybase-fasering.<br/><br/>Als uw Azure Storage is geconfigureerd met VNet-service-eindpunt, moet u verificatie van beheerde identiteiten gebruiken met 'vertrouwde Microsoft-service toestaan' ingeschakeld voor het opslagaccount. Raadpleeg Impact van het gebruik van [VNet-service-eindpunten](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)met Azure Storage. Leer ook de benodigde configuraties [voor respectievelijk Azure Blob](connector-azure-blob-storage.md#managed-identity) [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) configureren.<br/> | LinkedServiceReference | Alleen als de gegevensstroom naar een Azure Synapse Analytics
staging.folderPath | Als u een bron of sink Azure Synapse Analytics, het mappad in het Blob Storage-account dat wordt gebruikt voor fasering van PolyBase | Tekenreeks | Alleen als de gegevensstroom naar de Azure Synapse Analytics
traceLevel | Het logboekregistratieniveau van de uitvoering van uw gegevensstroomactiviteit instellen | Fine, Coarse, None | Nee

![Voer Gegevensstroom](media/data-flow/activity-data-flow.png "Voer Gegevensstroom")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamisch gegevensstroom berekenen tijdens runtime

De eigenschappen Core Count en Compute Type kunnen dynamisch worden ingesteld om aan te passen aan de grootte van uw binnenkomende brongegevens tijdens runtime. Gebruik pijplijnactiviteiten zoals Opzoek of Metagegevens op halen om de grootte van de gegevens van de brongegevensset te vinden. Gebruik vervolgens Dynamische inhoud toevoegen in de eigenschappen van Gegevensstroom-activiteit.

![Dynamische Gegevensstroom](media/data-flow/dyna1.png "Dynamische gegevensstroom")

[Hier is een korte videozelfstudie waarin deze techniek wordt uitgelegd](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Gegevensstroom Integration Runtime

Kies welke Integration Runtime u wilt gebruiken voor de uitvoering van Gegevensstroom activiteit. Standaard gebruikt Data Factory Azure Integration Runtime automatisch oplossen met vier werkkernen. Deze IR heeft een algemeen rekentype en wordt uitgevoerd in dezelfde regio als uw factory. Voor operationele pijplijnen is het raadzaam om uw eigen Azure Integration Runtimes te maken die specifieke regio's, rekentype, kerntellingen en TTL definiëren voor het uitvoeren van uw gegevensstroomactiviteit.

Een minimaal rekentype van Algemeen (geoptimaliseerd voor rekenkracht wordt niet aanbevolen voor grote workloads) met een configuratie van 8+8 (in totaal 16 v-cores) en een 10-minuut is de minimale aanbeveling voor de meeste productieworkloads. Door een kleine TTL in te stellen, Azure IR een warm cluster onderhouden dat niet de enkele minuten van de starttijd voor een koud cluster in gebruik neemt. U kunt de uitvoering van uw gegevensstromen nog sneller uitvoeren door 'Snel opnieuw gebruiken' te selecteren in de configuraties Azure IR gegevensstroom. Zie Azure Integration [Runtime voor meer informatie.](concepts-integration-runtime.md)

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> De Integration Runtime in de Gegevensstroom is alleen van toepassing op *geactiveerde uitvoeringen* van uw pijplijn. Het opsporen van fouten in uw pijplijn met gegevensstromen wordt uitgevoerd op het cluster dat is opgegeven in de foutopsporingssessie.

### <a name="polybase"></a>PolyBase

Als u een testlocatie gebruikt Azure Synapse Analytics sink of bron, moet u een faseringslocatie kiezen voor de batchbelasting van PolyBase. Met PolyBase kunt u batchgewijs laden in plaats van de gegevens rij voor rij te laden. PolyBase vermindert de laadtijd in de Azure Synapse Analytics.

## <a name="logging-level"></a>Niveau van logboekregistratie

Als u niet elke pijplijnuitvoering van uw gegevensstroomactiviteiten nodig hebt om alle uitgebreide telemetrielogboeken volledig te registreren, kunt u eventueel uw logboekregistratieniveau instellen op 'Basic' of 'Geen'. Bij het uitvoeren van uw gegevensstromen in de modus 'Uitgebreid' (standaard) vraagt u ADF om activiteiten op elk afzonderlijk partitieniveau volledig vast te stellen tijdens uw gegevenstransformatie. Dit kan een dure bewerking zijn, zodat u uw algehele gegevensstroom en pijplijnprestaties alleen kunt verbeteren door uitgebreide problemen op te lossen. De modus Basic legt alleen de duur van transformaties in een logboek bij, terwijl 'Geen' alleen een samenvatting van de duur biedt.

![Niveau van logboekregistratie](media/data-flow/logging.png "Logboekregistratieniveau instellen")

## <a name="sink-properties"></a>Sinkeigenschappen

Met de groeperingsfunctie in gegevensstromen kunt u zowel de volgorde van uitvoering van uw sinks instellen als sinks groeperen met hetzelfde groepsnummer. Als u wilt helpen bij het beheren van groepen, kunt u ADF vragen om sinks parallel in dezelfde groep uit te voeren. U kunt de sinkgroep ook instellen om door te gaan, zelfs nadat een van de sinks een fout heeft aangetroffen.

Het standaardgedrag van gegevensstroom-sinks is om elke sink sequentiële wijze uit te voeren en de gegevensstroom te laten mislukken wanneer er een fout in de sink wordt aangetroffen. Bovendien worden alle sinks standaard ingesteld op dezelfde groep, tenzij u naar de eigenschappen van de gegevensstroom gaat en verschillende prioriteiten voor de sinks in stelt.

![Sinkeigenschappen](media/data-flow/sink-properties.png "Sink-eigenschappen instellen")

## <a name="parameterizing-data-flows"></a>Gegevensstromen parameteriseren

### <a name="parameterized-datasets"></a>Geparameteriseerde gegevenssets

Als uw gegevensstroom gebruikmaakt van geparameteriseerde gegevenssets, stelt u de parameterwaarden in op het **tabblad** Instellingen.

![Parameters Gegevensstroom uitvoeren](media/data-flow/params.png "Parameters")

### <a name="parameterized-data-flows"></a>Geparameteriseerde gegevensstromen

Als uw gegevensstroom is geparameteriseerd, stelt u de dynamische waarden van de gegevensstroomparameters in op **het tabblad** Parameters. U kunt de expressietaal van de ADF-pijplijn of de expressietaal van de gegevensstroom gebruiken om dynamische of letterlijke parameterwaarden toe te wijzen. Zie parameters Gegevensstroom [voor meer informatie.](parameters-data-flow.md)

### <a name="parameterized-compute-properties"></a>Geparameteriseerde rekeneigenschappen.

U kunt het aantal kernen of het rekentype parameteriseren als u de Azure Integration Runtime automatisch omgeeft en waarden opgeeft voor compute.coreCount en compute.computeType.

![Voorbeeld van Gegevensstroom parameter uitvoeren](media/data-flow/parameterize-compute.png "Parametervoorbeeld")

## <a name="pipeline-debug-of-data-flow-activity"></a>Foutopsporing van pijplijn Gegevensstroom activiteit

Als u een pijplijn voor foutopsporing wilt uitvoeren met een Gegevensstroom-activiteit, moet u de foutopsporingsmodus van de gegevensstroom inschakelen via de **schuifregelaar Gegevensstroom Fouten** opsporen op de bovenste balk. Met de foutopsporingsmodus kunt u de gegevensstroom uitvoeren op een actief Spark-cluster. Zie Foutopsporingsmodus [voor meer informatie.](concepts-data-flow-debug-mode.md)

![Schermopname die laat zien waar de knop Foutopsporing is](media/data-flow/debug-button-3.png)

De foutopsporingspijplijn wordt uitgevoerd op het actieve foutopsporingscluster, niet de integration runtime-omgeving die is opgegeven in Gegevensstroom instellingen voor activiteiten. U kunt de foutopsporingsomgeving kiezen bij het starten van de foutopsporingsmodus.

## <a name="monitoring-the-data-flow-activity"></a>De Gegevensstroom bewaken

De Gegevensstroom heeft een speciale bewakingservaring waar u partitionerings-, fasetijd- en gegevensvereedingsinformatie kunt bekijken. Open het bewakingsvenster via het pictogram van een bril onder **Acties.** Zie Monitoring [Data Flows (Gegevensstromen bewaken) voor meer informatie.](concepts-data-flow-monitoring.md)

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Gebruik Gegevensstroom activiteit resulteert in een volgende activiteit

De gegevensstroomactiviteit uitvoert metrische gegevens met betrekking tot het aantal rijen dat naar elke sink is geschreven en rijen die uit elke bron worden gelezen. Deze resultaten worden geretourneerd in de `output` sectie van het resultaat van de activiteitsuit werking. De geretourneerde metrische gegevens hebben de indeling van de onderstaande json.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Als u bijvoorbeeld het aantal rijen wilt weergeven dat is geschreven naar een sink met de naam sink1 in een activiteit met de naam 'dataflowActivity', gebruikt u `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Gebruik om het aantal rijen op te halen dat is gelezen uit een bron met de naam 'source1' die in die sink is `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` gebruikt.

> [!NOTE]
> Als een sink nul rijen heeft geschreven, wordt deze niet in metrische gegevens weergeven. Het bestaan kan worden geverifieerd met behulp van de `contains` functie . Controleert `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` bijvoorbeeld of er rijen naar sink1 zijn geschreven.

## <a name="next-steps"></a>Volgende stappen

Zie Controlestroomactiviteiten die worden ondersteund door Data Factory: 

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Pijplijnactiviteit uitvoeren](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)
