---
title: Een onderwerp voor een Azure Service Bus-naamruimte maken met behulp van een sjabloon
description: 'Quickstart: Een Service Bus-naamruimte met een onderwerp en een abonnement maken met behulp van een Azure Resource Manager-sjabloon'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 706432c039bc38e9799828ae752d6ad0c92ffde3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704602"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Quickstart: Een Service Bus-naamruimte met een onderwerp en een abonnement maken met behulp van een Azure Resource Manager-sjabloon

In dit artikel wordt beschreven hoe u met een Azure Resource Manager-sjabloon een Service Bus-naamruimte en een onderwerp en een abonnement binnen die naamruimte maakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe u de parameters definieert die bij de uitvoering van de implementatie zijn opgegeven. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen

Zie [Azure Resource Manager-sjablonen samenstellen][Authoring Azure Resource Manager templates] voor meer informatie over het maken van sjablonen.

Zie de sjabloon [Service Bus-naamruimte met onderwerp en abonnement][Service Bus namespace with topic and subscription] voor de volledige sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar om te downloaden en te implementeren.
> 
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus-naamruimte met een wachtrij maken](service-bus-resource-manager-namespace-queue.md)
> * [Een Service Bus-naamruimte met een wachtrij en een autorisatieregel maken](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus-naamruimte met een onderwerp, een abonnement en een regel maken](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u op de meest recente sjablonen wilt controleren, gaat u naar de galerie [Azure-quickstart-sjablonen][Azure Quickstart Templates] en zoekt u naar **Service Bus**.

## <a name="what-do-you-deploy"></a>Wat gaat u implementeren?

Met deze sjabloon implementeert u een Service Bus-naamruimte met een onderwerp en een abonnement.

[Service Bus-onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) biedt een een-op-veel-communicatievorm in een *publiceren/abonneren*-patroon.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameterwaarden bevat. Definieer een parameter voor de waarden die variëren op basis van het project dat u wilt implementeren of op basis van de omgeving waarin u gaat implementeren. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

Met de sjabloon worden de volgende parameters gedefinieerd:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

De naam van de Service Bus-naamruimte die moet worden gemaakt.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

De naam van het onderwerp dat in de Service Bus-naamruimte wordt gemaakt.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

De naam van het abonnement dat in de Service Bus-naamruimte wordt gemaakt.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

De Service Bus-API-versie van de sjabloon.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resources om te implementeren

Hiermee maakt u een standaard-Service Bus-naamruimte van het type **Messaging**, met een onderwerp en een abonnement.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Zie [naamruimten](/azure/templates/microsoft.servicebus/namespaces), [onderwerpen](/azure/templates/microsoft.servicebus/namespaces/topics) en [abonnementen](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) voor de JSON-syntaxis en -eigenschappen.

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az deployment group create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Nu u resources hebt gemaakt en geïmplementeerd met behulp van Azure Resource Manager, kunt u in de volgende artikelen lezen hoe u deze resources kunt beheren:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
