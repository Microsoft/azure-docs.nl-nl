---
title: 'Zelf studie: JSON-sjabloon exporteren vanuit de Azure Portal for Bicep Development'
description: Meer informatie over het gebruik van een geëxporteerde JSON-sjabloon om uw Bicep-ontwikkeling te volt ooien.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632556"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Zelf studie: een geëxporteerde JSON-sjabloon gebruiken uit de Azure Portal

In deze zelfstudie reeks hebt u een Bicep-bestand gemaakt om een Azure-opslag account te implementeren. In de volgende twee zelfstudies voegt u een *App Service-plan* en een *website* toe. In plaats van helemaal nieuwe sjablonen te maken, leert u hoe u JSON-sjablonen exporteert vanuit het Azure Portal en hoe u voorbeeld sjablonen van de [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen gebruikt. U kunt deze sjablonen aanpassen voor eigen gebruik. In deze zelf studie wordt gekeken naar het exporteren van sjablonen en het aanpassen van het resultaat van uw Bicep-bestand. Dit neemt ongeveer **14 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over uitvoer](bicep-tutorial-add-outputs.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Dit Bicep-bestand werkt goed voor het implementeren van opslag accounts, maar mogelijk wilt u er meer resources aan toevoegen. U kunt een JSON-sjabloon uit een bestaande resource exporteren om de JSON voor die resource snel te verkrijgen. En converteer de JSON naar Bicep voordat u deze aan uw Bicep-bestand kunt toevoegen.

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

    ![Portal voor het exporteren van Resource Manager-sjablonen](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**. Het kan even duren om de resource te maken.

## <a name="export-template"></a>Sjabloon exporteren

Op dit moment ondersteunt de Azure Portal alleen het exporteren van JSON-sjablonen. Er zijn hulpprogram ma's die u kunt gebruiken om JSON-sjablonen te decompileren voor Bicep-bestanden.

1. Selecteer **Ga naar resource**.

    ![Ga naar resource](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecteer **Sjabloon exporteren**.

    ![Sjabloon voor het exporteren van Resource Manager-sjablonen](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   Met de functie voor het exporteren van sjablonen wordt de huidige status van een resource opgehaald en een sjabloon gegenereerd om deze te implementeren. Het exporteren van een sjabloon kan een handige manier zijn om snel de JSON te verkrijgen die u nodig hebt om een resource te implementeren.

1. De `Microsoft.Web/serverfarms` definitie en de parameter definitie zijn de onderdelen die u nodig hebt.

    ![Geëxporteerde Resource Manager-sjabloon](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > De geëxporteerde sjabloon is doorgaans uitgebreider dan u wellicht wilt bij het maken van een sjabloon. Het SKU-object in de geëxporteerde sjabloon heeft bijvoorbeeld vijf eigenschappen. Deze sjabloon werkt, maar u kunt gewoon de `name` eigenschap gebruiken. U kunt beginnen met de geëxporteerde sjabloon en deze vervolgens aanpassen aan uw vereisten.

1. Selecteer **Downloaden**.  Het gedownloade zip-bestand bevat een **template.js** en een **parameters.jsop**. Pak de bestanden uit.
1. Blader naar **https://bicepdemo.z22.web.core.windows.net/** , selecteer **Decompile** en open **template.jsop**. U krijgt de sjabloon in Bicep.

## <a name="revise-existing-bicep-file"></a>Bestaand Bicep-bestand herzien

De niet-nageleefde geëxporteerde sjabloon biedt u de meeste Bicep die u nodig hebt, maar u moet deze aanpassen voor uw Bicep-bestand. Let vooral op verschillen in para meters en variabelen tussen uw Bicep-bestand en het geëxporteerde Bicep-bestand. Het export proces kent uiteraard niet de para meters en variabelen die u al hebt gedefinieerd in uw Bicep-bestand.

In het volgende voor beeld ziet u de toevoegingen aan uw Bicep-bestand. Het bevat de geëxporteerde code plus enkele wijzigingen. Eerst wordt de naam van de parameter gewijzigd zodat deze overeenkomt met uw naamconventie. Ten tweede wordt uw locatieparameter gebruikt voor de locatie van het App Service-plan. Ten derde worden enkele van de eigenschappen waar de standaardwaarde goed is, verwijderd.

Kopieer het hele bestand en vervang het Bicep-bestand door de inhoud ervan.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

Gebruik Azure CLI of Azure PowerShell voor het implementeren van een Bicep-bestand.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

U hebt geleerd hoe u een JSON-sjabloon uit de Azure Portal exporteert, hoe u de JSON-sjabloon converteert naar een Bicep-bestand en hoe u de geëxporteerde sjabloon voor uw Bicep-ontwikkeling gebruikt. U kunt ook de Azure Quick Start-sjablonen gebruiken om de ontwikkeling van Bicep te vereenvoudigen.

> [!div class="nextstepaction"]
> [Azure-quickstart-sjablonen gebruiken](bicep-tutorial-quickstart-template.md)
