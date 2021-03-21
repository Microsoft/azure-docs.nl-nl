---
title: 'Quickstart voor Azure: een Batch-account maken met een Azure Resource Manager-sjabloon'
description: In deze quickstart wordt uitgelegd hoe u een Batch-account maakt met behulp van een ARM-sjabloon.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: subject-armqs
ms.openlocfilehash: fc6f1537b32c8ced2cfc2bf09f16106f791001fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106577"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Quickstart: Een Batch-account maken met een ARM-sjabloon

U hebt een Batch-account nodig om rekenresources (pools met rekenknooppunten) en Batch-taken te maken. Als u een Azure Storage-account koppelt aan uw Batch-account, kunt u toepassingen implementeren en invoer- en uitvoergegevens voor de meeste workloads uit de praktijk opslaan. In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) gebruikt om een Batch-account te maken, inclusief opslag. Nadat u deze snelstartgids hebt voltooid, begrijpt u de belangrijkste principes van de Batch-service en bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-abonnement nodig.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Hiermee maakt u een opslagaccount.
- [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts): Hiermee maakt u een Batch-account.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon maakt een Azure Batch-account en een opslagaccount.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden.

   ![Resource Manager-sjabloon, Batch-account maken, portalimplementatie](media/quick-create-template/batch-template.png)

   - **Abonnement**: selecteer een Azure-abonnement.
   - **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik op **OK**.
   - **Locatie**: selecteer een locatie. Bijvoorbeeld **VS - centraal**.
   - **Naam van Batch-account**: Laat de standaardwaarde staan.
   - **Opslagaccountsku**: selecteer een type opslagaccount. Bijvoorbeeld **Standard_LRS**.
   - **Locatie**: wijzig de standaardlocatie zodat de resources zich op dezelfde locatie bevinden als de resourcegroep.
   - Ik ga akkoord met de bovenstaande voorwaarden: **Selecteren**.

1. Selecteer **Aankoop**.

Na enkele minuten ziet u een melding dat het Batch-account is gemaakt.

In dit voorbeeld wordt Azure Portal gebruikt voor de implementatie van de sjabloon. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

U kunt de implementatie valideren in Azure Portal door te navigeren naar de resourcegroep die u hebt gemaakt. Controleer in het scherm **Overzicht** dat het Batch-account en het opslagaccount aanwezig zijn.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met volgende [zelfstudies](./tutorial-parallel-dotnet.md), kunt u deze resources intact laten. Als u ze niet meer nodig hebt, kunt u [de resourcegroep verwijderen](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), waardoor ook het Batch-account en het opslagaccount die u hebt gemaakt, worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Batch-account en een opslagaccount gemaakt. Voor meer informatie over Azure Batch gaat u naar de Azure Batch-zelfstudies.

> [!div class="nextstepaction"]
> [Azure Batch-zelfstudies](./tutorial-parallel-dotnet.md)
