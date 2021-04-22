---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 69857915620eada94586754a6c934edaf0b294a9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880335"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Meld u aan Azure Communication Services Azure CLI om een resource [te maken.](/cli/azure/authenticate-azure-cli) U kunt dit doen via de terminal met behulp van ```az login``` de opdracht en uw referenties op te geven. Voer de volgende opdracht uit om de resource te maken:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Als u een specifiek abonnement wilt selecteren, kunt u ook de vlag ```--subscription``` opgeven en de abonnements-id opgeven.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

U kunt uw Communication Services configureren met de volgende opties:

* De resourcegroep
* De naam van de Communication Services-resource
* De geografie waaraan de resource wordt gekoppeld

In de volgende stap kunt u tags toewijzen aan de resource. Tags kunnen worden gebruikt voor het organiseren van uw Azure-resources. Zie de [documentatie van resource-tagging ](../../../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags.

## <a name="manage-your-communication-services-resource"></a>Een Communication Services-resource maken.

Als u tags wilt toevoegen aan Communication Services resource, voert u de volgende opdrachten uit. U kunt zich ook richten op een specifiek abonnement.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Zie az communication voor meer informatie [over aanvullende opdrachten.](/cli/azure/communication)
