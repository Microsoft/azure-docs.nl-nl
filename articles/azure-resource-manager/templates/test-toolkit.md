---
title: ARM-sjabloon test Toolkit
description: Hierin wordt beschreven hoe u de Azure Resource Manager sjabloon (ARM-sjabloon) test Toolkit uitvoert op uw sjabloon. Met de Toolkit kunt u zien of u aanbevolen procedures hebt geïmplementeerd.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 29ebe2f0ec4d4c39154272bbbdf4d0c9ede9fd95
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928185"
---
# <a name="use-arm-template-test-toolkit"></a>ARM-sjabloon test Toolkit gebruiken

Met de [Azure Resource Manager sjabloon (arm-sjabloon) test Toolkit](https://aka.ms/arm-ttk) wordt gecontroleerd of de sjabloon aanbevolen procedures gebruikt. Als uw sjabloon niet voldoet aan de aanbevolen procedures, wordt een lijst met waarschuwingen met de voorgestelde wijzigingen geretourneerd. Met behulp van de test Toolkit kunt u leren hoe u veelvoorkomende problemen bij het ontwikkelen van sjablonen voor komt.

De test Toolkit bevat een aantal [standaard testen](test-cases.md). Deze tests zijn aanbevelingen, maar geen vereisten. U kunt bepalen welke tests relevant zijn voor uw doelen en aanpassen welke tests worden uitgevoerd.

In dit artikel wordt beschreven hoe u de test Toolkit uitvoert en hoe u tests toevoegt of verwijdert. Zie [test cases voor kits](test-cases.md)voor beschrijvingen van de standaard tests.

De Toolkit is een set Power shell-scripts die kunnen worden uitgevoerd met een opdracht in Power shell of CLI.

## <a name="install-on-windows"></a>Installeren in Windows

1. Als u Power shell nog niet hebt, [installeert u Power shell in Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Down load het meest recente zip-bestand](https://aka.ms/arm-ttk-latest) voor de test Toolkit en pak het uit.

1. Start Power shell.

1. Navigeer naar de map waar u de test Toolkit hebt uitgepakt. Ga in deze map naar **arm-ttk** map.

1. Als uw [uitvoerings beleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies) scripts van Internet blokkeert, moet u de blok kering van de script bestanden opheffen. Zorg ervoor dat u zich in de map **arm-ttk** bevindt.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importeer de module.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Als u de tests wilt uitvoeren, gebruikt u de volgende opdracht:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Installeren in Linux

1. Als u Power shell nog niet hebt, [installeert u Power shell op Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Down load het meest recente zip-bestand](https://aka.ms/arm-ttk-latest) voor de test Toolkit en pak het uit.

1. Start Power shell.

   ```bash
   pwsh
   ```

1. Navigeer naar de map waar u de test Toolkit hebt uitgepakt. Ga in deze map naar **arm-ttk** map.

1. Als uw [uitvoerings beleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies) scripts van Internet blokkeert, moet u de blok kering van de script bestanden opheffen. Zorg ervoor dat u zich in de map **arm-ttk** bevindt.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importeer de module.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Als u de tests wilt uitvoeren, gebruikt u de volgende opdracht:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Installeren in Mac OS

1. Als u Power shell nog niet hebt, [installeert u Power shell op macOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. `coreutils` installeren:

   ```bash
   brew install coreutils
   ```

1. [Down load het meest recente zip-bestand](https://aka.ms/arm-ttk-latest) voor de test Toolkit en pak het uit.

1. Start Power shell.

   ```bash
   pwsh
   ```

1. Navigeer naar de map waar u de test Toolkit hebt uitgepakt. Ga in deze map naar **arm-ttk** map.

1. Als uw [uitvoerings beleid](/powershell/module/microsoft.powershell.core/about/about_execution_policies) scripts van Internet blokkeert, moet u de blok kering van de script bestanden opheffen. Zorg ervoor dat u zich in de map **arm-ttk** bevindt.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importeer de module.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Als u de tests wilt uitvoeren, gebruikt u de volgende opdracht:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Resultaatopmaak

Tests die worden uitgevoerd in de **groen** worden weer gegeven en zijn voorafgegaan door **[+]**.

Niet-uitgevoerde tests worden **rood** weer gegeven en worden voorafgegaan door **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="test resultaten weer geven":::

De tekst resultaten zijn:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Test parameters

Wanneer u de para meter **-TemplatePath** opgeeft, zoekt de toolkit in die map naar een sjabloon met de naam azuredeploy.jsop of maintemplate.jsop. Deze sjabloon wordt eerst getest en vervolgens worden alle andere sjablonen in de map en de bijbehorende submappen getest. De andere sjablonen worden getest als gekoppelde sjablonen. Als uw pad een bestand bevat met de naam [CreateUiDefinition.jsop](../managed-applications/create-uidefinition-overview.md), worden er tests uitgevoerd die relevant zijn voor de UI-definitie.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Als u een bestand in die map wilt testen, voegt u de para meter **-File** toe. De map moet echter nog steeds een hoofd sjabloon hebben met de naam azuredeploy.jsop of maintemplate.jsop.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Standaard worden alle tests uitgevoerd. Als u wilt opgeven welke afzonderlijke testen moeten worden uitgevoerd, gebruikt u de para meter **-test** . Geef de naam van de test op. Zie [test cases voor Toolkit](test-cases.md)voor de namen.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Tests aanpassen

Voor ARM-sjablonen voert de Toolkit alle tests uit in de map **\arm-ttk\testcases\deploymentTemplate**. Als u een test permanent wilt verwijderen, verwijdert u het bestand uit de map.

Voor [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) -bestanden worden alle tests in de map **\arm-ttk\testcases\CreateUiDefinition** uitgevoerd.

Als u uw eigen test wilt toevoegen, maakt u een bestand met de naam Conventie: **Your-Custom-Test-Name.test.ps1**.

De test kan de sjabloon ophalen als een object parameter of een teken reeks parameter. Normaal gesp roken gebruikt u de ene of de andere, maar u kunt beide gebruiken.

Gebruik de object parameter wanneer u een sectie van de sjabloon wilt ophalen en de eigenschappen wilt door lopen. Een test die gebruikmaakt van de object parameter heeft de volgende indeling:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Het object Temp late heeft de volgende eigenschappen:

* $schema
* contentVersion
* parameters
* variabelen
* resources
* uitvoer

U kunt bijvoorbeeld de verzameling para meters ophalen met `$TemplateObject.parameters` .

Gebruik de teken reeks parameter wanneer u een teken reeks bewerking op de hele sjabloon moet uitvoeren. Een test die gebruikmaakt van de teken reeks parameter heeft de volgende indeling:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

U kunt bijvoorbeeld een reguliere expressie van de teken reeks parameter uitvoeren om te zien of er een specifieke syntaxis wordt gebruikt.

Bekijk de andere tests in die map voor meer informatie over het implementeren van de test.

## <a name="integrate-with-azure-pipelines"></a>Integreren met Azure-pijp lijnen

U kunt de test Toolkit toevoegen aan uw Azure-pijp lijn. Met een pijp lijn kunt u de test uitvoeren elke keer dat de sjabloon wordt bijgewerkt, of deze uitvoeren als onderdeel van het implementatie proces.

De eenvoudigste manier om de test Toolkit toe te voegen aan uw pijp lijn is met uitbrei dingen van derden. De volgende twee uitbrei dingen zijn beschikbaar:

* [ARM TTK-tests uitvoeren](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Test programma ARM-sjabloon](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

U kunt ook uw eigen taken implementeren. In het volgende voor beeld ziet u hoe u de test Toolkit downloadt.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

In het volgende voor beeld ziet u hoe u de tests uitvoert.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [standaard test cases voor arm-sjabloon test Toolkit](test-cases.md)voor meer informatie over de standaard tests.
