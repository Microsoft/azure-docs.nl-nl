---
title: 'Zelfstudie: uitvoer aan een sjabloon toevoegen'
description: Voeg uitvoer toe aan uw ARM-sjabloon (Azure Resource Manager) om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7acfb168462354017168ee333ea0b1ecaea434c1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107121"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Zelfstudie: Uitvoer aan uw ARM-sjabloon toevoegen

In deze zelfstudie leert u hoe u een waarde uit uw ARM-sjabloon (Azure Resource Manager) kunt retourneren. U gebruikt uitvoer wanneer u een waarde van een geïmplementeerde resource nodig hebt. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over variabelen](template-tutorial-add-variables.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie de [hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Er wordt een opslagaccount geïmplementeerd, maar er wordt geen informatie over het opslagaccount geretourneerd. Mogelijk moet u de eigenschappen van een nieuwe resource vastleggen zodat deze later beschikbaar zijn ter referentie.

## <a name="add-outputs"></a>Uitvoer toevoegen

U kunt uitvoer gebruiken om waarden te retourneren uit de sjabloon. Het kan bijvoorbeeld nuttig zijn om de eindpunten voor uw nieuwe opslagaccount op te halen.

In het volgende voorbeeld ziet u de wijziging in de sjabloon die nodig is om een uitvoerwaarde toe te voegen. Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Er zijn enkele belangrijke dingen die u moet weten over de uitvoerwaarde die u hebt toegevoegd.

Het type van de geretourneerde waarde is ingesteld op `object`, wat betekent dat er een JSON-object wordt geretourneerd.

Er wordt gebruikgemaakt van de [reference](template-functions-resource.md#reference)-functie om de runtimestatus van het opslagaccount op te halen. Als u de runtimestatus van een resource wilt ophalen, geeft u de naam of id van een resource door. In dit geval gebruikt u de variabele die u hebt gebruikt voor het maken van de naam van het opslagaccount.

Ten slotte wordt de eigenschap `primaryEndpoints` uit het opslagaccount geretourneerd

## <a name="deploy-template"></a>Sjabloon implementeren

U bent klaar om de sjabloon te implementeren en de geretourneerde waarde te bekijken.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

In de uitvoer van de implementatieopdracht ziet u een object dat alleen op het volgende voorbeeld lijkt als het uitvoer in JSON-indeling betreft:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="review-your-work"></a>Uw werk controleren

U hebt veel gedaan in de afgelopen zes zelfstudies. Laten we even eens terugkijken naar wat u hebt gedaan. U hebt een sjabloon gemaakt met parameters die u eenvoudig kunt opgeven. De sjabloon kan in verschillende omgevingen worden gebruikt, omdat deze kan worden aangepast en de benodigde waarden dynamisch kunnen worden gemaakt. Er wordt ook informatie geretourneerd over het opslagaccount dat u in uw script kunt gebruiken.

Nu gaan we eens kijken naar de resourcegroep en de implementatiegeschiedenis.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. Afhankelijk van de stappen die u hebt uitgevoerd, hebt u een of meer opslagaccounts in de resourcegroep.
1. Als het goed is, hebt u ook een aantal geslaagde implementaties, die worden weergegeven in de geschiedenis. Selecteer deze koppeling.

   ![Implementaties selecteren](./media/template-tutorial-add-outputs/select-deployments.png)

1. U ziet al uw implementaties in de geschiedenis. Selecteer de implementatie met de naam **addoutputs**.

   ![Implementatiegeschiedenis weergeven](./media/template-tutorial-add-outputs/show-history.png)

1. U kunt de invoerwaarden bekijken.

   ![Invoerwaarden weergeven](./media/template-tutorial-add-outputs/show-inputs.png)

1. U kunt de uitvoerwaarden bekijken.

   ![Uitvoerwaarden weergeven](./media/template-tutorial-add-outputs/show-outputs.png)

1. U kunt de sjabloon bekijken.

   ![Sjabloon weergeven](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een retourwaarde aan de sjabloon toegevoegd. In de volgende zelfstudie leert u hoe u een sjabloon exporteert en gedeelten van de geëxporteerde sjabloon in uw sjabloon gebruikt.

> [!div class="nextstepaction"]
> [Een geëxporteerde sjabloon gebruiken](template-tutorial-export-template.md)
