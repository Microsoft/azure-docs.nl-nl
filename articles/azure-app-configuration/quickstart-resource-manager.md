---
title: Een Azure App Configuration-archief maken met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon)
titleSuffix: Azure App Configuration
description: Meer informatie over het maken van een Azure App Configuration-archief met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 56505c95e65911cafbaaa403cd09332695439d97
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825671"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Quickstart: Een Azure App Configuration-archief maken met behulp van een ARM-sjabloon

In deze quickstart wordt het volgende beschreven:

- Een App Configuration-archief implementeren met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
- Sleutelwaarden maken in een App Configuration-archief met behulp van een ARM-sjabloon.
- Sleutelwaarden lezen in een App Configuration-archief met behulp van een ARM-sjabloon.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Er wordt een nieuw App Configuration-archief gemaakt met twee sleutelwaarden erin. Vervolgens wordt de functie `reference` gebruikt voor het uitvoeren van de waarden van de twee resources voor sleutelwaarden. Als u de waarde van de sleutel op deze manier leest, kan deze worden gebruikt op andere plaatsen in de sjabloon.

De quickstart maakt gebruik van het element `copy` voor het maken van meerdere exemplaren van de sleutelwaarde-resource. Zie [Resource-iteratie in ARM-sjablonen](../azure-resource-manager/templates/copy-resources.md)voor meer informatie over het element `copy`.

> [!IMPORTANT]
> Voor deze sjabloon is versie `2020-07-01-preview` of hoger van de resourceprovider van App Configuration vereist. Deze versie maakt gebruik van de functie `reference` om sleutelwaarden te lezen. De functie `listKeyValue` die is gebruikt voor het lezen van sleutelwaarden in de vorige versie is niet meer beschikbaar vanaf versie `2020-07-01-preview`.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores): een App Configuration-archief maken.
- [Microsoft.AppConfiguration/configurationStores/keyValues](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues): maak een sleutelwaarde in het App Configuration-archief.

> [!TIP]
> De naam van de `keyValues`-resource is een combinatie van sleutel en label. De sleutel en het label worden samengevoegd met het scheidingsteken `$`. Het label is optioneel. In het bovenstaande voorbeeld maakt de `keyValues`-resource met de naam `myKey` een sleutelwaarde zonder label.
>
> Met procentcodering, ook wel URL-codering genoemd, kunnen sleutels of labels tekens bevatten die niet zijn toegestaan in de resourcenamen van ARM-sjablonen. `%` is ook geen toegestaan teken, dus `~` wordt gebruikt in plaats daarvan. Voer de volgende stappen uit om een naam correct te coderen:
>
> 1. URL-codering toepassen
> 2. Vervang `~` door `~7E`
> 3. Vervang `%` door `~`
>
> Als u bijvoorbeeld een sleutelwaardepaar met sleutelnaam `AppName:DbEndpoint` en labelnaam `Test` wilt maken, moet de resourcenaam `AppName~3ADbEndpoint$Test` zijn.

> [!NOTE]
> In App Configuration is toegang tot sleutelwaardegegevens toegestaan via een [privékoppeling](concept-private-endpoint.md) vanuit het virtuele netwerk. Wanneer de functie is ingeschakeld, worden alle aanvragen voor uw App Configuration-gegevens via het openbare netwerk standaard geweigerd. Omdat de ARM-sjabloon buiten het virtuele netwerk wordt uitgevoerd, is toegang tot gegevens vanuit een ARM-sjabloon niet toegestaan. Als u toegang tot gegevens vanuit een ARM-sjabloon bij gebruik van een privékoppeling wilt toestaan, kunt u toegang via het openbare netwerk inschakelen met behulp van de volgende Azure CLI-opdracht. Het is van belang om rekening te houden met de beveiligingsimplicaties van het inschakelen van toegang via het openbare netwerk in dit scenario.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon maakt een nieuw App Configuration-archief met twee sleutelwaarden erin.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

U kunt de sjabloon ook implementeren met behulp van de volgende PowerShell-cmdlet. De sleutelwaarden vindt u in de uitvoer van de PowerShell-console.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Typ **App Configuration** in het zoekvak van de Azure-portal. Selecteer **App Configuration** uit de lijst.
1. Selecteer de zojuist gemaakte resource van de App Configuration.
1. Klik onder **Bewerkingen** op **Configuratieverkenner.**
1. Controleer of er twee sleutelwaarden bestaan.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, het App Configuration-archief en alle gerelateerde resources. Als u van plan bent om het App Configuration-archief in de toekomst te gebruiken, kunt u het verwijderen overslaan. Als u dit archief niet meer gaat gebruiken, verwijdert u alle resources die in deze quickstart zijn gemaakt door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel voor meer informatie over het maken van andere toepassingen met Azure App Configuration:

> [!div class="nextstepaction"]
> [Quickstart: Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)
