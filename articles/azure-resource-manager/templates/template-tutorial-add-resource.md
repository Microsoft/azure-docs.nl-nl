---
title: 'Zelfstudie: een resource aan een sjabloon toevoegen'
description: Hierin worden de stappen voor het maken van uw eerste ARM-sjabloon (Azure Resource Manager) beschreven. U krijgt meer informatie over de syntaxis van sjabloonbestanden en het implementeren van een opslagaccount.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106968"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Zelfstudie: Een resource aan uw ARM-sjabloon toevoegen

In de [vorige zelfstudie](template-tutorial-create-first-template.md) hebt u geleerd hoe u een lege ARM-sjabloon (Azure Resource Manager) maakt en deze implementeert. U kunt nu een daadwerkelijke resource implementeren. In deze zelfstudie voegt u een opslagaccount toe. Deze zelfstudie neemt ongeveer **9 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [inleidende zelfstudie over sjablonen](template-tutorial-create-first-template.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie de [hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="add-resource"></a>Resource toevoegen

Als u een opslagaccountdefinitie aan de bestaande sjabloon wilt toevoegen, bekijkt u de gemarkeerde JSON in het volgende voorbeeld. In plaats van secties van de sjabloon te kopiëren, kopieert u het hele bestand en vervangt u uw sjabloon door de inhoud ervan.

Vervang `{provide-unique-name}` en de accolades `{}` door een unieke naam voor het opslagaccount.

> [!IMPORTANT]
> De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers bevatten. De naam mag niet langer zijn dan 24 tekens. U kunt een naamgevingspatroon gebruiken zoals het gebruik van **store1** als voorvoegsel en vervolgens uw initialen en de datum van vandaag toevoegen. Uw naam kan er bijvoorbeeld uitzien als **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Het bedenken van een unieke naam voor een opslagaccount is niet eenvoudig en werkt niet goed voor het automatiseren van grote implementaties. Verderop in deze serie zelfstudies gebruikt u sjabloonfuncties waarmee u gemakkelijker een unieke naam kunt maken.

## <a name="resource-properties"></a>Resource-eigenschappen

U vraagt zich misschien af hoe u de eigenschappen voor elk resourcetype kunt vinden. U kunt de resourcetypen die u wilt implementeren, vinden in het [referentiemateriaal voor ARM-sjablonen](/azure/templates/).

Elke resource die u implementeert, heeft ten minste de volgende drie eigenschappen:

- `type`: Het type resource. Deze waarde is een combinatie van de naamruimte van de resourceprovider en het resourcetype, zoals `Microsoft.Storage/storageAccounts`.
- `apiVersion`: De versie van de REST API die moet worden gebruikt voor het maken van de resource. Elke resourceprovider publiceert eigen API-versies. Met andere woorden, elk type heeft een specifieke waarde.
- `name`: De naam van de resource.

De meeste resources hebben ook de eigenschap `location`. Hiermee wordt de regio ingesteld waarin de resource wordt geïmplementeerd.

De overige eigenschappen variëren per resourcetype en API-versie. Het is belangrijk om inzicht te krijgen in het verband tussen de API-versie en de beschikbare eigenschappen. Daarom gaan we er hier dieper op in.

In deze zelfstudie hebt u een opslagaccount aan de sjabloon toegevoegd. U kunt zien dat de API-versie [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts) is. Merk op dat u niet alle eigenschappen aan uw sjabloon hebt toegevoegd. Veel eigenschappen zijn optioneel. De resourceprovider `Microsoft.Storage` kan een nieuwe API-versie uitbrengen, maar de versie die u implementeert, hoeft niet te worden gewijzigd. U kunt deze versie blijven gebruiken en er zeker van zijn dat de resultaten van uw implementatie consistent zijn.

Als u een oudere API-versie bekijkt, zoals [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), ziet u dat er minder eigenschappen beschikbaar zijn.

Als u besluit de API-versie voor een resource te wijzigen, moet u de eigenschappen voor die versie evalueren en de sjabloon dienovereenkomstig aanpassen.

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt de sjabloon implementeren om het opslagaccount te maken. Geef een andere naam voor uw implementatie op zodat u deze eenvoudig kunt vinden in de geschiedenis.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

Er kunnen twee implementatiefouten kunnen optreden:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` is geen geldige naam voor een opslagaccount. Een opslagaccountnaam moet tussen 3 en 24 tekens lang zijn en mag alleen bestaan uit getallen en kleine letters.

    Vervang `{provide-unique-name}` (met inbegrip van de accolades) in de sjabloon door een unieke opslagaccountnaam. Zie [Resource toevoegen](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` wordt al gebruikt.

    Probeer in de sjabloon een andere naam voor het opslagaccount.

Deze implementatie duurt langer dan de implementatie van de lege sjabloon omdat het opslagaccount is gemaakt. Het kan een minuut duren, maar gewoonlijk gaat het sneller.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. U ziet dat een opslagaccount is geïmplementeerd.
1. U ziet dat het implementatielabel nu de volgende tekst bevat: **Implementaties: 2 geslaagd**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon voor het implementeren van een Azure-opslagaccount gemaakt. In de volgende zelfstudies leert u hoe u parameters, variabelen, resources en uitvoer toevoegt aan een sjabloon. Deze items zijn de bouwstenen voor veel complexere sjablonen.

> [!div class="nextstepaction"]
> [Parameters toevoegen](template-tutorial-add-parameters.md)
