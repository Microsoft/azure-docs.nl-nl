---
title: Azure Service Bus-resources maken met behulp van sjablonen
description: Azure Resource Manager sjablonen gebruiken voor het automatiseren van het maken van Service Bus resources
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075253"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Service Bus-resources maken met behulp van Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u Service Bus-resources maakt en implementeert met behulp van Azure Resource Manager sjablonen, Power shell en de resource provider van Service Bus.

Azure Resource Manager Sjablonen helpen u de resources te definiëren die voor een oplossing moeten worden geïmplementeerd en om para meters en variabelen op te geven waarmee u waarden voor verschillende omgevingen kunt invoeren. De sjabloon wordt geschreven in JSON en bestaat uit expressies die u kunt gebruiken om waarden voor uw implementatie samen te stellen. Zie [structuur en syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md)voor gedetailleerde informatie over het schrijven van Azure Resource Manager sjablonen en een bespreking van de sjabloon indeling.

> [!NOTE]
> In de voor beelden in dit artikel ziet u hoe u Azure Resource Manager kunt gebruiken om een Service Bus naam ruimte en bericht entiteit (wachtrij) te maken. Voor andere sjabloon voorbeelden gaat u naar de [Galerie met Azure Quick Start sjablonen][Azure Quickstart Templates gallery] en zoekt u naar **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager-sjablonen

Deze Service Bus Azure Resource Manager sjablonen kunnen worden gedownload en geïmplementeerd. Klik op de volgende koppelingen voor meer informatie over elk item, met koppelingen naar de sjablonen op GitHub:

* [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
* [Een Service Bus-naamruimte met een wachtrij maken](service-bus-resource-manager-namespace-queue.md)
* [Een Service Bus-naamruimte met een onderwerp en abonnement maken](service-bus-resource-manager-namespace-topic.md)
* [Een Service Bus-naamruimte met een wachtrij en een autorisatieregel maken](service-bus-resource-manager-namespace-auth-rule.md)
* [Een Service Bus-naamruimte met een onderwerp, een abonnement en een regel maken](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementeren met PowerShell

In de volgende procedure wordt beschreven hoe u Power shell gebruikt om een Azure Resource Manager sjabloon te implementeren waarmee een standaardlaag Service Bus naam ruimte wordt gemaakt en een wachtrij binnen die naam ruimte. Dit voor beeld is gebaseerd op het [maken van een service bus naam ruimte met een wachtrij](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) sjabloon. De geschatte werk stroom is als volgt:

1. Power Shell installeren.
2. Maak de sjabloon en (optioneel) een parameter bestand.
3. Meld u in Power shell aan bij uw Azure-account.
4. Maak een nieuwe resource groep als deze niet bestaat.
5. De implementatie testen.
6. Stel, indien gewenst, de implementatie modus in.
7. De sjabloon implementeren.

Zie [resources implementeren met Azure Resource Manager sjablonen][Deploy resources with Azure Resource Manager templates]voor volledige informatie over het implementeren van Azure Resource Manager sjablonen.

### <a name="install-powershell"></a>PowerShell installeren

Installeer Azure PowerShell door de instructies in aan de slag te volgen [met Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Een sjabloon maken

Kloon de opslag plaats of kopieer de sjabloon [201-servicebus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) van github:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Een parameter bestand maken (optioneel)

Als u een optioneel parameter bestand wilt gebruiken, kopieert u het bestand [201-servicebus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Vervang de waarde van `serviceBusNamespaceName` door de naam van de service bus naam ruimte die u in deze implementatie wilt maken en vervang de waarde van `serviceBusQueueName` door de naam van de wachtrij die u wilt maken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Zie het artikel [para meters](../azure-resource-manager/templates/parameter-files.md) voor meer informatie.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Meld u aan bij Azure en stel het Azure-abonnement in

Voer de volgende opdracht uit vanuit een Power shell-prompt:

```powershell
Connect-AzAccount
```

U wordt gevraagd om u aan te melden bij uw Azure-account. Nadat u zich hebt aangemeld, voert u de volgende opdracht uit om uw beschik bare abonnementen weer te geven:

```powershell
Get-AzSubscription
```

Met deze opdracht wordt een lijst met beschik bare Azure-abonnementen geretourneerd. Kies een abonnement voor de huidige sessie door de volgende opdracht uit te voeren. Vervang door `<YourSubscriptionId>` de GUID voor het Azure-abonnement dat u wilt gebruiken:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>De resource groep instellen

Als u geen bestaande resource groep hebt, maakt u een nieuwe resource groep met de opdracht **New-AzResourceGroup** . Geef de naam op van de resource groep en de locatie die u wilt gebruiken. Bijvoorbeeld:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Als dit lukt, wordt een samen vatting van de nieuwe resource groep weer gegeven.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>De implementatie testen

Valideer uw implementatie door de cmdlet uit te voeren `Test-AzResourceGroupDeployment` . Wanneer u de implementatie test, geeft u de para meters op exact dezelfde manier op als bij het uitvoeren van de implementatie.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>De implementatie maken

Als u de nieuwe implementatie wilt maken, voert u de `New-AzResourceGroupDeployment` cmdlet uit en geeft u de benodigde para meters op wanneer u hierom wordt gevraagd. De para meters bevatten een naam voor uw implementatie, de naam van uw resource groep en het pad of de URL naar het sjabloon bestand. Als de **modus** parameter niet is opgegeven, wordt de standaard waarde **Incrementeel** gebruikt. Zie [incrementele en volledige implementaties](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie.

Met de volgende opdracht wordt u gevraagd om de drie para meters in het Power shell-venster:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Als u in plaats daarvan een parameter bestand wilt opgeven, gebruikt u de volgende opdracht:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

U kunt ook inline-para meters gebruiken wanneer u de cmdlet implementatie uitvoert. De opdracht is als volgt:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Als u een [volledige](../azure-resource-manager/templates/deployment-modes.md) implementatie wilt uitvoeren, stelt u de para meter voor de **modus** in op **voltooid**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>De implementatie controleren
Als de resources met succes zijn geïmplementeerd, wordt een samen vatting van de implementatie weer gegeven in het Power shell-venster:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Volgende stappen
U hebt nu de basis werk stroom en opdrachten voor het implementeren van een Azure Resource Manager sjabloon gezien. Bezoek de volgende koppelingen voor meer gedetailleerde informatie:

* [Overzicht van Azure Resource Manager][Azure Resource Manager overview]
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Resource typen van micro soft. ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
