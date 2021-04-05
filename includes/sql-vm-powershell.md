---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560671"
---
## <a name="start-your-powershell-session"></a>Een PowerShell-sessie starten
 

Voer de cmdlet [**Connect-AZ account**](/powershell/module/Az.Accounts/Connect-AzAccount) uit en er wordt een aanmeldings scherm weer gegeven waarin u uw referenties kunt invoeren. Gebruik de referenties waarmee u zich aanmeldt bij Azure Portal.

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt, gebruikt u de cmdlet [**set-AzContext**](/powershell/module/az.accounts/set-azcontext) om te selecteren welk abonnement uw Power shell-sessie moet gebruiken. Als u wilt zien welk abonnement door de huidige Power shell-sessie wordt gebruikt, voert u [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext)uit. Voer [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription)uit om al uw abonnementen weer te geven.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```