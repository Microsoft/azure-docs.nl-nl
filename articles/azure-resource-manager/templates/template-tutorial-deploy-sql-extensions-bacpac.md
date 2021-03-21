---
title: SQL BACPAC-bestanden met sjablonen importeren
description: Meer informatie over het gebruik van Azure SQL Database-extensies om SQL BACPAC-bestanden te importeren met ARM-sjablonen (Azure Resource Manager).
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1bd9f7408baf40791c31626ea9e87a73c65b999c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963994"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Zelfstudie: SQL BACPAC-bestanden met ARM-sjablonen importeren

Meer informatie over het gebruik van Azure SQL Database-extensies om een [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)-bestand te importeren met ARM-sjablonen (Azure Resource Manager). Implementatieartefacten zijn, naast de hoofdsjabloonbestanden, bestanden die vereist zijn om een implementatie te voltooien. Het BACPAC-bestand is een artefact.

In deze zelfstudie maakt u een sjabloon voor het implementeren van een [logische SQL-server](../../azure-sql/database/logical-servers.md) en één database, en importeert u een BACPAC-bestand. Voor informatie over het implementeren van extensies van virtuele Azure-machines met behulp van ARM-sjablonen, raadpleegt u [Zelfstudie: Extensies van virtuele machines implementeren met ARM-sjablonen](./template-tutorial-deploy-vm-extensions.md).

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
>
> * Een BACPAC-bestand voorbereiden.
> * Een quickstartsjabloon openen.
> * De sjabloon bewerken.
> * De sjabloon implementeren.
> * De implementatie controleren.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
* Gebruik een gegenereerd wachtwoord voor het serverbeheerdersaccount om de beveiliging te verbeteren. Hierna volgt een voorbeeld dat u kunt gebruiken om een wachtwoord te genereren:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

Een BACPAC-bestand wordt gedeeld in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Zie [Een database vanuit Azure SQL Database exporteren naar een BACPAC-bestand](../../azure-sql/database/database-export.md) om uw eigen bestand te maken. Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u de sjabloon later in de zelfstudie bijwerken.

Het BACPAC-bestand moet worden opgeslagen in een Azure Storage-account voordat het kan worden geïmporteerd met behulp van een ARM-sjabloon. Met het volgende PowerShell-script bereidt u het BACPAC-bestand voor met de volgende stappen:

* Het BACPAC-bestand downloaden.
* Een Azure Storage-account maken.
* Een blobcontainer in een opslagaccount maken.
* Het BACPAC-bestand uploaden naar de container.
* De sleutel van het opslagaccount en de URL van de blob weergeven.

1. Selecteer **Uitproberen** om de Azure Cloud Shell te openen. Plak het volgende PowerShell-script in het shell-venster.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Noteer de sleutel van het opslagaccount en de URL van het BACPAC-bestand. U hebt deze waarden nodig wanneer u de sjabloon gaat implementeren.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

De sjabloon die in deze zelfstudie wordt gebruikt, wordt opgeslagen in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Selecteer in Visual Studio Code **Bestand** > **Bestand openen**.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.

    Er worden twee resources gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Zie de [sjabloonverwijzing](/azure/templates/microsoft.sql/servers/databases).

        Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
1. Selecteer **Bestand** > **Opslaan als** om het bestand op uw lokale computer op te slaan als *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

1. Voeg twee extra parameters toe aan het einde van de sectie `parameters` om de sleutel van het opslagaccount en de BACPAC-URL in te stellen.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Voeg een komma toe na de afsluitende accolade (`}`) van de eigenschap `adminPassword`. Als u het JSON-bestand wilt opmaken vanuit Visual Studio Code, selecteert u Shift+Alt+F.

    Zie [Een BACPAC-bestand voorbereiden](#prepare-a-bacpac-file) om deze twee waarden op te halen.

1. Voeg twee extra resources aan de sjabloon toe.

    * Als u wilt toestaan dat de SQL Database-extensie BACPAC-bestanden kan importeren, moet u verkeer vanaf Azure-services toestaan. Voeg de volgende firewallregeldefinitie toe onder de serverdefinitie:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        De sjabloon ziet er als volgt uit:

        ![Sjabloon met definitie van firewallregel](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Voeg met de volgende JSON een SQL Database-extensieresource toe aan de databasedefinitie:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        De sjabloon ziet er als volgt uit:

        ![Sjabloon met SQL Database-extensie](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Zie de [verwijzing voor SQL Database-extensies](/azure/templates/microsoft.sql/servers/databases/extensions) voor meer informatie over de resourcedefinitie. Hier volgen enkele belangrijke elementen:

        * `dependsOn`: de extensieresource moet worden gemaakt nadat de database is gemaakt.
        * `storageKeyType`: Geef het type van de te gebruiken opslagsleutel op. De waarde kan `StorageAccessKey` of `SharedAccessKey` zijn. Gebruik in deze zelfstudie `StorageAccessKey`.
        * `storageKey`: Geef de sleutel op voor het opslagaccount waarin het BACPAC-bestand wordt opgeslagen. Als het type opslagsleutel `SharedAccessKey` is, moet deze worden voorafgegaan door een '?'.
        * `storageUri`: Geef de URL op van het BACPAC-bestand dat is opgeslagen in een opslagaccount.
        * `administratorLoginPassword`: het wachtwoord van de SQL-beheerder. Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).

De voltooide sjabloon ziet er als volgt uit:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>De sjabloon implementeren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Raadpleeg de sectie [De sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) voor de implementatieprocedure. Gebruik in plaats daarvan het volgende PowerShell-implementatiescript:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

U kunt dezelfde projectnaam gebruiken die u ook hebt gebruikt bij het voorbereiden van het BACPAC-bestand, zodat alle resources worden opgeslagen in dezelfde resourcegroep. Op deze manier is het eenvoudiger om resourcetaken, zoals het opschonen van de resources, te beheren. Als u dezelfde projectnaam gebruikt, kunt u ofwel de opdracht `New-AzResourceGroup` verwijderen uit het script ofwel ja (y) of nee (n) antwoorden wanneer u wordt gevraagd of u de bestaande resourcegroep wilt bijwerken.

Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).

## <a name="verify-the-deployment"></a>De implementatie controleren

U moet een extra firewallregel toevoegen om de server te benaderen vanaf uw clientcomputer. Raadpleeg [IP-firewallregels maken en beheren](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules) voor meer informatie.

Selecteer in Azure Portal de database in de geïmplementeerde resourcegroep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. U ziet dat twee tabellen in de database worden geïmporteerd.

![Query-editor (preview)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu in Azure Portal.
1. Voer de naam van de resourcegroep in het veld **Filter by name** in.
1. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resourcegroep.
1. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een server en een database geïmplementeerd en een BACPAC-bestand geïmporteerd. Raadpleeg voor meer informatie over het oplossen van problemen met sjabloonimplementatie:

> [!div class="nextstepaction"]
> [Problemen met ARM-sjabloonimplementatie oplossen](./template-tutorial-troubleshoot.md)
