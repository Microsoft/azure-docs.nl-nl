---
title: 'Quickstart: Een event hub maken met behulp van PowerShell - Azure Event Hubs'
description: In deze snelstart wordt beschreven hoe u een event hub maakt met behulp van Azure PowerShell en vervolgens gebeurtenissen verzendt en ontvangt met behulp van .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88933950"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Quickstart: Een event hub maken met behulp van Azure PowerShell

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze snelstart maakt u een Event Hub met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account][] voordat u begint.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of hoger.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal gebruikt, moet u de nieuwste versie van PowerShell uitvoeren om deze snelstart te kunnen voltooien. Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische verzameling van Azure-resources. U hebt een resourcegroep nodig om een Event Hub te maken. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio VS - oost. Vervang `myResourceGroup` door de naam van de resourcegroep die u wilt gebruiken:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Zodra uw resourcegroep is gemaakt, maakt u een Event Hubs-naamruimte in die resourcegroep. Een Event Hubs-naamruimte biedt een unieke Fully Qualified Domain Name waarin uw Event Hub kunt maken. Vervang `namespace_name` door een unieke naam voor de naamruimte:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Een Event Hub maken

Nu u een ​​Event Hubs-naamruimte hebt, maakt u een Event Hub binnen die naamruimte:  
Toegestane periode voor `MessageRetentionInDays` ligt tussen één en zeven dagen.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gefeliciteerd! U hebt Azure PowerShell gebruikt om een ​​Event Hubs-naamruimte en een Event Hub binnen die naamruimte te maken. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Event Hubs-naamruimte gemaakt en voorbeeldtoepassingen gebruikt om gebeurtenissen te verzenden en ontvangen vanuit uw Event Hub. Zie de zelfstudies **Gebeurtenissen verzenden en ontvangen** voor stapsgewijze instructies voor het verzenden van gebeurtenissen naar of ontvangen van gebeurtenissen vanuit een Event Hub: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (alleen verzenden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (alleen ontvangen)](event-hubs-storm-getstarted-receive.md)


[maakt u een gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
