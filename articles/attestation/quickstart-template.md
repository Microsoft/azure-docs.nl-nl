---
title: Een Azure Attestation-certificaat maken met behulp van de Azure Resource Manager-sjabloon
description: Meer informatie over het maken van een Azure Attestation-certificaat met behulp van de Azure Resource Manager-sjabloon.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834242"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Quickstart: Een Azure Attestation-provider maken met een ARM-sjabloon

[Microsoft Azure Attestation](overview.md) is een oplossing voor het afleiden van Trusted Execution Environments (TEE’s). Deze quickstart is gericht op het implementeren van een ARM-sjabloon (Azure Resource Manager-sjabloon) voor het maken van een beleid voor Microsoft Azure Attestation.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Azure-resources die in de sjabloon zijn gedefinieerd:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en de sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden.

    Gebruik de standaardwaarde om de attestation-provider te maken, tenzij er iets anders is aangegeven.

    - **Naam attestation-provider**: Selecteer een naam voor uw Azure Attestation-provider.
    - **Locatie**: Selecteer een locatie. Bijvoorbeeld **VS - centraal**.
    - **Tags**: Selecteer een locatie. Bijvoorbeeld **VS - centraal**.

1. Selecteer **Aankoop**. Nadat de attestation-resource is geïmplementeerd, ontvangt u een melding.

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure-portal gebruiken om de Attestation-resource te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Azure Attestations bouwen voort op deze quickstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor wordt ook de attestation-resource verwijderd. De resourcegroep verwijderen met behulp van Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een attestation-resource gemaakt met behulp van een ARM-sjabloon en de implementatie gevalideerd. Zie [Overzicht van Azure Attestation](overview.md) voor meer informatie over Azure Attestation.
