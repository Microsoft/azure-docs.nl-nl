---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819997"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Als u een Azure Communication Services-resource wilt maken, [meldt u zich aan bij Azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)en voert u de volgende opdracht uit:

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

Zie [AZ Communication](https://docs.microsoft.com/cli/azure/ext/communication/communication)(Engelstalig) voor meer informatie over aanvullende opdrachten.