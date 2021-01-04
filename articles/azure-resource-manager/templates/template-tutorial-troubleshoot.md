---
title: Problemen met implementaties oplossen
description: Leer hoe u implementaties van ARM-sjablonen (Azure Resource Manager) bewaakt en problemen ermee oplost. Hiermee worden activiteitenlogboeken en implementatiegeschiedenis weergegeven.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589114"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Zelfstudie: Problemen met ARM-sjabloonimplementatie oplossen

Leer hoe u problemen met implementaties van ARM-sjablonen (Azure Resource Manager) oplost. In deze zelfstudie stelt u twee fouten in een sjabloon in en leert u hoe u de activiteitenlogboeken en de implementatiegeschiedenis kunt gebruiken om de problemen op te lossen.

Er zijn twee typen fouten die gerelateerd zijn aan de implementatie van sjablonen:

- **Validatiefouten** ontstaan door scenario's en kunnen vóór de implementatie worden vastgesteld. Ze bevatten syntaxisfouten in de sjabloon of proberen resources te implementeren waarmee uw abonnementquota worden overschreden.
- **Fouten bij de implementatie** ontstaan als gevolg van voorwaarden die tijdens het implementatieproces optreden. Bijvoorbeeld wanneer deze toegang proberen te krijgen tot een resource die parallel wordt geïmplementeerd.

Beide typen fouten retourneren een foutcode die u gebruikt om de problemen met de implementatie op te lossen. Beide typen fouten worden weergegeven in het activiteitenlogboek. Validatiefouten worden echter niet weergegeven in de implementatiegeschiedenis omdat de implementatie niet is gestart.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> - Een sjabloon met problemen maken
> - Validatiefouten oplossen
> - Implementatiefouten oplossen
> - Resources opschonen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

- Visual Studio Code met de extensie Resource Manager Tools. Zie [Quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Een sjabloon met problemen maken

Open een sjabloon met de naam [Een standaardopslagaccount maken](https://azure.microsoft.com/resources/templates/101-storage-account-create/) vanuit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/) en stel twee sjabloonproblemen in.

1. Selecteer in Visual Studio Code **Bestand** > **Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Wijzig de `apiVersion`-regel in de volgende regel:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` is een ongeldig elementnaam. Dit is een validatiefout.
    - De API-versie moet `"2018-07-01"` zijn.  Dit is een implementatiefout.

5. Selecteer **Bestand** > **Opslaan als** om het bestand op uw lokale computer op te slaan als _azuredeploy.json_.

## <a name="troubleshoot-the-validation-error"></a>De validatiefout oplossen

Raadpleeg de sectie [De sjabloon implementeren](template-tutorial-create-multiple-instances.md#deploy-the-template) om de sjabloon te implementeren.

U ontvangt een foutbericht vanuit de shell dat vergelijkbaar is met:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Het foutbericht geeft aan dat er een probleem is met `apiVersion1`.

Gebruik Visual Studio Code om het probleem te verhelpen door `apiVersion1` te wijzigen in `apiVersion`. Sla de sjabloon vervolgens op.

## <a name="troubleshoot-the-deployment-error"></a>De implementatiefout oplossen

Raadpleeg de sectie [De sjabloon implementeren](template-tutorial-create-multiple-instances.md#deploy-the-template) om de sjabloon te implementeren.

U ontvangt een foutbericht vanuit de shell dat vergelijkbaar is met:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

De implementatiefout kunt u vinden in de Microsoft Azure-portal met behulp van de volgende procedure:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Open de resourcegroep door **Resourcegroepen** en vervolgens de naam van de resourcegroep te selecteren. U ziet **1 mislukt** onder **Implementatie**.

    ![Schermopname met de mislukte implementatie gemarkeerd.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selecteer **Foutdetails**.

    ![Schermopname met de koppeling Foutdetails gemarkeerd.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Het foutbericht is hetzelfde als het foutbericht dat eerder werd weergegeven:

    ![Schermopname met de foutdetails.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

U kunt de fout ook in de activiteitenlogboeken vinden:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Controleren** > **Activiteitenlogboek**.
3. Gebruik de filters om het logboek te vinden.

    ![Zelfstudie over het oplossen van problemen met Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Gebruik Visual Studio Code om het probleem te verhelpen en implementeer de sjabloon vervolgens opnieuw.

Zie [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](common-deployment-errors.md) voor een lijst met veelvoorkomende fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u fouten met implementaties van ARM-sjablonen kunt oplossen.  Zie [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](common-deployment-errors.md) voor meer informatie.
