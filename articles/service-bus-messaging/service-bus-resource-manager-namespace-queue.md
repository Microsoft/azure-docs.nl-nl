---
title: Een Azure Service Bus-naamruimte en -wachtrij maken met behulp van Azure-sjabloon
description: 'Quickstart: Een Service Bus-naamruimte en een wachtrij maken met behulp van een Azure Resource Manager-sjabloon'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 69cffb6000df9d8b058d92231c130ea8a601e6d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88660628"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Quickstart: Een Service Bus-naamruimte en een wachtrij maken met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u met een Azure Resource Manager-sjabloon (ARM-sjabloon) een Service Bus-naamruimte en een wachtrij binnen die naamruimte maakt. In het artikel wordt uitgelegd hoe u kunt opgeven welke resources worden geïmplementeerd en hoe u de parameters definieert die bij de uitvoering van de implementatie zijn opgegeven. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Onder meer de volgende resources worden in de sjabloon gedefinieerd:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> De volgende ARM-sjablonen zijn beschikbaar om te downloaden en te implementeren.
>
> * [Een Service Bus-naamruimte met een wachtrij en een autorisatieregel maken](service-bus-resource-manager-namespace-auth-rule.md)
> * [Een Service Bus-naamruimte met een onderwerp en abonnement maken](service-bus-resource-manager-namespace-topic.md)
> * [Een Service Bus-naamruimte maken](service-bus-resource-manager-namespace.md)
> * [Een Service Bus-naamruimte met een onderwerp, een abonnement en een regel maken](service-bus-resource-manager-namespace-topic-with-rule.md)

Zie [Azure quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) voor meer informatie over sjablonen

## <a name="deploy-the-template"></a>De sjabloon implementeren

Met deze sjabloon implementeert u een Service Bus-naamruimte met een wachtrij.

[Service Bus-wachtrijen](service-bus-queues-topics-subscriptions.md#queues) bieden het FIFO-principe (first in, first out) voor de berichtbezorging naar een of meer concurrerende gebruikers.

Klik op de volgende knop om de implementatie automatisch uit te voeren: Maak een nieuwe resourcegroep voor de implementatie, zodat u deze later eenvoudig kunt opschonen.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Selecteer **Meldingen** bovenaan de pagina om de implementatiestatus weer te geven. Wacht tot de implementatie is voltooid. Selecteer vervolgens **Ga naar resourcegroep** in het meldingsbericht om naar de pagina te navigeren voor de resourcegroep die de Service Bus-naamruimte bevat. 

    ![Melding van implementatie](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Controleer of uw Service Bus-naamruimte wordt weergeven in de lijst met resources. 

    ![Resourcegroep - naamruimte](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecteer de naamruimte in de lijst om de pagina **Service Bus-naamruimte** weer te geven. 

## <a name="clean-up-resources"></a>Resources opschonen

1. Navigeer in de Azure-portal naar de pagina **Resourcegroep** voor uw resourcegroep.
2. Selecteer **Resourcegroep verwijderen** uit de taakbalk. 
3. Typ de naam van de resourcegroep en selecteer **Verwijderen**. 

    ![Resourcegroep - verwijderen](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Volgende stappen

Zie het volgende onderwerp waarin wordt uitgelegd hoe u een autorisatieregel maakt voor de naamruimte/wachtrij:

[Een Service Bus-autorisatieregel voor naamruimte en wachtrij maken met behulp van een ARM-sjabloon](service-bus-resource-manager-namespace-auth-rule.md)

Meer informatie over het beheren van deze resources vindt u in de volgende artikelen:

* [Service Bus met PowerShell beheren](service-bus-manage-with-ps.md)
* [Service Bus-resources beheren met Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md