---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 22a9cf3338f422341928a77f2bf14c497aa2ba31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563770"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) installeren 

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Als u een Azure Communication Services-resource wilt maken, [meldt u zich aan bij Azure cli](/cli/azure/authenticate-azure-cli). U kunt dit via de Terminal doen met behulp van de ```az login``` opdracht en uw referenties opgeven. Voer de volgende opdracht uit om de resource te maken:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Als u een specifiek abonnement wilt selecteren, kunt u ook de vlag opgeven ```--subscription``` en de abonnements-id opgeven.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

U kunt uw communicatie Services-resource configureren met de volgende opties:

* De resource groep
* De naam van de Communication Services-resource
* De geografie waaraan de resource wordt gekoppeld

In de volgende stap kunt u tags toewijzen aan de resource. Tags kunnen worden gebruikt voor het organiseren van uw Azure-resources. Zie de [documentatie van resource-tagging ](../../../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags.

## <a name="manage-your-communication-services-resource"></a>Een Communication Services-resource maken.

Voer de volgende opdrachten uit om tags toe te voegen aan uw Communication Services-resource. U kunt ook een specifiek abonnement instellen.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Zie [AZ Communication](/cli/azure/ext/communication/communication)(Engelstalig) voor meer informatie over aanvullende opdrachten.
