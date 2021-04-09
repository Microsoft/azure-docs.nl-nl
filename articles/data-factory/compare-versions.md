---
title: Azure Data Factory vergelijken met Data Factory-versie 1
description: In dit artikel wordt Azure Data Factory vergeleken met Azure Data Factory-versie 1.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: overview
ms.date: 04/09/2018
ms.openlocfilehash: dc5a4c92ee4ac0acd4a69ef94fec0981e328d829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393714"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Azure Data Factory vergelijken met Data Factory-versie 1

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt Data Factory vergeleken met Data Factory-versie 1. Zie [Inleiding tot Data Factory](introduction.md) voor een inleiding tot Data Factory. Zie [Inleiding tot Azure Data Factory](v1/data-factory-introduction.md) voor een inleiding tot Data Factory-versie 1. 

## <a name="feature-comparison"></a>Vergelijking van functies
In de volgende tabel worden de functies van Data Factory vergeleken met de functies van Data Factory-versie 1. 

| Functie | Versie 1 | Huidige versie | 
| ------- | --------- | --------- | 
| Gegevenssets | Een benoemde weergave van gegevens met een verwijzing naar de gegevens die u als in- en uitvoer wilt gebruiken in uw activiteiten. Met gegevenssets worden gegevens binnen andere gegevensarchieven geïdentificeerd, waaronder tabellen, bestanden, mappen en documenten. Een Azure Blob-gegevensset benoemt bijvoorbeeld de blobcontainer en -map in de Azure Blob-opslag van waaruit de activiteit de gegevens moet lezen.<br/><br/>**Beschikbaarheid** definieert het segmenteringsmodel voor het verwerkingsvenster voor de gegevensset (bijvoorbeeld elk uur, dagelijks, enzovoort). | De gegevenssets in de huidige versie zijn hetzelfde. U hoeft echter geen **beschikbaarheidsschema's** voor gegevenssets te definiëren. U kunt een triggerbron definiëren waarmee pijplijnen kunnen worden gepland op basis van een klokplannermodel. Zie [Triggers](concepts-pipeline-execution-triggers.md#trigger-execution) en [Gegevenssets](concepts-datasets-linked-services.md) voor meer informatie. | 
| Gekoppelde services | Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. | Gekoppelde services zijn hetzelfde als in Data Factory V1, maar met een nieuwe eigenschap **connectVia** voor het gebruik van de Integration Runtime-rekenomgeving van de huidige versie van Data Factory. Zie [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md) en [Eigenschappen van de gekoppelde service voor Azure Blob-opslag](connector-azure-blob-storage.md#linked-service-properties) voor meer informatie. |
| Pijplijnen | Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. U gebruikt startTime, endTime en isPaused voor het plannen en uitvoeren van pijplijnen. | Pijplijnen zijn groepen met activiteiten die op gegevens moeten worden uitgevoerd. Het plannen van activiteiten in de pijplijn is echter gescheiden in nieuwe triggerbronnen. U kunt pijp lijnen in de huidige versie van Data Factory meer zien als werk stroom eenheden die u afzonderlijk plant via triggers. <br/><br/>In de huidige versie van Data Factory is geen Windows-tijd meer nodig voor pijp lijnen. De concepten startTime, endTime en isPaused van Data Factory V1 zijn niet meer aanwezig in de huidige versie van Data Factory. Zie [Pijplijnuitvoering en triggers](concepts-pipeline-execution-triggers.md) en [Pijplijnen en activiteiten](concepts-pipelines-activities.md) voor meer informatie. |
| Activiteiten | Activiteiten definiëren welke acties binnen een pijplijn moeten worden uitgevoerd op uw gegevens. Activiteiten als het verplaatsen van gegevens (kopieeractiviteit) en het transformeren van gegevens (zoals Hive, Pig en MapReduce) worden ondersteund. | In de huidige versie van Data Factory zijn activiteiten nog steeds gedefinieerde acties binnen een pijplijn. De huidige versie van Data Factory introduceert nieuwe [controlestroomactiviteiten](concepts-pipelines-activities.md#control-flow-activities). U gebruikt deze activiteiten in een controlestroom (lussen en vertakkingen). Gegevensverplaatsing en gegevenstransformatie worden, net zoals in V1, ook ondersteund in de huidige versie. In de huidige versie kunt u transformatieactiviteiten definiëren zonder gegevenssets te gebruiken. |
| Hybride verplaatsing van gegevens en verzenden van activiteiten | [Gegevensbeheergateway](v1/data-factory-data-management-gateway.md), nu bekend als Integration Runtime, bood ondersteuning voor het verplaatsen van gegevens tussen on-premises en de cloud.| Gegevensbeheergateway wordt nu zelf-hostende Integration Runtime genoemd. Het biedt dezelfde mogelijkheden als in V1. <br/><br/> Azure-SSIS Integration Runtime in de huidige versie ondersteunt ook het implementeren en uitvoeren van SSIS-pakketten (SQL Server Integration Services) in de cloud. Zie voor meer informatie [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).|
| Parameters | N.v.t. | Parameters zijn sleutel-waardeparen van alleen-lezen-configuratie-instellingen die in pijplijnen worden gedefinieerd. U kunt argumenten voor de parameters doorgeven als u de pijplijn handmatig uitvoert. Als u een scheduler-trigger gebruikt, kan de trigger ook waarden voor de parameters doorgeven. Activiteiten binnen de pijplijn gebruiken de parameterwaarden.  |
| Expressies | In Data Factory V1 kunt u functies en systeemvariabelen gebruiken in gegevensselectiequery's en activiteits-/gegevensseteigenschappen. | In de huidige versie van Data Factory kunt u overal in een JSON-tekenreekswaarde gebruikmaken van expressies. Zie [Expressies en functies in de huidige versie van Data Factory](control-flow-expression-language-functions.md) voor meer informatie.|
| Pijplijnuitvoeringen | N.v.t. | Eén instantie van een uitvoeringsbewerking van een pijplijn. Stel dat u een pijplijn hebt die wordt uitgevoerd om 8 uur, 9 uur en 10 uur. In dit geval wordt de pijplijn drie keer afzonderlijk uitgevoerd (pijplijnuitvoeringen). Elke pijplijnuitvoering heeft een unieke id. De id is een unieke GUID die de betreffende specifieke pijplijnuitvoering definieert. Pijplijnuitvoeringen worden doorgaans geïnstantieerd doordat argumenten worden doorgegeven aan parameters die zijn gedefinieerd in de pijplijnen. |
| Uitvoering van activiteiten | N.v.t. | Een instantie van een uitvoerbewerking van een activiteit binnen een pijplijn. | 
| Triggeruitvoeringen | N.v.t. | Een instantie van een uitvoerbewerking van een trigger. Zie [Triggers](concepts-pipeline-execution-triggers.md) voor meer informatie. |
| Planning | Planning is gebaseerd op de begin-/eindtijd van een pijplijn en de beschikbaarheid van gegevenssets. | Scheduler-trigger of uitvoering via externe planner. Zie [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie. |

De volgende secties bevatten meer informatie over de mogelijkheden van de huidige versie. 

## <a name="control-flow"></a>Controlestroom  
De huidige versie van Data Factory heeft een nieuw flexibel gegevenspijplijnmodel dat niet meer is gekoppeld aan tijdseriegegevens, ter ondersteuning van diverse integratiestromen en -patronen in het moderne datawarehouse. Dit zijn enkele veelvoorkomende stromen die nu mogelijk zijn geworden. Deze worden gedetailleerd beschreven in de volgende gedeelten.   

### <a name="chaining-activities"></a>Koppelingsactiviteiten
In V1 moest u de uitvoer van een activiteit configureren als de invoer van een andere activiteit om ze te koppelen. In de huidige versie kunt u activiteiten koppelen in een reeks binnen een pijplijn. U kunt de eigenschap **dependsOn** in een activiteitsdefinitie gebruiken om deze te koppelen aan een upstream-activiteit. Zie [Pijplijnen en activiteiten](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) en [Vertakkings- en koppelingsactiviteiten](tutorial-control-flow.md) voor meer informatie en een voorbeeld. 

### <a name="branching-activities"></a>Vertakkingsactiviteiten
In de huidige versie kunt u vertakkingen van activiteiten maken binnen een pijplijn. De [activiteit Als-voorwaarde](control-flow-if-condition-activity.md) biedt dezelfde functionaliteit als een `if`-instructie in een programmeertaal. Er wordt een reeks activiteiten mee geëvalueerd als de voorwaarde resulteert in `true` en een andere reeks activiteiten als de voorwaarde resulteert in `false`. Zie [Vertakkings- en koppelingsactiviteiten](tutorial-control-flow.md) voor een voorbeeld van vertakkingsactiviteiten.

### <a name="parameters"></a>Parameters 
U kunt parameters definiëren op pijplijnniveau en de argumenten doorgeven tijdens het aanroepen van de pijplijn op aanvraag of vanuit een trigger. Activiteiten kunnen de argumenten die zijn doorgegeven aan de pijplijn gebruiken. Zie [Pijplijnen en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie. 

### <a name="custom-state-passing"></a>Aangepaste status doorgeven
Activiteituitvoer zoals de status kan worden gebruikt door een volgende activiteit in de pijplijn. In de JSON-definitie van een activiteit bijvoorbeeld, hebt u toegang tot de uitvoer van de vorige activiteit door middel van de volgende syntaxis: `@activity('NameofPreviousActivity').output.value`. Met deze functie kunt u werkstromen bouwen waar waarden activiteiten kunnen passeren.

### <a name="looping-containers"></a>Herhalende containers
De [activiteit ForEach](control-flow-for-each-activity.md) definieert een herhalende controlestroom in de pijplijn. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen. 

De activiteit [Until](control-flow-until-activity.md) biedt dezelfde functionaliteit als de lusstructuur do-until in een programmeertaal. Er wordt een reeks activiteiten uitgevoerd totdat de voorwaarde die aan de activiteit is gekoppeld, resulteert in `true`. U kunt in Data Factory een time-outwaarde voor de Until-activiteit opgeven.  

### <a name="trigger-based-flows"></a>Op triggers gebaseerde stromen
Pijplijnen kunnen op aanvraag (op basis van een gebeurtenis, ofwel blob-bericht) of volgens wandklokplanningen worden geactiveerd. In het artikel [Pijplijnen en triggers](concepts-pipeline-execution-triggers.md) vindt u meer informatie over triggers. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Een pijplijn aanroepen vanaf een andere pijplijn
De [activiteit Execute Pipeline](control-flow-execute-pipeline-activity.md) stelt een Data Factory-pijplijn in staat om een andere pijplijn aan te roepen.

### <a name="delta-flows"></a>Deltastromen
Een belang rijke use case in ETL-patronen is ' Delta ladingen ', waarbij alleen gegevens worden geladen die zijn gewijzigd sinds de laatste iteratie van een pijp lijn. Nieuwe mogelijkheden in de huidige versie, zoals de [activiteit lookup](control-flow-lookup-activity.md), flexibele planning en controlestroom maken deze use case op een natuurlijke manier mogelijk. Voor een zelfstudie met stapsgewijze instructies, zie [Zelfstudie: stapsgewijs kopiëren](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Andere controlestroomactiviteiten
Hier volgen nog enkele controlestroomactiviteiten die worden ondersteund in de huidige versie van Data Factory. 

Controleactiviteit | Beschrijving
---------------- | -----------
[Activiteit ForEach](control-flow-for-each-activity.md) | Deze activiteit definieert een herhalende controlestroom in de pijplijn. Deze activiteit wordt gebruikt om een verzameling te herhalen en voert opgegeven activiteiten uit in een lus. De lusimplementatie van deze activiteit is vergelijkbaar met Foreach-lusstructuur in computertalen.
[Activiteit Web](control-flow-web-activity.md) | Deze roept een aangepast REST-eindpunt aan vanaf een Data Factory-pijplijn. U kunt gegevenssets en gekoppelde services doorgeven die moten worden verbruikt door en die toegankelijk zijn voor de activiteit. 
[Activiteit Lookup](control-flow-lookup-activity.md) | Deze activiteit leest of zoekt de waarde op van een record op tabelnaamwaarde in een externe bron. Er kan naar deze uitvoer worden verwezen door volgende activiteiten. 
[Activiteit Metagegevens ophalen](control-flow-get-metadata-activity.md) | Haalt de metagegevens op van de gegevens in Azure Data Factory. 
[Activiteit Wait](control-flow-wait-activity.md) | Onderbreekt de pijplijn voor een opgegeven periode.

## <a name="deploy-ssis-packages-to-azure"></a>SSIS-pakketten implementeren in Azure 
U gebruikt Azure-SSIS om de SSIS-werkbelastingen naar de cloud te verplaatsen, een gegevensfactory te maken met de huidige versie en een Azure-SSIS Integration Runtime in te richten.

Azure-SSIS Integration Runtime is een volledig beheerd cluster met virtuele Azure-machines (knooppunten) die uw SSIS-pakketten uitvoeren in de cloud. Als u Azure-SSIS Integration Runtime hebt ingericht, kunt u dezelfde hulpmiddelen gebruiken die u hebt gebruikt voor het implementeren van SSIS-pakketten in een on-premises SSIS-omgeving. 

U kunt bijvoorbeeld SQL Server Data Tools of SQL Server Management Studio gebruiken om SSIS-pakketten te implementeren in deze runtime op Azure. Zie de zelfstudie [Pakketten van SQL Server Integration Services implementeren in Azure](./tutorial-deploy-ssis-packages-azure.md) voor stapsgewijze instructies. 

## <a name="flexible-scheduling"></a>Flexibel plannen
In de huidige versie van Data Factory hoeft u geen beschikbaarheidsschema’s voor gegevenssets te definiëren. U kunt een triggerbron definiëren waarmee pijplijnen kunnen worden gepland op basis van een klokplannermodel. U kunt ook vanuit een trigger parameters aan pijplijnen doorgeven voor een flexibel plannings- en uitvoeringsmodel. 

In de huidige versie van Data Factory is geen Windows-tijd meer nodig voor pijp lijnen. De concepten startTime, endTime en isPaused van Data Factory V1 bestaan niet meer in de huidige versie van Data Factory. Zie [Pijplijnuitvoering en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie over het bouwen en vervolgens plannen van een pijplijn in de huidige versie van Data Factory.

## <a name="support-for-more-data-stores"></a>Ondersteuning voor meer gegevensarchieven
De huidige versie ondersteunt het kopiëren van gegevens van en naar meer gegevensarchieven dan V1. Zie de volgende artikelen voor een lijst met ondersteunde gegevensarchieven:

- [Versie 1: ondersteunde gegevensarchieven](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Huidige versie: ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Ondersteuning voor Apache Spark-cluster op aanvraag
De huidige versie ondersteunt het maken van een Azure HDInsight Spark-cluster op aanvraag. Als u een Spark-cluster op aanvraag wilt maken, geeft u het clustertype op als Spark in uw definitie van uw gekoppelde HDInsight-service op aanvraag. Vervolgens configureert u de Spark-activiteit in de pijplijn voor het gebruik van deze gekoppelde service. 

Tijdens runtime, als de activiteit wordt uitgevoerd, maakt de data factory-service automatisch de Apache Spark-cluster. Raadpleeg voor meer informatie de volgende artikelen:

- [Spark-activiteiten in de huidige versie van Data Factory](transform-data-using-spark.md)
- [Een gekoppelde Azure HDInsight-service op aanvraag](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Aangepaste activiteiten
In V1 implementeert u (aangepaste) DotNet-activiteitscode door een .NET-klassebibliotheekproject te maken met een klasse die de methode Execute van de IDotNetActivity-interface implementeert. Daarom moet de aangepaste code worden geschreven in .NET Framework 4.5.2 en worden uitgevoerd op Batch-poolknooppunten van Azure onder Windows. 

In een aangepaste activiteit in de huidige versie hoeft u geen .NET-interface meer te implementeren. U kunt rechtstreeks opdrachten en scripts uitvoeren en uw eigen, aangepaste code uitvoeren die als een uitvoerbaar bestand is gecompileerd. 

Zie [Verschil tussen aangepaste activiteiten in Data Factory en versie 1](transform-data-using-dotnet-custom-activity.md#compare-v2-v1) voor meer informatie.

## <a name="sdks"></a>SDK's
 De huidige versie van Data Factory biedt een meer uitgebreide set SDK's, die kan worden gebruikt voor het ontwerpen, beheren en controleren van pijplijnen.

- **.NET SDK**: de .NET SDK is bijgewerkt in de huidige versie.

- **PowerShell**: de PowerShell-cmdlets zijn bijgewerkt in de huidige versie. In de huidige versie is **DataFactoryV2** opgenomen in de namen van de cmdlets, bijvoorbeeld: Get-AzDataFactoryV2. 

- **Python SDK**: deze SDK is nieuw in de huidige versie.

- **REST API**: de REST API is bijgewerkt in de huidige versie. 

De SDK's die zijn bijgewerkt in de huidige versie, zijn niet compatibel met eerdere versies van V1-clients. 

## <a name="authoring-experience"></a>Ontwerpen

| | Versie 2 | Versie 1 |
| ------ | -- | -- | 
| **Azure-portal** | [Ja](quickstart-create-data-factory-portal.md) | Nee |
| **Azure PowerShell** | [Ja](quickstart-create-data-factory-powershell.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-powershell.md) |
| **.NET SDK** | [Ja](quickstart-create-data-factory-dot-net.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-vs.md) |
| **REST API** | [Ja](quickstart-create-data-factory-rest-api.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-rest-api.md) |
| **Python SDK** | [Ja](quickstart-create-data-factory-python.md) | Nee |
| **Resource Manager-sjabloon** | [Ja](quickstart-create-data-factory-resource-manager-template.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Rollen en machtigingen

De rol Bijdrager in Data Factory-versie 1 kan niet worden gebruikt om resources te maken en te beheren in de huidige versie van Data Factory. Raadpleeg [Data Factory Contributor](../role-based-access-control/built-in-roles.md#data-factory-contributor) voor meer informatie.

## <a name="monitoring-experience"></a>Bewaken
In de huidige versie kunt u ook gegevensfactory’s controleren met behulp van [Azure Monitor](monitor-using-azure-monitor.md). De nieuwe PowerShell-cmdlets bieden ondersteuning voor het bewaken van [integratie-runtimes](monitor-integration-runtime.md). Zowel V1 als V2 ondersteunen visueel bewaken via bewakingstoepassingen die vanuit Azure Portal kunnen worden gestart.


## <a name="next-steps"></a>Volgende stappen
In de volgende quickstarts vindt u informatie over het maken van een data factory door het volgen van stapsgewijze instructies: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md).