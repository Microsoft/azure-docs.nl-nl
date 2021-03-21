---
title: 'Zelfstudie: sjabloonfuncties toevoegen'
description: Voeg sjabloonfuncties toe aan uw ARM-sjabloon (Azure Resource Manager) om waarden samen te stellen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 52b5bd0650b3a069adc3ef7f101c48a4674deaab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107104"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Zelfstudie: Sjabloonfuncties toevoegen aan uw ARM-sjabloon

In deze zelfstudie leert u hoe u [sjabloonfuncties](template-functions.md) aan uw ARM-sjabloon (Azure Resource Manager) kunt toevoegen. U gebruikt functies om waarden dynamisch samen te stellen. Naast deze systeemsjabloonfuncties kunt u ook [door de gebruiker gedefinieerde functies](./template-user-defined-functions.md) maken. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over parameters](template-tutorial-add-parameters.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie [Hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

De locatie van het opslagaccount is in code vastgelegd als **US - oost**. Het is echter mogelijk dat u het opslagaccount moet implementeren in andere regio's. U loopt opnieuw tegen het probleem aan dat uw sjabloon te weinig flexibiliteit biedt. U kunt een parameter voor de locatie toevoegen, maar het zou geweldig zijn als de standaardwaarde gebruiksvriendelijker was dan alleen een in code vastgelegde waarde.

## <a name="use-function"></a>Functie gebruiken

Als u de vorige zelfstudie in deze serie hebt voltooid, hebt u al een functie gebruikt. Toen u `"[parameters('storageName')]"` toevoegde, hebt u de functie [parameters](template-functions-deployment.md#parameters) gebruikt. De vierkante haken geven aan dat de syntaxis tussen de haken een [sjabloonexpressie](template-expressions.md) is. In Resource Manager wordt de syntaxis opgelost in plaats van dat deze wordt behandeld als een letterlijke waarde.

Met functies kunt u flexibiliteit aan uw sjabloon toevoegen door dynamische waarden op te halen tijdens de implementatie. In deze zelfstudie gebruikt u een functie om de locatie op te halen van de resourcegroep die u voor de implementatie gebruikt.

In het volgende voorbeeld ziet u de benodigde wijzigingen voor het toevoegen van een parameter met de naam `location`. De standaardwaarde van de parameter roept de functie [resourceGroup](template-functions-resource.md#resourcegroup) aan. Deze functie retourneert een-object met informatie over de resourcegroep die wordt gebruikt voor de implementatie. Een van de eigenschappen van het object is een location-eigenschap. Wanneer u de standaardwaarde gebruikt, is de locatie van het opslagaccount hetzelfde als die van de resourcegroep. De resources in een resourcegroep hoeven niet dezelfde locatie te delen. U kunt zo nodig ook een andere locatie opgeven.

Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Sjabloon implementeren

In de vorige zelfstudies hebt u een opslagaccount gemaakt in US - oost, maar uw resourcegroep is gemaakt in VS - centraal. Voor deze zelfstudie wordt uw opslagaccount in dezelfde regio gemaakt als de resourcegroep. Gebruik de standaardwaarde voor de locatie, zodat u deze parameterwaarde niet hoeft op te geven. U moet een nieuwe naam opgeven voor het opslagaccount, omdat u een opslagaccount maakt op een andere locatie. Gebruik bijvoorbeeld **store2** als voorvoegsel in plaats van **store1**.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. U ziet dat er een opslagaccountresource is geïmplementeerd met dezelfde locatie als de resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een functie gebruikt bij het definiëren van de standaardwaarde voor een parameter. Ook in de volgende zelfstudies in deze serie gebruikt u functies. Aan het einde van de serie voegt u functies toe aan elke sectie van de sjabloon.

> [!div class="nextstepaction"]
> [Variabelen toevoegen](template-tutorial-add-variables.md)
