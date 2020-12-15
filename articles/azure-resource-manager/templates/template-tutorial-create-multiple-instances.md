---
title: Meerdere resource-exemplaren maken
description: Leer hoe u een ARM-sjabloon (Azure Resource Manager) maakt om meerdere Azure-resource-instanties te maken.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 94509ba209e95eb9199ddd760529d47eb48bda10
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930795"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Zelfstudie: Meerdere resource-instanties maken met ARM-sjablonen

Leer hoe u een ARM-sjabloon (Azure Resource Manager) herhaaldelijk gebruikt om meerdere instanties van een Azure-resource te maken. In deze zelfstudie wijzigt u een sjabloon om drie instanties van het opslagaccount te maken.

![Diagram: Azure Resource Manager maakt meerdere instanties](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.
4. De resource Microsoft.Storage/storageAccounts is in de sjabloon gedefinieerd. Vergelijk de sjabloon met de [sjabloonverwijzing](/azure/templates/Microsoft.Storage/storageAccounts). Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
5. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Met de bestaande sjabloon wordt één opslagaccount gemaakt. U past de sjabloon aan om drie opslagaccounts te maken.

Breng vanuit Visual Studio Code de volgende vier wijzigingen aan:

![Azure Resource Manager maakt meerdere instanties](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Voeg een `copy`-element toe aan de resourcedefinitie van het opslagaccount. In het copy-element geeft u het aantal iteraties en een variabele voor deze lus op. Het aantal iteraties moet een positief geheel getal zijn en mag niet hoger zijn dan 800.
2. De functie `copyIndex()` retourneert de huidige iteratie in de lus. U gebruikt de index als het voorvoegsel van de naam. `copyIndex()` is gebaseerd op nul. Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie copyIndex(). Bijvoorbeeld *copyIndex(1)* .
3. Verwijder het element **variables**, want dit wordt niet meer gebruikt.
4. Verwijder het element **outputs**. Dit is niet langer nodig.

De voltooide sjabloon ziet er als volgt uit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Voor meer informatie over het maken van meerdere instanties raadpleegt u [Deploy multiple instances of a resource or property in ARM Templates](./copy-resources.md) (Meerdere instanties van een resource of eigenschap implementeren in ARM-sjablonen)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

1. Kies uw favoriete omgeving door in de linkerbovenhoek **PowerShell** of **Bash** (voor CLI) te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Bestand uploaden in Cloud Shell in de Azure-portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Na het uploaden van het bestand kunt u de **ls**-opdracht en de **cat**-opdracht uitvoeren om te controleren of het bestand is geüpload.

1. Voer vanuit Cloud Shell de volgende opdrachten uit. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Als u alle drie de opslagaccounts wilt weergeven, laat u de parameter --name weg:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

Vergelijk de namen van de opslagaccounts met de naamdefinitie in de sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal drie resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u meerdere instanties van opslagaccounts maakt.  In de volgende zelfstudie ontwikkelt u een sjabloon met meerdere resources en meerdere resourcetypen. Sommige resources hebben afhankelijke resources.

> [!div class="nextstepaction"]
> [Afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md)
