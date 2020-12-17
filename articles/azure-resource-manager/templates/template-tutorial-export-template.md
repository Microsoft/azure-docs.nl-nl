---
title: 'Zelfstudie: sjabloon exporteren uit de Azure-portal'
description: Meer informatie over het gebruik van een geëxporteerde sjabloon om de ontwikkeling van uw sjabloon te voltooien.
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106917"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Zelfstudie: Uit de Azure-portal geëxporteerde sjabloon gebruiken

In deze serie zelfstudies hebt u een sjabloon gemaakt voor het implementeren van een Azure-opslagaccount. In de volgende twee zelfstudies voegt u een *App Service-plan* en een *website* toe. In plaats van totaal nieuwe sjablonen te maken, leert u hoe u sjablonen uit de Azure-portal exporteert en hoe u voorbeeldsjablonen van [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) kunt gebruiken. U kunt deze sjablonen aanpassen voor eigen gebruik. Deze zelfstudie is gericht op het exporteren van sjablonen en het aanpassen van het resultaat voor uw sjabloon. Dit neemt ongeveer **14 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over uitvoer](template-tutorial-add-outputs.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie de [hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Deze sjabloon werkt goed voor het implementeren van opslagaccounts, maar mogelijk wilt u er meer resources aan toevoegen. U kunt een sjabloon uit een bestaande resource exporteren om de JSON voor die resource snel te verkrijgen.

## <a name="create-app-service-plan"></a>Een App Service-plan maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Een resource maken**.
1. Voer **App Service-plan** in **Marketplace doorzoeken** en selecteer **App Service-plan**.  Selecteer niet **App Service-plan (klassiek)**
1. Selecteer **Maken**.
1. Voer het volgende in:

    - **Abonnement**: selecteer uw Azure-abonnement.
    - **Resourcegroep**: Selecteer **Nieuwe maken** en geef een naam op. Geef een andere resourcegroepsnaam op dan degene die u al in deze serie zelfstudies hebt gebruikt.
    - **Naam**: voer een naam in voor het App service-plan.
    - **Besturingssysteem**: selecteer **Linux**.
    - **Regio**: selecteer een Azure-locatie. Bijvoorbeeld **VS - centraal**.
    - **Prijscategorie**: als u kosten wilt besparen, wijzigt u de SKU in **Basic B1** (onder Dev/Test).

    ![Portal voor het exporteren van Resource Manager-sjablonen](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**. Het kan even duren om de resource te maken.

## <a name="export-template"></a>Sjabloon exporteren

1. Selecteer **Ga naar resource**.

    ![Ga naar resource](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecteer **Sjabloon exporteren**.

    ![Sjabloon voor het exporteren van Resource Manager-sjablonen](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Met de functie voor het exporteren van sjablonen wordt de huidige status van een resource opgehaald en een sjabloon gegenereerd om deze te implementeren. Het exporteren van een sjabloon kan een handige manier zijn om snel de JSON te verkrijgen die u nodig hebt om een resource te implementeren.

1. Bekijk de definitie van `Microsoft.Web/serverfarms` en de parameterdefinitie in de geëxporteerde sjabloon. U hoeft deze secties niet te kopiëren. U kunt gewoon deze geëxporteerde sjabloon gebruiken als voorbeeld van de manier waarop u deze resource aan uw sjabloon wilt toevoegen.

    ![Geëxporteerde Resource Manager-sjabloon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> De geëxporteerde sjabloon is doorgaans uitgebreider dan u wellicht wilt bij het maken van een sjabloon. Het SKU-object in de geëxporteerde sjabloon heeft bijvoorbeeld vijf eigenschappen. Deze sjabloon werkt, maar u kunt ook gewoon de eigenschap `name` (naam) gebruiken. U kunt beginnen met de geëxporteerde sjabloon en deze vervolgens aanpassen aan uw vereisten.

## <a name="revise-existing-template"></a>Bestaande sjabloon herzien

De geëxporteerde sjabloon biedt u het merendeel van de benodigde JSON, maar u moet deze aanpassen voor uw sjabloon. Let vooral op verschillen in parameters en variabelen tussen uw sjabloon en de geëxporteerde sjabloon. Voor het exportproces is uiteraard niet bekend welke parameters en variabelen u al hebt gedefinieerd in uw sjabloon.

In het volgende voorbeeld worden de toevoegingen aan uw sjabloon uitgelicht. Het bevat de geëxporteerde code plus enkele wijzigingen. Eerst wordt de naam van de parameter gewijzigd zodat deze overeenkomt met uw naamconventie. Ten tweede wordt uw locatieparameter gebruikt voor de locatie van het App Service-plan. Ten derde worden enkele van de eigenschappen waar de standaardwaarde goed is, verwijderd.

Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt Azure CLI of Azure PowerShell gebruiken om een sjabloon te implementeren.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. De resourcegroep bevat een opslagaccount en een App Service-plan.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een sjabloon uit de Azure-portal exporteert en hoe u de geëxporteerde sjabloon verder kunt ontwikkelen. U kunt ook de Azure-quickstart-sjablonen gebruiken om de ontwikkeling van sjablonen te vereenvoudigen.

> [!div class="nextstepaction"]
> [Azure-quickstart-sjablonen gebruiken](template-tutorial-quickstart-template.md)
