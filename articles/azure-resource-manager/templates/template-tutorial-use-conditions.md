---
title: Voorwaarden gebruiken in sjablonen
description: Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden. Laat zien hoe u een nieuwe resource implementeert of een bestaande resource gebruikt.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696021"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Zelfstudie: Voorwaarden gebruiken in ARM-sjablonen

Leer hoe u Azure-resources kunt implementeren op basis van voorwaarden in een ARM-sjabloon (Azure Resource Manager).

In de zelfstudie [Resource-implementatievolgorde instellen](./template-tutorial-create-templates-with-dependent-resources.md) maakt u een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources, waaronder een opslagaccount. In plaats van elke keer een nieuw opslagaccount te maken, laat u gebruikers kiezen of ze een nieuw opslagaccount willen maken of een bestaand opslagaccount willen gebruiken. Om dit doel te bereiken, definieert u een extra parameter. Als de waarde van de parameter **new** is, wordt er een nieuw opslagaccount gemaakt. Anders wordt een bestaand opslagaccount met de gegeven naam gebruikt.

![Diagram: voorwaarden gebruiken in Resource Manager-sjablonen](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon aanpassen
> * De sjabloon implementeren
> * Resources opschonen

Deze zelfstudie behandelt alleen een basisscenario voor het gebruik van voorwaarden. Zie voor meer informatie:

* [Sjabloonbestandstructuur: voorwaarde](conditional-resource-deployment.md).
* [Een resource voorwaardelijk implementeren in een ARM-sjabloon](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Sjabloonfunctie: if](./template-functions-logical.md#if).
* [Vergelijkingsfuncties voor ARM-sjablonen](./template-functions-comparison.md)

Raadpleeg [Complexe cloudimplementaties beheren met behulp van geavanceerde functies voor ARM-sjablonen](/learn/modules/manage-deployments-advanced-arm-template-features/) voor een Microsoft Learn-module over de voorwaarden.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Snelstartsjablonen voor Azure is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer in Visual Studio Code **Bestand** > **Bestand openen**.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. Er worden zes resources gedefinieerd met de sjabloon:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Het is een goed idee om de sjabloonreferentie door te nemen voordat u een sjabloon aanpast.

1. Selecteer **Bestand** > **Opslaan als** om het bestand op uw lokale computer op te slaan als _azuredeploy.json_.

## <a name="modify-the-template"></a>De sjabloon aanpassen

Breng de volgende twee wijzigingen aan in de bestaande sjabloon:

* Voeg een parameter voor de opslagaccountnaam toe. Gebruikers kunnen een nieuwe opslagaccountnaam of een bestaande opslagaccountnaam opgeven.
* Voeg een nieuwe parameter toe met de naam `newOrExisting`. De implementatie gebruikt deze parameter om te bepalen of er een nieuw opslagaccount moet worden gemaakt of dat er een bestaand opslagaccount moet worden gebruikt.

Hier volgt de procedure waarmee de wijzigingen kunnen worden aangebracht:

1. Open _azuredeploy.json_ in Visual Studio Code.
1. Vervang de drie `variables('storageAccountName')` overal in de sjabloon door `parameters('storageAccountName')` .
1. Verwijder de volgende variabeledefinitie:

    ![Schermopname waarin de variabeledefinities die u moet verwijderen, zijn gemarkeerd.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Voeg de volgende twee parameters toe aan het begin van de parametersectie:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Druk op Alt+Shift+F om de sjabloon in Visual Studio Code in te delen.

    De bijgewerkte parameterdefinitie ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Voeg de volgende regel toe aan het begin van de definitie van het opslagaccount.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Met de voorwaarde wordt de waarde van de parameter `newOrExisting` gecontroleerd. Als de parameterwaarde **new** is, wordt er een opslagaccount gemaakt.

    De bijgewerkte definitie van het opslagaccount ziet er als volgt uit:

    ![Schermopname van de bijgewerkte definitie van het opslagaccount.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Werk de eigenschap `storageUri` van de resourcedefinitie van de virtuele machine bij met de volgende waarde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Deze wijziging is nodig als u een bestaand opslagaccount gebruikt onder een andere resourcegroep.

1. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

1. Kies uw favoriete omgeving door in de linkerbovenhoek **PowerShell** of **Bash** (voor CLI) te selecteren. U moet de shell opnieuw starten wanneer u overschakelt.

    ![Bestand uploaden in Cloud Shell in de Azure-portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht `ls` en de opdracht `cat` gebruiken om te controleren of het bestand is geüpload.

1. Gebruik het volgende PowerShell-script om de sjabloon te implementeren.

    > [!IMPORTANT]
    > De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers bevatten. De naam mag niet langer zijn dan 24 tekens. De naam van het opslagaccount is de projectnaam waaraan **store** is toegevoegd. Zorg ervoor dat de projectnaam en de gegenereerde opslagaccountnaam voldoen aan de vereisten voor opslagaccountnamen.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > De implementatie mislukt als `newOrExisting` de waarde **new** heeft, maar het opslagaccount met de opgegeven naam al bestaat.

Maak in dit geval een andere implementatie door `newOrExisting` in te stellen op **existing**, en de naam van een bestaand opslagaccount op te geven. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor informatie over het vooraf maken van een opslagaccount.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als u de resourcegroep wilt verwijderen, selecteert u **Uitproberen** om de Cloud Shell te openen. Plak het PowerShell-script door met de rechtermuisknop op het shell-venster te klikken en **Plakken** te selecteren.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gemaakt waarmee gebruikers kunnen kiezen tussen het maken van een nieuw opslagaccount en het gebruiken van een bestaand opslagaccount. Zie de volgende zelfstudie voor informatie over het ophalen van geheimen uit Azure Key Vault en hoe u deze geheimen als wachtwoorden gebruikt in de sjabloonimplementatie:

> [!div class="nextstepaction"]
> [Key Vault integreren in sjabloonimplementatie](./template-tutorial-use-key-vault.md)
