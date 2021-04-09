---
title: 'Zelf studie: uitvoer toevoegen aan Azure Resource Manager Bicep-bestand'
description: Voeg uitvoer toe aan uw Bicep-bestand om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594305"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Zelf studie: uitvoer toevoegen aan Azure Resource Manager Bicep-bestand

In deze zelf studie leert u hoe u een waarde uit uw implementatie kunt ophalen. U gebruikt uitvoer wanneer u een waarde van een geïmplementeerde resource nodig hebt. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over variabelen](bicep-tutorial-add-variables.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Er wordt een opslagaccount geïmplementeerd, maar er wordt geen informatie over het opslagaccount geretourneerd. Mogelijk moet u de eigenschappen van een nieuwe resource vastleggen zodat deze later beschikbaar zijn ter referentie.

## <a name="add-outputs"></a>Uitvoer toevoegen

U kunt uitvoer gebruiken om waarden van de implementatie te retour neren. Het kan bijvoorbeeld nuttig zijn om de eindpunten voor uw nieuwe opslagaccount op te halen.

In het volgende voor beeld ziet u de wijziging in uw Bicep-bestand om een uitvoer waarde toe te voegen. Kopieer het hele bestand en vervang het Bicep-bestand door de inhoud ervan.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Er zijn enkele belangrijke dingen die u moet weten over de uitvoerwaarde die u hebt toegevoegd.

Het type van de geretourneerde waarde is ingesteld op `object` , wat betekent dat er een sjabloon object wordt geretourneerd.

Als u de `primaryEndpoints` eigenschap van het opslag account wilt ophalen, gebruikt u de symbolische naam van het opslag account. De functie automatisch aanvullen van de Visual Studio-code geeft u een volledige lijst met eigenschappen:

   ![Eigenschappen van symbolische naam Bicep van Visual Studio code](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

U bent klaar om het Bicep-bestand te implementeren en de geretourneerde waarde te bekijken.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

U hebt veel gedaan in de afgelopen zes zelfstudies. Laten we even eens terugkijken naar wat u hebt gedaan. U hebt een Bicep-bestand gemaakt met para meters die u gemakkelijk kunt opgeven. Het Bicep-bestand kan in verschillende omgevingen worden gebruikt omdat het kan worden aangepast en de benodigde waarden dynamisch kunnen worden gemaakt. Er wordt ook informatie geretourneerd over het opslagaccount dat u in uw script kunt gebruiken.

Nu gaan we eens kijken naar de resourcegroep en de implementatiegeschiedenis.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. Afhankelijk van de stappen die u hebt uitgevoerd, hebt u een of meer opslagaccounts in de resourcegroep.
1. Als het goed is, hebt u ook een aantal geslaagde implementaties, die worden weergegeven in de geschiedenis. Selecteer deze koppeling.

   ![Implementaties selecteren](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. U ziet al uw implementaties in de geschiedenis. Selecteer de implementatie met de naam **addoutputs**.

   ![Implementatiegeschiedenis weergeven](./media/bicep-tutorial-add-outputs/show-history.png)

1. U kunt de invoerwaarden bekijken.

   ![Invoerwaarden weergeven](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. U kunt de uitvoerwaarden bekijken.

   ![Uitvoerwaarden weergeven](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. U kunt de JSON-sjabloon controleren.

   ![Sjabloon weergeven](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een retour waarde toegevoegd aan het Bicep-bestand. In de volgende zelf studie leert u hoe u een JSON-sjabloon exporteert en delen van die geëxporteerde sjabloon in uw Bicep-bestand gebruikt.

> [!div class="nextstepaction"]
> [Een geëxporteerde sjabloon gebruiken](bicep-tutorial-export-template.md)
