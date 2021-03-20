---
title: Een Service Bus autorisatie regel maken met behulp van een Azure-sjabloon
description: Een Service Bus autorisatie regel voor een naam ruimte en een wachtrij maken met behulp van Azure Resource Manager sjabloon
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: b0ffe022f6c61cf7d06c510c4f8fa5c764d72b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067185"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Een Service Bus-autorisatieregel voor naamruimte en wachtrij maken met behulp van een Azure Resource Manager-sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt waarmee een [autorisatie regel](service-bus-authentication-and-authorization.md#shared-access-signature) voor een service bus naam ruimte en wachtrij wordt gemaakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe u de parameters definieert die bij de uitvoering van de implementatie zijn opgegeven. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie [Azure Resource Manager sjablonen ontwerpen][Authoring Azure Resource Manager templates]voor meer informatie over het maken van sjablonen.

Zie voor de volledige sjabloon de [Service Bus-autorisatie regel sjabloon][Service Bus auth rule template] op github.

> [!NOTE]
> De volgende Azure Resource Manager-sjablonen zijn beschikbaar om te downloaden en te implementeren.
> 
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus-naamruimte met een wachtrij maken](service-bus-resource-manager-namespace-queue.md)
> * [Een Service Bus-naamruimte met een onderwerp en abonnement maken](service-bus-resource-manager-namespace-topic.md)
> * [Een Service Bus-naamruimte met een onderwerp, een abonnement en een regel maken](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Als u op de meest recente sjablonen wilt controleren, gaat u naar de galerie [Azure-quickstart-sjablonen][Azure Quickstart Templates] en zoekt u naar **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Wat wilt u implementeren?

Met deze sjabloon implementeert u een Service Bus autorisatie regel voor een naam ruimte en een bericht entiteit (in dit geval een wachtrij).

Deze sjabloon maakt gebruik van [Shared Access Signature (SAS)](service-bus-sas.md) voor verificatie. Met SAS kunnen toepassingen worden geverifieerd bij Service Bus met behulp van een toegangs sleutel die is geconfigureerd op de naam ruimte of op de Messa ging-entiteit (wachtrij of onderwerp) waaraan specifieke rechten zijn gekoppeld. U kunt deze sleutel vervolgens gebruiken om een SAS-token te genereren dat clients in hun eigen verificatie kunnen gebruiken om zich te verifiëren bij Service Bus.

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameterwaarden bevat. U moet een para meter definiëren voor die waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarin u implementeert. Definieer geen para meters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende parameters.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

De naam van de Service Bus-naamruimte die moet worden gemaakt.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName

De naam van de autorisatie regel voor de naam ruimte.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

De naam van de wachtrij in de Service Bus naam ruimte.

```json
"serviceBusQueueName": {
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

Hiermee maakt u een standaard Service Bus naam ruimte van het type **Messa ging** en een service bus autorisatie regel voor de naam ruimte en de entiteit.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Zie [naam ruimten](/azure/templates/microsoft.servicebus/namespaces), [wacht rijen](/azure/templates/microsoft.servicebus/namespaces/queues)en [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules)voor de JSON-syntaxis en-eigenschappen.

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Volgende stappen

Nu u resources hebt gemaakt en geïmplementeerd met behulp van Azure Resource Manager, kunt u in de volgende artikelen lezen hoe u deze resources kunt beheren:

* [Service Bus met PowerShell beheren](./service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Vereenvoudigde Service Bus-verificatie en -autorisatie](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/