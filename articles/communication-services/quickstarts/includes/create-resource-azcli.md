---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 6ed6544b8014e973eaf92c763ca18687ad89e5a7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495870"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Als u een Azure Communication Services-resource wilt maken, [meldt u zich aan bij Azure cli](/cli/azure/authenticate-azure-cli)en voert u de volgende opdracht uit:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

U kunt uw communicatie Services-resource configureren met de volgende opties:

* De resource groep
* De naam van de Communication Services-resource
* De geografie waaraan de resource wordt gekoppeld

In de volgende stap kunt u tags toewijzen aan de resource. Tags kunnen worden gebruikt voor het organiseren van uw Azure-resources. Zie de [documentatie van resource-tagging ](../../../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags.

## <a name="manage-your-communication-services-resource"></a>Een Communication Services-resource maken.

Voer de volgende opdrachten uit om tags toe te voegen aan uw communicatie Service-Resource:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Zie [AZ Communication](/cli/azure/ext/communication/communication)(Engelstalig) voor meer informatie over aanvullende opdrachten.