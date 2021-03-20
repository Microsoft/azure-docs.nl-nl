---
title: Het NuGet-pakket voor de Azure Stream Analytics CI/CD gebruiken
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics CI/CD NuGet-pakket gebruikt om een doorlopend integratie-en implementatie proces in te stellen.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 0b4356c74b2e0c1494456d5d1082efd7b8953a15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693372"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Het NuGet-pakket voor de Azure Stream Analytics CI/CD gebruiken voor integratie en ontwikkeling 
In dit artikel wordt beschreven hoe u het Azure Stream Analytics CI/CD NuGet-pakket gebruikt om een doorlopend integratie-en implementatie proces in te stellen.

Gebruik versie 2.3.0000.0 of hoger van [Stream Analytics tools for Visual Studio](./stream-analytics-quick-create-vs.md) om ondersteuning voor MSBuild te krijgen.

Er is een NuGet-pakket beschikbaar: [micro soft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Het biedt de hulpprogram ma's voor MSBuild, lokaal uitvoeren en implementeren die ondersteuning bieden voor het doorlopende integratie-en implementatie proces van [Stream Analytics Visual Studio-projecten](stream-analytics-vs-tools.md). 
> [!NOTE]
> Het NuGet-pakket kan alleen worden gebruikt met de 2.3.0000.0 of de bovenstaande versie van Stream Analytics-Hulpprogram Ma's voor Visual Studio. Als u projecten hebt gemaakt in eerdere versies van Visual Studio-hulpprogram ma's, opent u deze met de 2.3.0000.0 of de bovenstaande versie en slaat u deze op. Daarna zijn de nieuwe mogelijkheden ingeschakeld. 

Zie [Stream Analytics-hulpprogram ma's voor Visual Studio](./stream-analytics-quick-create-vs.md)voor meer informatie.

## <a name="msbuild"></a>MSBuild
Net als de standaard Visual Studio MSBuild-ervaring om een project te bouwen, hebt u twee opties. U kunt met de rechter muisknop op het project klikken en vervolgens **Build** kiezen. U kunt **MSBuild** ook gebruiken in het NuGet-pakket vanaf de opdracht regel.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Wanneer een Stream Analytics Visual Studio-project is gebouwd, worden de volgende twee Azure Resource Manager sjabloon bestanden gegenereerd in de map **bin/[debug/Retail]/Deploy** : 

* Resource Manager-sjabloon bestand

   `[ProjectName].JobTemplate.json`

* Resource Manager-parameter bestand
   
   `[ProjectName].JobTemplate.parameters.json`

De standaard parameters in de parameters.jsin het bestand zijn afkomstig uit de instellingen in uw Visual Studio-project. Als u wilt implementeren in een andere omgeving, vervangt u de para meters dienovereenkomstig.

> [!NOTE]
> Voor alle referenties worden de standaard waarden ingesteld op null. U **moet** de waarden instellen voordat u naar de Cloud implementeert.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Meer informatie over hoe u kunt [implementeren met een resource manager-sjabloon bestand en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Meer informatie over het [gebruik van een object als een para meter in een resource manager-sjabloon](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

Als u beheerde identiteit voor Azure Data Lake Store gen1 als uitvoer Sink wilt gebruiken, moet u toegang geven tot de service-principal met behulp van Power shell voordat u implementeert in Azure. Meer informatie over het [implementeren van ADLS gen1 met beheerde identiteit met een resource manager-sjabloon](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Opdracht regel programma

### <a name="build-the-project"></a>Het project bouwen
Het NuGet-pakket heeft een opdracht regel programma met de naam **SA.exe**. Het ondersteunt project build en lokale tests op een wille keurige computer, die u kunt gebruiken in uw continue integratie en doorlopend leverings proces. 

De implementatie bestanden worden standaard in de huidige map geplaatst. U kunt het uitvoerpad opgeven met behulp van de volgende para meter-OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Het script lokaal testen

Als uw project lokale invoer bestanden heeft opgegeven in Visual Studio, kunt u een geautomatiseerde script test uitvoeren met behulp van de opdracht *localrun* . Het resultaat van de uitvoer wordt onder de huidige map geplaatst.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Een taak definitie bestand genereren voor gebruik met de Stream Analytics Power shell-API

De *arm* -opdracht accepteert de taak sjabloon en de parameter bestanden van de taak sjablonen die zijn gegenereerd via build als invoer. Vervolgens worden deze gecombineerd in een JSON-bestand met taak definities dat kan worden gebruikt met de Stream Analytics Power shell-API.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Voorbeeld:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Azure Stream Analytics Cloud taak maken in Visual Studio](stream-analytics-quick-create-vs.md)
* [Stream Analytics query's lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics-taken verkennen met Visual Studio](stream-analytics-vs-tools.md)