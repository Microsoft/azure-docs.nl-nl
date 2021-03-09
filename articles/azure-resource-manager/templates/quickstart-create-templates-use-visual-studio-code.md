---
title: Sjabloon maken - Visual Studio Code
description: Gebruik Visual Studio Code en de Azure Resource Manager-extensie voor hulpprogramma's om te werken met Azure Resource Manager-sjablonen (ARM-sjablonen).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504654"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Quickstart: ARM-sjablonen maken met Visual Studio Code

De Azure Resource Manager-hulpprogramma's voor Visual Studio Code bieden taalondersteuning, resourcefragmenten en automatisch voltooien van resources. Met deze hulpprogramma's kunt u Azure Resource Manager-sjablonen (ARM-sjablonen) maken en valideren. In deze snelstart gebruikt u de extensie om helemaal zelf een ARM-sjabloon te maken. Daarbij ervaart u de extensiemogelijkheden, zoals ARM-sjabloonfragmenten, validatie, voltooiingen en ondersteuning van parameterbestanden.

Om deze quickstart af te ronden, moet u [Visual Studio Code](https://code.visualstudio.com/) met de [Azure Resource Manager-hulpprogramma-extensie](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) geïnstalleerd hebben. De [Azure CLI](/cli/azure/) of de [Azure PowerShell-module](/powershell/azure/new-azureps-module-az) moeten ook zijn geïnstalleerd en geverifieerd.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-arm-template"></a>Een ARM-sjabloon maken

Maak en open met Visual Studio Code een nieuw bestand met de naam *azuredeploy.json*. Voer `arm` in de code-editor in, waarmee Azure Resource Manager-fragmenten worden gestart voor het opbouwen van een ARM-sjabloon.

Selecteer `arm!` om een sjabloon te maken voor een implementatie van een Azure-resourcegroep.

![Afbeelding van Azure Resource Manager-opbouwfragmenten](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Met dit fragment maakt u de basisbouwstenen voor een ARM-sjabloon.

![Afbeelding van een volledig opgebouwd ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/2.png)

U ziet dat de taalmodus van Visual Studio Code is gewijzigd van *JSON* in *Azure Resource Manager-sjabloon*. De extensie bevat een taalserver die specifiek is voor ARM-sjablonen die ARM-sjabloonspecifieke validatie, voltooiing en andere taalservices bieden.

![Afbeelding van Azure Resource Manager als de Visual Studio Code-taalmodus](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Een Azure-resource toevoegen

De extensie bevat fragmenten voor veel Azure-resources. Deze fragmenten kunnen worden gebruikt om eenvoudig resources aan uw sjabloonimplementatie toe te voegen.

Plaats de cursor in het blok **resources** van de sjabloon, typ `storage` en selecteer het fragment *arm-storage*.

![Afbeelding van een resource die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Met deze actie wordt een opslagresource aan de sjabloon toegevoegd.

![Afbeelding van een Azure-opslagresource in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/5.png)

U kunt de **tab**-toets gebruiken om door de configureerbare eigenschapen in het opslagaccount te bladeren.

![Weergave van hoe de tab-toets kan worden gebruikt om te navigeren door de resourceconfiguratie](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Voltooiing en validatie

Een van de krachtigste mogelijkheden van de extensie is de integratie met Azure-schema's. Azure-schema's voorzien de extensie van mogelijkheden voor validatie en resourcebewuste voltooiing. We gaan het opslagaccount wijzigen om validatie en voltooiing in actie te zien.

Werk eerst het type opslagaccount bij naar een ongeldige waarde, zoals `megaStorage`. U ziet dat met deze actie een waarschuwing wordt gegenereerd die aangeeft dat `megaStorage` geen geldige waarde is.

![Afbeelding van een ongeldige opslagconfiguratie](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Als u de voltooiingsmogelijkheden wilt gebruiken, verwijdert u `megaStorage`, plaatst u de cursor binnen de dubbele aanhalingstekens en drukt u op `ctrl` + `space`. Met deze actie wordt een voltooiingslijst met geldige waarden weergegeven.

![Afbeelding van de automatische voltooiing van de extensie](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Sjabloonparameters toevoegen

Maak nu een parameter en gebruik deze om de naam van het opslagaccount op te geven.

Plaats de cursor in het parameterblok, voeg een regelterugloop toe, typ `"` en selecteer het `new-parameter`-fragment. Met deze actie wordt een algemene parameter aan de sjabloon toegevoegd.

![Afbeelding van een parameter die wordt toegevoegd aan de ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Wijzig de naam van de parameter in `storageAccountName` en de beschrijving in `Storage Account Name`.

![Afbeelding van de voltooide parameter in een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Namen van Azure-opslagaccounts hebben een minimumlengte van 3 en een maximumlengte van 24 tekens. Voeg zowel `minLength` als `maxLength` toe aan de parameter en geef de juiste waarden op.

![Afbeelding van minLength en maxLength die worden toegevoegd aan een ARM-sjabloonparameter](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Wijzig nu in de opslagresource de naam van de eigenschap om de parameter te gebruiken. Als u dit wilt doen, verwijdert u de huidige naam. Voer een dubbel aanhalingsteken en een vierkant haakje openen in `[` in. Hierdoor wordt een lijst met ARM-sjabloonfuncties geopend. Selecteer *parameters* in de lijst.

![Afbeelding van automatische voltooiing bij het gebruik van parameters in ARM-sjabloonresources](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Het invoeren van een enkel aanhalingsteken `'` binnen de ronde haakjes, levert een lijst op van alle parameters die in het sjabloon zijn gedefinieerd, in dit geval *storageAccountName*. Selecteer de parameter.

![Afbeelding van voltooide parameter in een ARM-sjabloonresource](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Een parameterbestand maken

Met een ARM-sjabloonparameterbestand kunt u omgevingsspecifieke parameterwaarden opslaan en deze waarden als een groep tijdens de implementatie doorgeven. U kunt bijvoorbeeld een parameterbestand hebben met waarden die specifiek zijn voor een testomgeving en een andere voor een productieomgeving.

Met deze extensie kunt u eenvoudig een parameterbestand maken op basis van uw bestaande sjablonen. Als u dit wilt doen, klikt u met de rechtermuisknop op de sjabloon in de code-editor en selecteert u `Select/Create Parameter File`.

![Afbeelding van het rechtermuisknopproces voor het maken van een parameterbestand op basis van een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Selecteer `New` > `All Parameters` > Selecteer een naam en locatie voor het parameterbestand.

![Afbeelding van de naam en het dialoogvenster Bestand opslaan bij het maken van een parameterbestand vanuit een ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Met deze actie wordt een nieuw parameterbestand gemaakt en toegewezen aan de sjabloon waaruit het is gemaakt. U kunt de huidige sjabloon-/parametertoewijzing zien en wijzigen in de statusbalk van Visual Studio Code terwijl de sjabloon is geselecteerd.

![Afbeelding van de sjabloon/parameterbestandstoewijzing in de statusbalk van Visual Studio Code.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nu het parameterbestand aan de sjabloon is toegewezen, valideert de extensie zowel de sjabloon als het parameterbestand samen. Als u deze validatie in de praktijk wilt zien, voegt u een waarde van twee tekens toe aan de parameter `storageAccountName` in het parameterbestand en slaat u het bestand op.

![Afbeelding van een ongeldige sjabloon vanwege een probleem met het parameterbestand](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Als u teruggaat naar de ARM-sjabloon ziet u dat er een fout is gegenereerd die aangeeft dat de waarde niet voldoet aan de criteria van de parameter.

![Afbeelding van een geldige ARM-sjabloon](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Wijzig de waarde in iets wat van toepassing is, sla het bestand op en ga terug naar de sjabloon. U ziet dat de parameterfout is opgelost.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Open de geïntegreerde Visual Studio Code-terminal met de toetsencombinatie `ctrl` + ```` ` ```` en gebruik de Azure CLI of Azure PowerShell-module om de sjabloon te implementeren.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de Azure-resources niet meer nodig zijn, gebruikt u de Azure CLI of Azure PowerShell-module om de quickstart-resourcegroep te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)
