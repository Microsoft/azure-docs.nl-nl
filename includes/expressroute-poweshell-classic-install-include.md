---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018147"
---
Installeer de nieuwste versie van de Azure SM (Service Management) PowerShell-modules en de Azure ExpressRoute-module. U kunt de Azure CloudShell-omgeving niet gebruiken om SM-modules uit te voeren.

1. Volg de instructies in het artikel [De Service Management-module installeren](/powershell/azure/servicemanagement/install-azure-ps) artikel voor het installeren van de Azure Service Management-module. Als de Az- of RM-module al is geïnstalleerd, gebruikt u -AllowClobber.
2. Importeer de geïnstalleerde modules. Wanneer u het volgende voorbeeld gebruikt, past u het pad aan de locatie en versie van de geïnstalleerde PowerShell-modules aan.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Als u zich wilt aanmelden bij uw Azure-account, opent u de PowerShell-console met verhoogde rechten en maakt u verbinding met uw account. Gebruik het volgende voorbeeld om verbinding te maken met behulp van de Service Management-module:

   ```powershell
   Add-AzureAccount
   ```