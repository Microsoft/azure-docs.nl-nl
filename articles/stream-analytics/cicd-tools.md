---
title: Bouw-, test-en implementaties van een Azure Stream Analytics-taak automatiseren met CI/CD-hulpprogram ma's
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics CI/CD-hulpprogram ma's gebruikt om automatisch een Azure Stream Analytics-project te bouwen, te testen en te implementeren.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036959"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Bouw-, test-en implementaties van een Azure Stream Analytics-taak automatiseren met CI/CD-hulpprogram ma's

U kunt het Azure Stream Analytics CI/CD NPM-pakket gebruiken om automatisch uw Azure Stream Analytics Visual Studio-of Visual Studio-projecten te bouwen, te testen en te implementeren. De projecten kunnen worden gemaakt met behulp van ontwikkel hulpprogramma's of ze kunnen worden geëxporteerd vanuit bestaande Stream Analytics taken. In dit artikel wordt beschreven hoe u het NPM-pakket gebruikt met een CI/CD-systeem. Zie [Azure DevOps gebruiken om een CI/cd-pijp lijn te maken voor een stream Analytics-taak](set-up-cicd-pipeline.md)voor implementatie met Azure-pijp lijnen.

## <a name="installation"></a>Installatie

U kunt [het pakket rechtstreeks downloaden](https://www.npmjs.com/package/azure-streamanalytics-cicd) of het [globaal](https://docs.npmjs.com/downloading-and-installing-packages-globally) installeren met behulp van de `npm install -g azure-streamanalytics-cicd` opdracht. We raden u aan de opdracht te gebruiken, die ook kan worden gebruikt in een Power shell-of Azure CLI-script taak van een build-pijp lijn in **Azure-pijp lijnen**.

## <a name="build-the-project"></a>Het project bouwen

Het pakket **ASA-streamanalytics-cicd** NPM bevat de hulpprogram ma's voor het genereren van Azure Resource Manager sjablonen van stream Analytics [Visual Studio-code projecten](./quick-create-visual-studio-code.md) of [Visual Studio-projecten](stream-analytics-quick-create-vs.md). U kunt ook het NPM-pakket gebruiken in Windows, macOS en Linux zonder Visual Studio code of Visual Studio te installeren.

Nadat u het pakket hebt geïnstalleerd, gebruikt u de volgende opdracht om uw Stream Analytics-projecten te bouwen.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

De *Build* -opdracht voert een controle van de syntaxis van een tref woord uit en voert de Azure Resource Manager sjabloon uit.

| Parameter | Beschrijving |
|---|---|
| `-project` | Het absolute pad van de **asaproj.jsin** het bestand voor uw Visual Studio code-project of **[uw project naam]. Asaproj** voor Visual Studio-project. |
| `-outputPath` | Het pad naar de uitvoermap voor Azure Resource Manager sjablonen. Als deze niet is opgegeven, worden de sjablonen in de huidige map geplaatst. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Wanneer een Stream Analytics project is gebouwd, worden de volgende twee bestanden in de uitvoermap gegenereerd:

* Azure Resource Manager sjabloon bestand

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager parameter bestand

   `[ProjectName].JobTemplate.parameters.json`

De standaard parameters in het parameters.jsbestand zijn afkomstig uit de instellingen in uw Visual Studio-code of Visual Studio-project. Als u wilt implementeren in een andere omgeving, vervangt u de para meters dienovereenkomstig.

De standaard waarden voor alle referenties zijn **Null**. U moet de waarden instellen voordat u naar Azure implementeert.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Als u beheerde identiteit voor Azure Data Lake Store gen1 als een uitvoer Sink wilt gebruiken, moet u toegang tot de Service-Principal verlenen met behulp van Power shell voordat u implementeert in Azure. Meer informatie over het [implementeren van ADLS gen1 met beheerde identiteit met een resource manager-sjabloon](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Lokaal uitvoeren

Als uw project lokale invoer bestanden heeft opgegeven, kunt u een Stream Analytics script lokaal uitvoeren met behulp van de `localrun` opdracht.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beschrijving |
|---|---|
| `-project` | Het pad van de **asaproj.jsin** het bestand voor uw Visual Studio code-project of **[uw project naam]. Asaproj** voor Visual Studio-project. |
| `-outputPath` | Het pad naar de uitvoermap. Als deze niet is opgegeven, worden de uitvoer resultaat bestanden in de huidige map geplaatst. |
| `-customCodeZipFilePath` | Het pad van het zip-bestand voor aangepaste C#-code, zoals een UDF of deserializer, als deze worden gebruikt. De Dll's inpakken in een zip-bestand en dit pad opgeven. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> Java script UDF werkt alleen in Windows.

## <a name="automated-test"></a>Geautomatiseerd testen

U kunt het CI/CD NPM-pakket gebruiken om automatische tests voor uw Stream Analytics script te configureren en uit te voeren.

### <a name="add-a-test-case"></a>Een test case toevoegen

De test cases worden beschreven in een test configuratie bestand. Om aan de slag te gaan, gebruikt u de `addtestcase` opdracht om een test case sjabloon toe te voegen aan het configuratie bestand van de test. Als het configuratie bestand van de test niet bestaat, wordt er standaard een gemaakt.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parameter | Beschrijving |
|---|---|
| `-project` | Het pad van de **asaproj.jsin** het bestand voor uw Visual Studio code-project of **[uw project naam]. Asaproj** voor Visual Studio-project. |
| `-testConfigPath` | Het pad van het test configuratie bestand. Als deze niet is opgegeven, wordt het bestand doorzocht in **\test** onder de huidige map van de **asaproj.jsin** het bestand met de standaard bestandsnaam **testConfig.jsop**. Er wordt een nieuw bestand gemaakt als dat nog niet bestaat. |

> [!NOTE]
> De `Script` waarde in de gegenereerde **testConfig.jsin** het bestand is alleen voor het leveren van de context. Het wordt niet gebruikt in de test logica. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Als het configuratie bestand voor de test leeg is, wordt de volgende inhoud naar het bestand geschreven. Anders wordt een test aanvraag toegevoegd aan de matrix van **TestCases**. De benodigde invoer configuraties worden automatisch gevuld volgens de invoer configuratie bestanden, indien aanwezig. Anders worden de standaard waarden geconfigureerd. Het **bestandspad** van elke invoer en verwachte uitvoer moet worden opgegeven voordat de test wordt uitgevoerd. U kunt de configuratie hand matig wijzigen.

Als u wilt dat de test validatie een bepaalde uitvoer negeert, stelt u het **verplichte** veld van die verwachte uitvoer in op **Onwaar**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Op dit moment is de enige toegestane waarde voor het `ScriptType` element `InputMock` , dat ook de standaard waarde is. Als u deze instelt op een andere waarde, wordt dit genegeerd en wordt de standaard waarde ( `InputMock` ) gebruikt. 

### <a name="run-a-unit-test"></a>Een eenheids test uitvoeren

U kunt de volgende opdracht gebruiken om meerdere test cases voor uw project uit te voeren. Er wordt een samen vatting van de test resultaten gegenereerd in de uitvoermap. Het proces wordt afgesloten met code **0** voor alle door gegeven tests; **-1** voor uitzonde ring opgetreden; **-2** voor testen is mislukt.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beschrijving |
|---|---|
| `-project` | Het pad van de **asaproj.jsin** het bestand voor uw Visual Studio code-project of **[uw project naam]. Asaproj** voor Visual Studio-project. |
| `-testConfigPath` | Het pad naar het configuratie bestand voor de test. Als deze niet is opgegeven, wordt het bestand doorzocht in **\test** onder de huidige map van de **asaproj.jsin** het bestand met de standaard bestandsnaam **testConfig.jsop**.
| `-outputPath` | Het pad van de uitvoermap voor het test resultaat. Als deze niet is opgegeven, worden de uitvoer resultaat bestanden in de huidige map geplaatst. |
| `-customCodeZipFilePath` | Het pad van het zip-bestand voor aangepaste code, zoals een UDF of deserializer, als deze worden gebruikt. |

Wanneer alle tests zijn voltooid, wordt een samen vatting van de test resultaten in JSON-indeling gegenereerd in de uitvoermap. Het overzichts bestand heeft de naam **testResultSummary.jsop**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Implementeren op Azure

U kunt de Azure Resource Manager sjabloon en de parameter bestanden die zijn gegenereerd op basis van build gebruiken om [uw taak in azure te implementeren](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Volgende stappen

* [Continue integratie en continue implementatie voor Azure Stream Analytics](cicd-overview.md)
* [Een CI/CD-pijp lijn instellen voor Stream Analytics-taak met behulp van Azure-pijp lijnen](set-up-cicd-pipeline.md)
