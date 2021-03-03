---
title: Aangepaste activiteiten gebruiken in een pijp lijn
description: Meer informatie over het maken van aangepaste activiteiten met behulp van .NET en het gebruik van de activiteiten in een Azure Data Factory-pijp lijn.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 64588d5968df635c3bb017bd1ff1d10951968f32
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724945"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-use-custom-activities.md)
> * [Huidige versie](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Er zijn twee soorten activiteiten die u in een Azure Data Factory pijp lijn kunt gebruiken.

- [Gegevens verplaatsings activiteiten](copy-activity-overview.md) om gegevens te verplaatsen tussen het [ondersteunde bron-en Sink-gegevens archief](copy-activity-overview.md#supported-data-stores-and-formats).
- [Activiteiten voor gegevens transformatie](transform-data.md) voor het transformeren van gegevens met behulp van reken services zoals Azure HDInsight, Azure Batch en Azure machine learning.

Als u gegevens wilt verplaatsen naar/van een gegevens archief dat Data Factory niet wordt ondersteund, of als u gegevens wilt transformeren of verwerken op een manier die niet wordt ondersteund door Data Factory, kunt u een **aangepaste activiteit** maken met uw eigen gegevens verplaatsing of-transformatie logica en de activiteit in een pijp lijn gebruiken. Met de aangepaste activiteit wordt uw aangepaste code logica uitgevoerd op een **Azure batch** pool met virtuele machines.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zie de volgende artikelen als u geen ervaring hebt met Azure Batch-service:

* [Azure batch basis principes](../batch/batch-technical-overview.md) voor een overzicht van de Azure batch-service.
* De cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) om een Azure batch-account (of) te maken [Azure Portal](../batch/batch-account-create-portal.md) het Azure batch-account te maken met behulp van Azure Portal. Zie het artikel over het [beheren van Azure batch-account met](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) behulp van Power shell voor gedetailleerde instructies voor het gebruik van de cmdlet.
* De cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) om een Azure batch groep te maken.

> [!IMPORTANT]
> Bij het maken van een nieuwe Azure Batch pool moet ' VirtualMachineConfiguration ' worden gebruikt en niet ' CloudServiceConfiguration '. Raadpleeg de [richt lijnen](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md)voor de migratie van Azure batch voor groepen voor meer informatie. 

## <a name="azure-batch-linked-service"></a>Azure Batch gekoppelde service

De volgende JSON definieert een voor beeld Azure Batch gekoppelde service. Zie [reken omgevingen die worden ondersteund door Azure Data Factory](compute-linked-services.md) voor meer informatie

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over Azure batch gekoppelde service.

## <a name="custom-activity"></a>Aangepaste activiteit

Het volgende JSON-code fragment definieert een pijp lijn met een eenvoudige aangepaste activiteit. De definitie van de activiteit bevat een verwijzing naar de Azure Batch gekoppelde service.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

In dit voor beeld is de helloworld.exe een aangepaste toepassing die is opgeslagen in de map customactv2/HelloWorld van het Azure Storage account dat wordt gebruikt in de resourceLinkedService. Met de aangepaste activiteit wordt deze aangepaste toepassing verzonden zodat deze kan worden uitgevoerd op Azure Batch. U kunt de opdracht vervangen door een wille keurige toepassing die kan worden uitgevoerd op het doel bewerkings systeem van de knoop punten van de Azure Batch groep.

In de volgende tabel worden namen en beschrijvingen van eigenschappen beschreven die specifiek zijn voor deze activiteit.

| Eigenschap              | Beschrijving                              | Vereist |
| :-------------------- | :--------------------------------------- | :------- |
| naam                  | Naam van de activiteit in de pijp lijn     | Ja      |
| beschrijving           | Tekst die beschrijft wat de activiteit doet.  | Nee       |
| type                  | Voor aangepaste activiteit is het type activiteit **aangepast**. | Ja      |
| linkedServiceName     | Gekoppelde service aan Azure Batch. Zie het artikel [Compute linked Services](compute-linked-services.md) (Engelstalig) voor meer informatie over deze gekoppelde service.  | Ja      |
| command               | Opdracht van de aangepaste toepassing die moet worden uitgevoerd. Als de toepassing al beschikbaar is op het Azure Batch groeps knooppunt, kunnen de resourceLinkedService en folderPath worden overgeslagen. U kunt bijvoorbeeld de opdracht opgeven `cmd /c dir` , die wordt ondersteund door het knoop punt Windows Batch-groep. | Ja      |
| resourceLinkedService | Azure Storage gekoppelde service naar het opslag account waarin de aangepaste toepassing is opgeslagen | Geen &#42;       |
| folderPath            | Pad naar de map van de aangepaste toepassing en alle bijbehorende afhankelijkheden<br/><br/>Als er afhankelijkheden zijn opgeslagen in submappen, dat wil zeggen, in een hiërarchische mappen structuur onder *FolderPath* : de mapstructuur wordt op dit moment afgevlakt wanneer de bestanden worden gekopieerd naar Azure batch. Dat wil zeggen dat alle bestanden naar één map zonder submappen worden gekopieerd. U kunt dit probleem omzeilen door de bestanden te comprimeren, het gecomprimeerde bestand te kopiëren en deze vervolgens te uitgepakt met aangepaste code op de gewenste locatie. | Geen &#42;       |
| referenceObjects      | Een matrix met bestaande gekoppelde services en gegevens sets. De gekoppelde services en gegevens sets waarnaar wordt verwezen, worden door gegeven aan de aangepaste toepassing in JSON-indeling zodat uw aangepaste code kan verwijzen naar resources van de Data Factory | Nee       |
| extendedProperties    | Door de gebruiker gedefinieerde eigenschappen die kunnen worden door gegeven aan de aangepaste toepassing in JSON-indeling zodat uw aangepaste code kan verwijzen naar aanvullende eigenschappen | Nee       |
| retentionTimeInDays | De retentie tijd voor de bestanden die zijn verzonden voor aangepaste activiteit. De standaard waarde is 30 dagen. | Nee |

&#42; de eigenschappen `resourceLinkedService` en `folderPath` moet beide worden opgegeven of beide worden wegge laten.

> [!NOTE]
> Als u gekoppelde services als referenceObjects in een aangepaste activiteit doorgeeft, is het een goede beveiligings procedure om een Azure Key Vault ingeschakelde gekoppelde service uit te geven (aangezien deze geen beveiligde teken reeksen bevat) en de referenties rechtstreeks vanuit Key Vault uit de code op te halen met behulp van een geheime naam. [Hier](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) vindt u een voor beeld waarin wordt verwezen naar Azure ingeschakelde gekoppelde service, haalt de referenties op uit Key Vault en opent vervolgens de opslag in de code.

## <a name="custom-activity-permissions"></a>Machtigingen voor aangepaste activiteiten

Met de aangepaste activiteit wordt het Azure Batch automatische gebruikers account ingesteld op *niet-beheerders toegang met het taak bereik* (de standaard specificatie voor automatische gebruikers). U kunt het machtigings niveau van het account voor automatische gebruikers niet wijzigen. Zie [taken uitvoeren onder gebruikers accounts in batch | voor meer informatie. Automatische gebruikers accounts](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Opdrachten uitvoeren

U kunt een opdracht rechtstreeks uitvoeren met aangepaste activiteit. In het volgende voor beeld wordt de opdracht ' echo hallo wereld ' op het doel Azure Batch groeps knooppunten uitgevoerd en wordt de uitvoer naar stdout afgedrukt.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Objecten en eigenschappen door geven

In dit voor beeld ziet u hoe u referenceObjects en extendedProperties kunt gebruiken om Data Factory objecten en door de gebruiker gedefinieerde eigenschappen door te geven aan uw aangepaste toepassing.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Wanneer de activiteit wordt uitgevoerd, worden referenceObjects en extendedProperties opgeslagen in de volgende bestanden die worden geïmplementeerd in dezelfde map voor uitvoering van de SampleApp.exe:

- `activity.json`

  Hiermee worden extendedProperties en eigenschappen van de aangepaste activiteit opgeslagen.

- `linkedServices.json`

  Hiermee wordt een matrix opgeslagen met gekoppelde services die zijn gedefinieerd in de eigenschap referenceObjects.

- `datasets.json`

  Slaat een matrix van gegevens sets op die zijn gedefinieerd in de eigenschap referenceObjects.

De volgende voorbeeld code laat zien hoe de SampleApp.exe toegang kan krijgen tot de vereiste gegevens van JSON-bestanden:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Uitvoer uitvoeringen ophalen

U kunt een pijplijn uitvoering starten met behulp van de volgende Power shell-opdracht:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Wanneer de pijp lijn wordt uitgevoerd, kunt u de uitvoerings uitvoer controleren met de volgende opdrachten:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

De **stdout** en **stderr** van uw aangepaste toepassing worden opgeslagen in de **adfjobs** -container in de Azure Storage gekoppelde service die u hebt gedefinieerd bij het maken van Azure batch gekoppelde service met een GUID van de taak. U kunt het gedetailleerde pad ophalen uit de uitvoer van de activiteit, zoals wordt weer gegeven in het volgende code fragment:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Als u de inhoud van stdout.txt in stroomafwaartse activiteiten wilt gebruiken, kunt u het pad naar het stdout.txt bestand in de expressie ' \@ activiteit ' (' MyCustomActivity '). output. outputs [0] '.

> [!IMPORTANT]
> - De activity.jsop, linkedServices.jsop en datasets.jsop worden opgeslagen in de map runtime van de batch-taak. Voor dit voor beeld worden de activity.jsop, linkedServices.json en datasets.json opgeslagen in het `https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/` pad. Als dat nodig is, moet u deze afzonderlijk opschonen.
> - Voor gekoppelde services die gebruikmaken van de Self-Hosted Integration Runtime, de gevoelige informatie zoals sleutels of wacht woorden worden versleuteld door de Self-Hosted Integration Runtime om ervoor te zorgen dat de referenties blijven bestaan in een door de klant gedefinieerde particuliere netwerk omgeving. Er ontbreken mogelijk enkele gevoelige velden wanneer ernaar wordt verwezen door uw aangepaste toepassings code. Gebruik SecureString in extendedProperties in plaats van een gekoppelde service verwijzing zo nodig te gebruiken.

## <a name="pass-outputs-to-another-activity"></a>Uitvoer door geven aan een andere activiteit

U kunt aangepaste waarden van uw code in een aangepaste activiteit naar Azure Data Factory verzenden. U kunt dit doen door ze te schrijven `outputs.json` vanuit uw toepassing. Data Factory kopieert de inhoud van `outputs.json` en voegt deze toe aan de uitvoer van de activiteit als waarde van de `customOutput` eigenschap. (De limiet voor de grootte is 2 MB.) Als u de inhoud van `outputs.json` in downstream activiteiten wilt gebruiken, kunt u de waarde ophalen met behulp van de-expressie `@activity('<MyCustomActivity>').output.customOutput` .

## <a name="retrieve-securestring-outputs"></a>SecureString-uitvoer ophalen

Gevoelige eigenschaps waarden die zijn opgegeven als type *SecureString*, zoals wordt weer gegeven in enkele voor beelden in dit artikel, worden gemaskeerd op het tabblad controle in de gebruikers interface van Data Factory.  Bij uitvoering van de werkelijke pijp lijn wordt een *SecureString* -eigenschap echter GESERIALISEERD als JSON in het `activity.json` bestand als tekst zonder opmaak. Bijvoorbeeld:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Deze serialisatie is niet echt veilig en is niet bedoeld om te worden beveiligd. Het doel is om Data Factory om de waarde te maskeren op het tabblad controle.

Als u eigenschappen van het type *SecureString* vanuit een aangepaste activiteit wilt openen, leest u het `activity.json` bestand, dat zich in dezelfde map als uw heeft bevindt. EXE, deserialiseren van de JSON en vervolgens toegang tot de JSON-eigenschap (extendedProperties => [propertyname] => waarde).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> V2-aangepaste activiteit vergelijken en versie 1 (aangepast) DotNet-activiteit

In Azure Data Factory versie 1 implementeert u een (aangepaste) DotNet-activiteit door een .NET Class Library-project te maken met een klasse die de `Execute` methode van de interface implementeert `IDotNetActivity` . De gekoppelde services, gegevens sets en uitgebreide eigenschappen in de JSON-nettolading van een (aangepaste) DotNet-activiteit worden door gegeven aan de uitvoerings methode als objecten met een sterk type. Zie [(aangepast) DotNet in versie 1](v1/data-factory-use-custom-activities.md)voor meer informatie over het gedrag van versie 1. Als gevolg van deze implementatie moet uw versie 1 DotNet-activiteit code .NET Framework 4.5.2. De activiteit versie 1 DotNet moet ook worden uitgevoerd op op Windows gebaseerde Azure Batch pool-knoop punten.

In de aangepaste v2-activiteit van Azure Data Factory is het niet nodig om een .NET-interface te implementeren. U kunt nu direct opdrachten, scripts en uw eigen aangepaste code uitvoeren, gecompileerd als een uitvoerbaar bestand. Als u deze implementatie wilt configureren, geeft u de `Command` eigenschap samen met de `folderPath` eigenschap op. De aangepaste activiteit uploadt het uitvoer bare bestand en de bijbehorende afhankelijkheden naar `folderpath` en voert de opdracht voor u uit.

De gekoppelde services, gegevens sets (gedefinieerd in referenceObjects) en uitgebreide eigenschappen die zijn gedefinieerd in de JSON-payload van een aangepaste versie van Data Factory v2-activiteit, kunnen worden gebruikt door uw uitvoer bare bestand als JSON-bestand. U kunt de vereiste eigenschappen openen met behulp van een JSON serializer, zoals wordt weer gegeven in het voor gaande SampleApp.exe code voorbeeld.

Met de wijzigingen die in de aangepaste Data Factory v2-activiteit zijn geïntroduceerd, kunt u uw aangepaste code logica in uw voorkeurs taal schrijven en deze uitvoeren op Windows-en Linux-bewerkings systemen die worden ondersteund door Azure Batch.

In de volgende tabel worden de verschillen beschreven tussen de aangepaste Data Factory v2-activiteit en de Data Factory versie 1 (aangepast) DotNet-activiteit:

|Verschillen      | Aangepaste activiteit      | versie 1 (aangepast) DotNet-activiteit      |
| ---- | ---- | ---- |
|Hoe aangepaste logica wordt gedefinieerd      |Door een uitvoerbaar bestand op te geven      |Een .NET-DLL implementeren      |
|Uitvoerings omgeving van de aangepaste logica      |Windows of Linux      |Windows (.NET Framework 4.5.2)      |
|Scripts uitvoeren      |Ondersteunt het rechtstreeks uitvoeren van scripts (bijvoorbeeld ' cmd/c echo hallo wereld ' op Windows VM)      |Implementatie vereist in de .NET-DLL      |
|Gegevensset vereist      |Optioneel      |Vereist om activiteiten te koppelen en informatie door te geven      |
|Informatie van activiteit door geven aan aangepaste logica      |Via ReferenceObjects (LinkedServices en gegevens sets) en ExtendedProperties (aangepaste eigenschappen)      |Via ExtendedProperties (aangepaste eigenschappen), invoer en uitvoer gegevens sets      |
|Informatie ophalen in aangepaste logica      |Parseert activity.jsop, linkedServices.jsop en datasets.jsopgeslagen in dezelfde map van het uitvoer bare bestand      |Via .NET SDK (.NET-frame 4.5.2)      |
|Logboekregistratie      |Schrijft rechtstreeks naar STDOUT      |De logboeken in .NET DLL implementeren      |

Als u bestaande .NET-code hebt geschreven voor een DotNet-activiteit van versie 1 (aangepast), moet u de code zodanig aanpassen dat deze werkt met de huidige versie van de aangepaste activiteit. Werk uw code bij door deze richt lijnen op hoog niveau te volgen:

  - Wijzig het project van een .NET-klassebibliotheek naar een console-app.
  - Start uw toepassing met de- `Main` methode. De `Execute` methode van de `IDotNetActivity` interface is niet meer vereist.
  - De gekoppelde services, gegevens sets en activiteiten met een JSON-serialisatiefunctie lezen en parseren, en niet als sterk getypte objecten. Geef de waarden van de vereiste eigenschappen door aan uw belangrijkste aangepaste code logica. Raadpleeg de voor gaande SampleApp.exe code als voor beeld.
  - Het logboek object wordt niet meer ondersteund. Uitvoer van het uitvoer bare bestand kan worden afgedrukt op de-console en wordt opgeslagen in stdout.txt.
  - Het NuGet-pakket micro soft. Azure. Management. DataFactories is niet meer nodig.
  - Compileer uw code, upload het uitvoer bare bestand en de bijbehorende afhankelijkheden naar Azure Storage en definieer het pad in de `folderPath` eigenschap.

Voor een volledig voor beeld van de manier waarop het end-to-end DLL-en pijp lijn-voor beeld dat wordt beschreven in het artikel Data Factory versie 1 [aangepaste activiteiten gebruiken in een Azure Data Factory pijp lijn](./v1/data-factory-use-custom-activities.md) kunnen worden herschreven als Data Factory aangepaste activiteit, Zie [Data Factory aangepaste activiteit voor beeld](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatisch schalen van Azure Batch

U kunt ook een Azure Batch groep maken met de functie voor **automatisch schalen** . U kunt bijvoorbeeld een Azure batch-pool maken met 0 toegewezen Vm's en een formule voor automatisch schalen op basis van het aantal in behandeling zijnde taken.

Met de voorbeeld formule wordt het volgende gedrag gerealiseerd: wanneer de pool voor het eerst wordt gemaakt, begint deze met 1 VM. $PendingTasks metriek definieert het aantal taken in de status actief + actief (in wachtrij). Met de formule vindt u het gemiddelde aantal taken in de afgelopen 180 seconden dat in behandeling is en stelt u TargetDedicated dienovereenkomstig in. Zo zorgt u ervoor dat TargetDedicated nooit meer dan 25 Vm's verloopt. Als er nieuwe taken worden ingediend, neemt de groep automatisch toe en als de taken zijn voltooid, worden virtuele machines één voor één vrijgegeven en wordt de virtuele machine met automatisch schalen kleiner. startingNumberOfVMs en maxNumberofVMs kunnen worden aangepast aan uw behoeften.

Formule voor automatisch schalen:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zie [reken knooppunten automatisch schalen in een Azure batch groep](../batch/batch-automatic-scaling.md) voor meer informatie.

Als de groep de standaard [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)gebruikt, kan de batch-service 15-30 minuten in beslag nemen voordat de aangepaste activiteit wordt uitgevoerd. Als de groep een andere autoScaleEvaluationInterval gebruikt, kan de batch-service autoScaleEvaluationInterval + 10 minuten duren.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeert:

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive-activiteit](transform-data-using-hadoop-hive.md)
* [Pig-activiteit](transform-data-using-hadoop-pig.md)
* [MapReduce-activiteit](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-activiteit](transform-data-using-hadoop-streaming.md)
* [Spark-activiteit](transform-data-using-spark.md)
* [Activiteit voor het uitvoeren van Azure Machine Learning Studio (klassiek)](transform-data-using-machine-learning.md)
* [Opgeslagen procedure activiteit](transform-data-using-stored-procedure.md)