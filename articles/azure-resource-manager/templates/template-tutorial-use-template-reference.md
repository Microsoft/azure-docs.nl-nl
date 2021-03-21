---
title: Sjabloonverwijzing gebruiken
description: Gebruik de verwijzing voor ARM-sjablonen (Azure Resource Manager) om een sjabloon te maken.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584133"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Zelfstudie: De ARM-sjabloonverwijzing gebruiken

Meer informatie over het zoeken van de sjabloonschema-informatie en het gebruiken van deze informatie om ARM-sjablonen (Azure Resource Manager) te maken.

In deze zelfstudie gebruikt u een basissjabloon uit Azure-quickstart-sjablonen. Met behulp van het referentiemateriaal voor sjablonen kunt u de sjabloon aanpassen.

![Verwijzing naar Resource Manager-sjabloon voor implementatieopslagaccount](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * Inzicht in de sjabloon
> * De sjabloonverwijzing zoeken
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer in Visual Studio Code **Bestand** > **Bestand openen**.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. Selecteer **Bestand** > **Opslaan als** om het bestand op uw lokale computer op te slaan als _azuredeploy.json_.

## <a name="understand-the-schema"></a>Informatie over het schema

1. Vouw vanuit Visual Studio Code de sjabloon samen naar het hoofdniveau. U hebt de eenvoudigste structuur met de volgende elementen:

    ![Eenvoudigste structuur voor Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: geef de locatie van het JSON-schemabestand op dat de versie van de sjabloontaal beschrijft.
    * `contentVersion`: geef een waarde op voor dit element om belangrijke wijzigingen in de sjabloon vast te leggen.
    * `parameters`: geef de waarden op die worden geleverd wanneer de implementatie wordt uitgevoerd om resource-implementatie aan te passen.
    * `variables`: geef de waarden op die worden gebruikt als JSON-fragmenten in de sjabloon voor het vereenvoudigen van sjabloontaalexpressies.
    * `resources`: geef de resourcetypen op die worden geïmplementeerd of bijgewerkt in een resourcegroep.
    * `outputs`: geef de waarden op die worden geretourneerd na de implementatie.

1. Vouw `resources` uit. Er is een `Microsoft.Storage/storageAccounts`-resource gedefinieerd. De SKU-naam gebruikt een parameterwaarde. De parameter wordt `storageAccountType` genoemd.

    ![Definitie opslagaccount Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Vouw `parameters` uit om te zien hoe `storageAccountType` wordt gedefinieerd. De parameter heeft vier toegestane waarden. U vindt de andere toegestane waarden en vervolgens wijzigt u de parameterdefinitie.

    ![SKU's van resources van opslagaccount in Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>De sjabloonverwijzing zoeken

1. Blader naar [Azure-sjabloonverwijzing](/azure/templates/).
1. Voer in het vak **Filteren op titel** **opslagaccounts** in en selecteer de eerste **Opslagaccounts** onder **Referentie > Opslag**.

    ![Resource Manager-sjabloon verwijzing opslagaccount](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Een resourceprovider heeft doorgaans diverse API-versies:

    ![Resource Manager-sjabloon verwijzing opslagaccountversies](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecteer **Alle resources** onder **Opslag** in het linkerdeelvenster. Op deze pagina worden de resourcetypen en versies van de opslagresourceprovider vermeld. Het is raadzaam om de nieuwste API-versies te gebruiken voor de resourcetypen die zijn gedefinieerd in de sjabloon.

    ![Typeversies opslagaccount Resource Manager-sjabloonverwijzing](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecteer de nieuwste versie van het resourcetype `storageAccount`. De meest recente versie is **2019-06-01** wanneer dit artikel wordt geschreven. Zorg ervoor dat deze versie overeenkomt met de versie die wordt gebruikt voor de opslagaccountresource in uw sjabloon. Als u de API-versie bijwerkt, controleert u of de resourcedefinitie overeenkomt met de sjabloonverwijzing.

1. Op deze pagina vindt u de details van het resourcetype storageAccount. Het bevat bijvoorbeeld de toegestane waarden voor het **SKU-object**. Er zijn meer SKU's dan weergegeven in de quickstart-sjabloon die u eerder hebt geopend. U kunt de quickstart-sjabloon aanpassen zodat alle beschikbare opslagtypen worden toegevoegd.

    ![Resource Manager-sjabloon verwijzing opslagaccount SKU's](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg vanuit Visual Studio Code de extra opslagaccounttypen toe, zoals wordt weergegeven in de volgende schermopname:

![Resouces van opslagaccount in Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

1. Kies uw favoriete omgeving door in de linkerbovenhoek **PowerShell** of **Bash** (voor CLI) te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Bestand uploaden in Cloud Shell in de Azure-portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht `ls` en de opdracht `cat` gebruiken om te controleren of het bestand is geüpload.

1. Voer vanuit Cloud Shell de volgende opdrachten uit. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven.

   Wanneer u de sjabloon implementeert, geeft u de parameter `storageAccountType` op met een toegevoegde waarde, bijvoorbeeld **Standard_RAGRS**. De implementatie mislukt als u de oorspronkelijke quickstart-sjabloon gebruikt, omdat **Standard_RAGRS** geen toegestane waarde is.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
1. Voer de naam van de resourcegroep in het veld **Filter by name** in.
1. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
1. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u met sjabloonverwijzing een bestaande sjabloon kunt aanpassen. Voor informatie over hoe u meerdere exemplaren van een opslagaccount maakt, zie:

> [!div class="nextstepaction"]
> [Meerdere exemplaren maken](./template-tutorial-create-multiple-instances.md)
