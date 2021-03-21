---
title: Zelf studie-Azure Resource Manager Bicep-bestanden maken & implementeren
description: Maak uw eerste Bicep-bestand voor de implementatie van Azure-resources. In de zelf studie vindt u informatie over de syntaxis van het Bicep-bestand en het implementeren van een opslag account.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594271"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren

In deze zelf studie leert u [Bicep](./bicep-overview.md). U ziet hoe u een Bicep-bestand maakt en dit implementeert in Azure. U vindt meer informatie over de structuur van het Bicep-bestand en de hulpprogram ma's die u nodig hebt voor het werken met Bicep-bestanden. Het voltooien van deze zelfstudie kost ongeveer **12 minuten**, maar de daadwerkelijke tijd is afhankelijk van hoeveel hulpprogramma’s u moet installeren.

Deze zelfstudie is de eerste van een reeks. Tijdens de voortgang van de reeks wijzigt u de stap voor het starten van Bicep-bestand door stap totdat u alle kern onderdelen van een Bicep-bestand hebt bekeken. Deze elementen zijn de bouw stenen voor veel complexere Bicep-bestanden. We hopen dat u aan het einde van de serie zeker weet dat u uw eigen Bicep-bestanden maakt en klaar bent om uw implementaties te automatiseren met Bicep-bestanden.

Zie [Bicep](./bicep-overview.md)als u meer wilt weten over de voor delen van het gebruik van Bicep en waarom u de implementatie moet automatiseren met Bicep-bestanden.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Hulpprogramma's installeren

Laten we beginnen door ervoor te zorgen dat u beschikt over de hulpprogram ma's die u nodig hebt voor het maken en implementeren van Bicep-bestanden. Installeer deze tools op uw lokale machine.

### <a name="editor"></a>Editor

Als u Bicep-bestanden wilt maken, hebt u een goede editor nodig. We raden Visual Studio code aan met de Bicep-extensie. Zie [Bicep-ontwikkel omgeving configureren](./bicep-install.md#development-environment)als u deze hulpprogram ma's wilt installeren.

### <a name="command-line-deployment"></a>Implementatie via opdrachtregels

U kunt Bicep-bestanden implementeren met behulp van Azure CLI of Azure PowerShell. Voor Azure CLI hebt u versie 2.20.0 of hoger nodig. voor Azure PowerShell hebt u versie 5.6.0 of hoger nodig. Zie voor installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)
- [Azure CLI installeren in macOS](/cli/azure/install-azure-cli-macos)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd moet u zich voor de eerste keer aanmelden. Bekijk [Aanmelden - PowerShell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) voor ondersteuning.

U bent klaar om te beginnen met het leren over Bicep.

## <a name="create-your-first-bicep-file"></a>Uw eerste Bicep-bestand maken

1. Open Visual Studio code met de uitbrei ding Bicep geïnstalleerd.
1. Selecteer **New File** in het menu **File** om een nieuw project te maken.
1. Selecteer **Save As** in het menu **File**.
1. Geef het bestand de naam _azuredeploy_ en selecteer de bestands extensie _Bicep_ . De volledige naam van het bestand is _azuredeploy. Bicep_.
1. Sla het bestand op uw werkstation op. Selecteer een pad dat u gemakkelijk kunt onthouden omdat u het pad later kunt opgeven bij het implementeren van het Bicep-bestand.
1. Kopieer en plak de volgende inhoud in het bestand:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    De Visual Studio Code-omgeving ziet er als volgt uit:

    ![Visual Studio code First Bicep-bestand](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    De resource declaratie bestaat uit vier onderdelen:

    - **resource**: sleutel woord.
    - **symbolische naam** (STG): een symbolische naam is een id voor het verwijzen naar de resource in uw Bicep-bestand. Het is niet wat de naam van de resource is wanneer deze wordt geïmplementeerd. De naam van de resource wordt gedefinieerd door de eigenschap **naam** .  Zie het vierde onderdeel in deze lijst. Om de zelf studies gemakkelijk te volgen, wordt **STG** gebruikt als de symbolische naam voor de bron van het opslag account in deze zelfstudie reeks. Zie [uitvoer toevoegen](./bicep-tutorial-add-outputs.md)als u wilt zien hoe u de symbolische naam kunt gebruiken om een volledige lijst met object eigenschappen te krijgen.
    - **resource type** ( Microsoft.Storage/storageAccounts@2019-06-01 ): het bestaat uit de resource provider (micro soft. Storage), resource type (Storage accounts) en apiVersion (2019-06-01). Elke resourceprovider publiceert eigen API-versies. Met andere woorden, elk type heeft een specifieke waarde. U kunt meer typen en apiVersions vinden voor verschillende Azure-resources van [arm-sjabloon verwijzing](/azure/templates/).
    - **Eigenschappen** (alles binnen = {...}): Dit zijn de specifieke eigenschappen die u wilt opgeven voor het opgegeven resource type. Dit zijn precies dezelfde eigenschappen die voor u beschikbaar zijn in een ARM-sjabloon. Elke resource heeft een `name` eigenschap. De meeste resources hebben ook de eigenschap `location`. Hiermee wordt de regio ingesteld waarin de resource wordt geïmplementeerd. De overige eigenschappen variëren per resourcetype en API-versie. Het is belangrijk om inzicht te krijgen in het verband tussen de API-versie en de beschikbare eigenschappen. Daarom gaan we er hier dieper op in.

        Voor dit opslag account kunt u de API-versie zien op [storage accounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). U ziet dat u niet alle eigenschappen aan uw Bicep-bestand hebt toegevoegd. Veel eigenschappen zijn optioneel. De resourceprovider `Microsoft.Storage` kan een nieuwe API-versie uitbrengen, maar de versie die u implementeert, hoeft niet te worden gewijzigd. U kunt deze versie blijven gebruiken en er zeker van zijn dat de resultaten van uw implementatie consistent zijn.

        Als u een oudere API-versie bekijkt, zoals [storage accounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), ziet u dat er een kleinere set eigenschappen beschikbaar is.

        Als u besluit de API-versie voor een resource te wijzigen, controleert u of u de eigenschappen voor die versie evalueert en past u het Bicep-bestand op de juiste wijze aan.

1. Vervang `{provide-unique-name}` met de accolades door `{}` een unieke naam voor het opslag account.

    > [!IMPORTANT]
    > De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers bevatten. De naam mag niet langer zijn dan 24 tekens. U kunt een naamgevingspatroon gebruiken zoals het gebruik van **store1** als voorvoegsel en vervolgens uw initialen en de datum van vandaag toevoegen. Uw naam kan er bijvoorbeeld uitzien als **store1abc09092019**.

    Het bedenken van een unieke naam voor een opslagaccount is niet eenvoudig en werkt niet goed voor het automatiseren van grote implementaties. Verderop in deze reeks zelf studies gebruikt u Bicep-functies waarmee u eenvoudiger een unieke naam kunt maken.

1. Sla het bestand op.

Gefeliciteerd, u hebt uw eerste Bicep-bestand gemaakt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meldt u aan met uw referenties voor Azure PowerShell/Azure CLI om aan de slag te gaan.

Selecteer de tabbladen in de volgende codesecties om te kiezen tussen Azure PowerShell en Azure CLI. De CLI-voorbeelden in dit artikel zijn geschreven voor de Bash-shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az login
```

---

Als u meerdere Azure-abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken. Vervang `[SubscriptionID/SubscriptionName]` en de vierkante haken `[]` door uw abonnementsgegevens:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Een resourcegroep maken

Wanneer u een Bicep-bestand implementeert, geeft u een resource groep op die de resources zal bevatten. Voordat u de opdracht voor de implementatie uitvoert, maakt u de resourcegroep met behulp van Azure CLI of Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

Bicep is een transparante abstractie over Azure Resource Manager sjablonen (ARM-sjablonen). Elk Bicep-bestand compileert naar een Standard ARM-sjabloon voordat deze wordt geïmplementeerd. U kunt uw Bicep-bestand compileren in een ARM-sjabloon voordat u het implementeert of uw Bicep-bestand rechtstreeks implementeert. Voor het implementeren van het Bicep-bestand gebruikt u Azure CLI of Azure PowerShell. Gebruik de resourcegroep die u hebt gemaakt. Geef de implementatie een naam zodat u deze gemakkelijk kunt herkennen in de implementatiegeschiedenis. Voor het gemak maakt u ook een variabele waarin het pad naar het Bicep-bestand wordt opgeslagen. Met deze variabele kunt u de implementatieopdrachten eenvoudiger uitvoeren omdat u het pad niet telkens opnieuw hoeft te typen. Vervang de accolades door het `{provide-the-path-to-the-bicep-file}` `{}` pad naar uw Bicep-bestand met de extensie naam _. Bicep_ .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

De implementatieopdracht retourneert resultaten. Zoek naar `ProvisioningState` om te controleren of de implementatie is geslaagd.

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkermenu.

1. Selecteer de resourcegroep die in de laatste procedure is geïmplementeerd. De standaardnaam is **myResourceGroup**. U ziet dat er geen resource is geïmplementeerd in de resourcegroep.

1. In de rechterbovenhoek van het overzicht wordt de status van de implementatie weergegeven. Selecteer **1 Geslaagd**.

   ![Implementatiestatus bekijken](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. U ziet nu de geschiedenis van de implementaties voor de resourcegroep. Selecteer **firstbicep**.

   ![Selecteer de implementatie](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Er wordt een samenvatting van de implementatie weergegeven. Er is één opslag account geïmplementeerd. Aan de linkerkant ziet u de invoer, uitvoer en de sjabloon die is gebruikt voor de implementatie.

   ![Implementatiesamenvatting bekijken](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de resourcegroep mogelijk verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudig Bicep-bestand gemaakt om te implementeren in Azure.  In de volgende zelf studies leert u hoe u para meters, variabelen, uitvoer en modules kunt toevoegen aan een Bicep-bestand. Deze functies zijn de bouw stenen voor veel complexere Bicep-bestanden.

> [!div class="nextstepaction"]
> [Parameters toevoegen](bicep-tutorial-add-parameters.md)
