---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644736"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- De [Azure AZ Power shell-module](https://docs.microsoft.com/powershell/azure/) installeren

## <a name="create-azure-communication-resource"></a>Azure Communication-resource maken

Als u een Azure Communication Services-resource wilt maken, [meldt u zich aan bij Azure cli](/cli/azure/authenticate-azure-cli). U kunt dit via de Terminal doen met behulp van de ```Connect-AzAccount``` opdracht en uw referenties opgeven. Voer de volgende opdracht uit om de resource te maken:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Als u een specifiek abonnement wilt selecteren, kunt u ook de vlag opgeven ```--subscription``` en de abonnements-id opgeven.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

U kunt uw communicatie Services-resource configureren met de volgende opties:

* De resource groep
* De naam van de Communication Services-resource
* De geografie waaraan de resource wordt gekoppeld

In de volgende stap kunt u tags toewijzen aan de resource. Tags kunnen worden gebruikt voor het organiseren van uw Azure-resources. Zie de [documentatie van resource-tagging ](../../../azure-resource-manager/management/tag-resources.md) voor meer informatie over tags.

## <a name="manage-your-communication-services-resource"></a>Een Communication Services-resource maken.

Voer de volgende opdrachten uit om tags toe te voegen aan uw Communication Services-resource. U kunt ook een specifiek abonnement instellen.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Gebruik de volgende opdracht om een overzicht te geven van alle Azure Communication Services-resources in een bepaald abonnement:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Gebruik de volgende opdracht om alle informatie over een bepaalde resource weer te geven:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
