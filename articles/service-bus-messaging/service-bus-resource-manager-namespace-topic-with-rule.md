---
title: Service Bus-onderwerp en-regel maken met behulp van Azure-sjabloon
description: Een Service Bus naam ruimte maken met een onderwerp, een abonnement en een regel met behulp van Azure Resource Manager sjabloon
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89077621"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Een Service Bus naam ruimte maken met een onderwerp, een abonnement en een regel met behulp van een Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt waarmee een Service Bus naam ruimte wordt gemaakt met een onderwerp, een abonnement en een regel (filter). In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe u de parameters definieert die bij de uitvoering van de implementatie zijn opgegeven. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen

Zie [Azure Resource Manager-sjablonen samenstellen][Authoring Azure Resource Manager templates] voor meer informatie over het maken van sjablonen.

Zie [Aanbevolen naamgevings regels voor Azure-resources][Recommended naming conventions for Azure resources]voor meer informatie over de procedures en patronen voor Azure resources-naamgevings conventies.

Voor de volledige sjabloon, zie de [Service Bus naam ruimte met onderwerp, abonnement en regel][Service Bus namespace with topic, subscription, and rule] sjabloon.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar om te downloaden en te implementeren.
> 
> * [Een Service Bus-naamruimte met een wachtrij en een autorisatieregel maken](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus-naamruimte met een wachtrij maken](service-bus-resource-manager-namespace-queue.md)
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus-naamruimte met een onderwerp en abonnement maken](service-bus-resource-manager-namespace-topic.md)
> 
> Als u de meest recente sjablonen wilt controleren, gaat u naar de galerie met [Azure Quick Start sjablonen][Azure Quickstart Templates] en zoekt u naar Service Bus.

## <a name="what-do-you-deploy"></a>Wat gaat u implementeren?

Met deze sjabloon implementeert u een Service Bus naam ruimte met een onderwerp, een abonnement en een regel (filter).

[Service Bus-onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) biedt een een-op-veel-communicatievorm in een *publiceren/abonneren*-patroon. Wanneer u onderwerpen en abonnementen gebruikt, communiceren onderdelen van een gedistribueerde toepassing niet rechtstreeks met elkaar. in plaats daarvan wisselen ze berichten uit via een onderwerp dat fungeert als een intermediair. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën ontvangt van berichten die naar het onderwerp zijn verzonden. Met een filter op het abonnement kunt u opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weer gegeven in een specifiek onderwerp-abonnement.

## <a name="what-are-rules-filters"></a>Wat zijn regels (filters)?

In veel gevallen moeten berichten met specifieke kenmerken op verschillende manieren worden verwerkt. Als u deze aangepaste verwerking wilt inschakelen, kunt u abonnementen configureren om berichten te vinden die specifieke eigenschappen hebben en vervolgens wijzigingen aan die eigenschappen uit te voeren. Hoewel Service Bus-abonnementen alle berichten zien die naar het onderwerp worden verzonden, kunt u alleen een subset van die berichten kopiëren naar de wachtrij voor virtuele abonnementen. Het wordt uitgevoerd met behulp van abonnements filters. Zie [regels en acties](service-bus-queues-topics-subscriptions.md#rules-and-actions)voor meer informatie over regels (filters).

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager definieert u de para meters voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameterwaarden bevat. Definieer een para meter voor die waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarin u implementeert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

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

### <a name="servicebusrulename"></a>serviceBusRuleName

De naam van de regel (filter) die is gemaakt in de Service Bus naam ruimte.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Hiermee maakt u een standaard Service Bus naam ruimte van het type **berichten**, met onderwerp en abonnement en regels.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Voor de JSON-syntaxis en-eigenschappen raadpleegt u [naam ruimten](/azure/templates/microsoft.servicebus/namespaces), [onderwerpen](/azure/templates/microsoft.servicebus/namespaces/topics), [abonnementen](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)en [regels](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van deze resources vindt u in de volgende artikelen:

* [Azure Service Bus beheren](service-bus-management-libraries.md)
* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
