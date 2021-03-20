---
title: Azure peering service inschakelen op een directe peering met behulp van Power shell
titleSuffix: Azure
description: Azure peering service inschakelen op een directe peering met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079044"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Azure peering service inschakelen op een directe peering met behulp van Power shell

In dit artikel wordt beschreven hoe u Azure [peering service](overview-peering-service.md) inschakelt op een directe peering met behulp van Power shell-cmdlets en het Azure Resource Manager-implementatie model.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) voordat u begint met de configuratie.
* Kies een directe peering in uw abonnement waarvoor u de peering-service wilt inschakelen. Als u er nog geen hebt, moet u een verouderde directe peering converteren of een nieuwe directe peering maken:
    * Als u een verouderde directe peering wilt converteren, volgt u de instructies in [een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-direct-powershell.md).
    * Als u een nieuwe directe peering wilt maken, volgt u de instructies in [een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Directe peering weer geven
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De directe peering inschakelen voor de peering-service

Als u in de vorige stap direct peering hebt, schakelt u deze in voor de peering-service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Een directe peering-verbinding wijzigen

Als u de verbindings instellingen wilt wijzigen, raadpleegt u de sectie ' een directe peering wijzigen ' in [een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md).

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van Power shell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle parameters opvragen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen over peering-service](service-faqs.md)voor meer informatie.
