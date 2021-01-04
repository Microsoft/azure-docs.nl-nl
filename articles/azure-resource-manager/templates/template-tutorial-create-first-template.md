---
title: 'Zelfstudie: sjabloon maken en implementeren'
description: Uw eerste ARM-sjabloon (Azure Resource Manager) maken. In deze zelfstudie krijgt u meer informatie over de syntaxis van sjabloonbestanden en het implementeren van een opslagaccount.
author: mumian
ms.date: 09/28/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 191eacbc9cc66ccfb9b378cb5e8a90b4e0fb20e6
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107020"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren

Deze zelfstudie is een introductie tot Azure Resource Manager-sjablonen (ARM-sjablonen). U ziet hoe u een basissjabloon maakt en deze implementeert in Azure. U krijgt informatie over de structuur van de sjabloon en de hulpprogramma’s die u nodig hebt om met sjablonen te werken. Het voltooien van deze zelfstudie kost ongeveer **12 minuten**, maar de daadwerkelijke tijd is afhankelijk van hoeveel hulpprogramma’s u moet installeren.

Deze zelfstudie is de eerste van een reeks. Naarmate u vordert in de reeks, past u de basissjabloon stap voor stap aan totdat alle kernonderdelen van een ARM-sjabloon zijn behandeld. Deze elementen zijn de bouwstenen voor veel complexere sjablonen. We hopen dat u aan het einde van de reeks in staat bent uw eigen sjablonen te maken en klaar bent om uw implementaties te automatiseren met sjablonen.

Zie [Overzicht van ARM-sjablonen](overview.md) als u meer wilt weten over de voordelen van het gebruik van sjablonen en het automatiseren van implementaties met sjablonen.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="get-tools"></a>Hulpprogramma's installeren

Laten we er allereerst voor zorgen dat u de nodige hulpprogramma's hebt om sjablonen te maken en implementeren. Installeer deze tools op uw lokale machine.

### <a name="editor"></a>Editor

Sjablonen zijn JSON-bestanden. Om sjablonen te maken hebt u een goede JSON-editor nodig. We raden Visual Studio Code met de extensie Resource Manager Tools aan. Als u deze hulpprogramma's wilt installeren, raadpleegt u [quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Implementatie via opdrachtregels

U hebt ook Azure PowerShell of Azure CLI nodig om het sjabloon te implementeren. Als u Azure CLI gebruikt, moet u de nieuwste versie hebben. Zie voor installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)
- [Azure CLI installeren in macOS](/cli/azure/install-azure-cli-macos)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd moet u zich voor de eerste keer aanmelden. Bekijk [Aanmelden - PowerShell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) voor ondersteuning.

> [!IMPORTANT]
> Als u Azure CLI gebruikt, moet u beschikken over versie 2.6 of hoger. De opdrachten die in deze zelfstudie worden weergegeven, werken niet als u een eerdere versie gebruikt. U kunt de geïnstalleerde versie controleren met de opdracht: `az --version`.

U bent nu klaar om te beginnen met het leren over sjablonen.

## <a name="create-your-first-template"></a>Uw eerste sjabloon maken

1. Open Visual Studio Code met de extensie Resource Manager Tools geïnstalleerd.
1. Selecteer **New File** in het menu **File** om een nieuw project te maken.
1. Selecteer **Save As** in het menu **File**.
1. Geef het bestand de naam _azuredeploy_ en selecteer de bestandsextensie _JSON_. De volledige naam van het bestand is _azuredeploy.json_.
1. Sla het bestand op uw werkstation op. Selecteer een pad dat u gemakkelijk kunt onthouden, omdat u het pad later bij het implementeren van de sjabloon moet opgeven.
1. Kopieer en plak de volgende JSON in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    De Visual Studio Code-omgeving ziet er als volgt uit:

    ![ARM-sjabloon, eerste Visual Studio Code-sjabloon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Met deze sjabloon worden geen resources geïmplementeerd. We beginnen met een lege sjabloon, zodat u vertrouwd kunt raken met de stappen voor het implementeren van een sjabloon. Dit minimaliseert de kans op fouten.

    Het JSON-bestand bevat de volgende elementen:

    - `$schema`: Geeft de locatie van het JSON-schemabestand aan. Het schemabestand beschrijft de eigenschappen die beschikbaar zijn in een sjabloon. In het schema zijn bijvoorbeeld `resources` gedefinieerd als een van de geldige eigenschappen voor een sjabloon. U hoeft zich geen zorgen te maken over dat de datum van het schema 1-4-2019 is. Deze versie van het schema is actueel en bevat alle recente functies. De schemadatum is niet gewijzigd omdat er sinds de introductie geen wijzigingen zijn aangebracht die fouten zouden kunnen veroorzaken.
    - `contentVersion`: Geeft de versie van de sjabloon aan (bv. 1.0.0.0). U kunt een willekeurige waarde voor dit element opgeven. Gebruik deze waarde als u belangrijke wijzigingen in uw sjabloon wilt documenteren. Wanneer u resources implementeert met de sjabloon, kan deze waarde worden gebruikt om ervoor te zorgen dat de juiste sjabloon wordt gebruikt.
    - `resources`: Bevat de resources die u wilt implementeren of bijwerken. Dit is momenteel leeg, maar hier voegt u later resources toe.

1. Sla het bestand op.

Gefeliciteerd, u hebt uw eerste sjabloon gemaakt.

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

Wanneer u een sjabloon implementeert, geeft u een resourcegroep op die de resources zal bevatten. Voordat u de opdracht voor de implementatie uitvoert, maakt u de resourcegroep met behulp van Azure CLI of Azure PowerShell.

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

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt Azure PowerShell of Azure CLI gebruiken om het sjabloon te implementeren. Gebruik de resourcegroep die u hebt gemaakt. Geef de implementatie een naam zodat u deze gemakkelijk kunt herkennen in de implementatiegeschiedenis. Maak voor het gemak ook een variabele waarin u het pad naar het sjabloonbestand opslaat. Met deze variabele kunt u de implementatieopdrachten eenvoudiger uitvoeren omdat u het pad niet telkens opnieuw hoeft te typen. Vervang `{provide-the-path-to-the-template-file}` en de accolades `{}` door het pad naar het sjabloonbestand.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

De implementatieopdracht retourneert resultaten. Zoek naar `ProvisioningState` om te controleren of de implementatie is geslaagd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Inrichtingsstatus van PowerShell-implementatie](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

![Inrichtingsstatus van Azure CLI-implementatie](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkermenu.

1. Selecteer de resourcegroep die in de laatste procedure is geïmplementeerd. De standaardnaam is **myResourceGroup**. U ziet dat er geen resource is geïmplementeerd in de resourcegroep.

1. In de rechterbovenhoek van het overzicht wordt de status van de implementatie weergegeven. Selecteer **1 Geslaagd**.

   ![Implementatiestatus bekijken](./media/template-tutorial-create-first-template/deployment-status.png)

1. U ziet nu de geschiedenis van de implementaties voor de resourcegroep. Selecteer **blanktemplate**.

   ![Selecteer de implementatie](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Er wordt een samenvatting van de implementatie weergegeven. In dit geval is er niet veel te zien, omdat er geen resources zijn geïmplementeerd. Later in deze reeks kan het handig zijn om de samenvatting in de implementatiegeschiedenis te bekijken. Aan de linkerkant ziet u de invoer, uitvoer en de sjabloon die is gebruikt voor de implementatie.

   ![Implementatiesamenvatting bekijken](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de resourcegroep mogelijk verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt een eenvoudige sjabloon voor een implementatie naar Azure gemaakt. In de volgende zelfstudie voegt u een opslagaccount toe aan de sjabloon en implementeert u het in uw resourcegroep.

> [!div class="nextstepaction"]
> [Resource toevoegen](template-tutorial-add-resource.md)
