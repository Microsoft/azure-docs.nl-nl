---
title: Delen buiten uw eigen organisatie (ARM-sjabloon) - quickstart over Azure Data Share
description: In deze quickstart vindt u meer informatie over het delen van gegevens met klanten en partners met behulp van Azure Data Share en een Azure Resource Manager-sjabloon (ARM-sjabloon).
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487684"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Quickstart: gegevens delen met behulp van Azure Data Share en een ARM-sjabloon

Meer informatie over het instellen van een nieuwe Azure Data Share vanuit een Azure-opslagaccount met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). En begin met het delen van uw gegevens met klanten en partners buiten uw Azure-organisatie. Zie [Ondersteunde gegevensopslag in Azure Data Share](./supported-data-stores.md) voor een lijst met ondersteunde gegevensopslag.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.DataShare/accounts](/azure/templates/microsoft.datashare/accounts)
* [Microsoft.DataShare/accounts/shares](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts/shares/dataSets](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft.DataShare/accounts/shares/invitations](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

Met deze sjabloon worden de volgende taken uitgevoerd:

* Maak een opslagaccount en een container die wordt gebruikt als Data Share-gegevensbron.
* Een Data Share-account en een gegevensshare maken.
* Maak een roltoewijzing om de rol Storage Blob Data Reader toe te kennen aan de resource voor het delen van brongegevens. Zie [Rollen en vereisten voor Azure Data Share](./concepts-roles-permissions.md).
* Voeg een gegevensset toe aan de gegevensshare.
* Voeg ontvangers toe aan de gegevensshare.
* Schakel een schema voor momentopnamen in voor de gegevensshare.

Deze sjabloon is gemaakt voor leerdoeleinden. In de praktijk hebt u meestal enkele gegevens in een bestaand opslagaccount. U moet de roltoewijzing maken voordat u een sjabloon of een script uitvoert om de gegevensset te maken. Soms wordt het volgende foutbericht weergegeven wanneer u de sjabloon implementeert:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Dit komt doordat de implementatie de gegevensset wil maken voordat de Azure-roltoewijzing is voltooid. Ondanks het foutbericht is de implementatie mogelijk geslaagd. U kunt nog steeds door [geïmplementeerde resources](#review-deployed-resources) bladeren.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Typ of selecteer de volgende waarden:

    * **Abonnement**: selecteer een Azure-abonnement dat wordt gebruikt om de gegevensshare en de andere resources te maken.
    * **Resourcegroep**: selecteer **Nieuwe maken** om een nieuwe resourcegroep te maken of een bestaande resourcegroep te selecteren.
    * **Locatie**: selecteer een locatie voor de resourcegroep.
    * **Projectnaam**: voer een projectnaam in.  De projectnaam wordt gebruikt om resourcenamen te genereren.  Zie de variabele definities in de vorige sjabloon.
    * **Locatie**: selecteer een locatie voor de resources.  U kunt dezelfde locatie gebruiken voor de resourcegroep.
    * **Uitnodigingse-mail**: voer het Azure e-mailadres van de ontvanger van de gegevensshare in.  E-mailalias werkt niet.

    Gebruik de standaardwaarde voor de overige instellingen.
1. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open het Data Share-account dat u hebt gemaakt.
1. Selecteer **Shares verzenden** in het linkermenu.  U ziet het vermelde opslagaccount.
1. Selecteer het opslagaccount.  Onder **Details** ziet u de synchronisatie-instelling die u in de sjabloon hebt geconfigureerd.

    ![Synchronisatie-instellingen voor Azure Data Share-opslagaccount](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Selecteer bovenaan **Uitnodigingen**. U ziet het e-mailadres dat u hebt opgegeven bij het implementeren van de sjabloon. De **status** is **In behandeling**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure-gegevensshare maakt en ontvangers uitnodigt. Ga door naar de zelfstudie voor het [accepteren en ontvangen van gegevens](subscribe-to-data-share.md) voor meer informatie over hoe een gegevensgebruiker een gegevensshare kan accepteren en ontvangen.
