---
title: Zelf studie-functies toevoegen aan Azure Resource Manager Bicep-bestanden
description: Voeg functies toe aan uw Bicep-bestanden om waarden te maken.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633152"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Zelf studie: functies toevoegen aan Azure Resource Manager Bicep-bestand

In deze zelf studie leert u hoe u [sjabloon functies](template-functions.md) kunt toevoegen aan uw Bicep-bestand. U gebruikt functies om waarden dynamisch samen te stellen. Naast deze systeemsjabloonfuncties kunt u ook [door de gebruiker gedefinieerde functies](./template-user-defined-functions.md) maken. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over parameters](bicep-tutorial-add-parameters.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

De locatie van het opslagaccount is in code vastgelegd als **US - oost**. Het is echter mogelijk dat u het opslagaccount moet implementeren in andere regio's. Er wordt nog een probleem opgetreden bij het oplossen van uw Bicep-bestand. U kunt een parameter voor de locatie toevoegen, maar het zou geweldig zijn als de standaardwaarde gebruiksvriendelijker was dan alleen een in code vastgelegde waarde.

## <a name="use-function"></a>Functie gebruiken

Met functies kunt u flexibiliteit toevoegen aan uw Bicep-bestand door waarden dynamisch op te halen tijdens de implementatie. In deze zelfstudie gebruikt u een functie om de locatie op te halen van de resourcegroep die u voor de implementatie gebruikt.

In het volgende voor beeld ziet u de wijzigingen voor het toevoegen van een para meter met de naam `location` . De standaardwaarde van de parameter roept de functie [resourceGroup](template-functions-resource.md#resourcegroup) aan. Deze functie retourneert een-object met informatie over de resourcegroep die wordt gebruikt voor de implementatie. Een van de eigenschappen van het object is een location-eigenschap. Wanneer u de standaardwaarde gebruikt, is de locatie van het opslagaccount hetzelfde als die van de resourcegroep. De resources in een resourcegroep hoeven niet dezelfde locatie te delen. U kunt zo nodig ook een andere locatie opgeven.

Kopieer het hele bestand en vervang het Bicep-bestand door de inhoud ervan.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

In de vorige zelfstudies hebt u een opslagaccount gemaakt in US - oost, maar uw resourcegroep is gemaakt in VS - centraal. Voor deze zelfstudie wordt uw opslagaccount in dezelfde regio gemaakt als de resourcegroep. Gebruik de standaardwaarde voor de locatie, zodat u deze parameterwaarde niet hoeft op te geven. U moet een nieuwe naam opgeven voor het opslagaccount, omdat u een opslagaccount maakt op een andere locatie. Gebruik bijvoorbeeld **store2** als voorvoegsel in plaats van **store1**.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

In deze zelfstudie hebt u een functie gebruikt bij het definiëren van de standaardwaarde voor een parameter. Ook in de volgende zelfstudies in deze serie gebruikt u functies. Aan het einde van de reeks voegt u functies toe aan elke sectie van het Bicep-bestand.

> [!div class="nextstepaction"]
> [Variabelen toevoegen](bicep-tutorial-add-variables.md)
