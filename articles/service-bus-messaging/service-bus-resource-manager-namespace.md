---
title: Een Azure Service Bus naam ruimte maken met behulp van een sjabloon
description: Azure Resource Manager sjabloon gebruiken om een Service Bus Messa ging-naam ruimte te maken
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067389"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Een Service Bus naam ruimte maken met behulp van een Azure Resource Manager sjabloon

Meer informatie over het implementeren van een Azure Resource Manager sjabloon voor het maken van een Service Bus naam ruimte. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Zie [Azure Resource Manager-documentatie](../azure-resource-manager/index.yml)voor meer informatie over het maken van sjablonen.

De volgende sjablonen zijn ook beschikbaar voor het maken van Service Bus-naam ruimten:

* [Een Service Bus-naamruimte met een wachtrij maken](./service-bus-resource-manager-namespace-queue.md)
* [Een Service Bus-naamruimte met een onderwerp en abonnement maken](./service-bus-resource-manager-namespace-topic.md)
* [Een Service Bus-naamruimte met een wachtrij en een autorisatieregel maken](./service-bus-resource-manager-namespace-auth-rule.md)
* [Een Service Bus-naamruimte met een onderwerp, een abonnement en een regel maken](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-service-bus-namespace"></a>Een service bus-naam ruimte maken

In deze Snelstartgids gebruikt u een [bestaande resource manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) van [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen:

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Zie [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) voor meer informatie over sjabloonvoorbeelden.

Een service bus-naam ruimte maken door een sjabloon te implementeren:

1. Selecteer **Probeer het** uit het volgende code blok en volg de instructies om u aan te melden bij de Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    De naam van de resource groep is de naam van de service bus-naam ruimte waaraan **RG** is toegevoegd.

2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechtermuisknop op de shell-console en selecteer **Plakken**.

Het kan even duren om een Event Hub te maken.

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u de geïmplementeerde service bus-naam ruimte wilt zien, kunt u de resource groep openen vanuit het Azure Portal of het volgende Azure PowerShell script gebruiken. Als de Cloud shell nog steeds geopend is, hoeft u de eerste en tweede regel van het volgende script niet te kopiëren/uit te voeren.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell wordt gebruikt voor het implementeren van de sjabloon in deze zelf studie. Zie voor andere implementatie methoden voor sjablonen:

* Met [behulp van de Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Met [behulp van Azure cli](../azure-resource-manager/templates/deploy-cli.md).
* Met [behulp van rest API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als de Cloud shell nog steeds geopend is, hoeft u de eerste en tweede regel van het volgende script niet te kopiëren/uit te voeren.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Service Bus naam ruimte gemaakt. Zie de andere Quick starts voor informatie over het maken van wacht rijen, onderwerpen/abonnementen en het gebruik ervan:

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Aan de slag met Service Bus-onderwerpen](service-bus-dotnet-how-to-use-topics-subscriptions.md)