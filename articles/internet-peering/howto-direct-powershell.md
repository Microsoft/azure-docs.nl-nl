---
title: Een directe peering maken of wijzigen met behulp van Power shell
titleSuffix: Azure
description: Een directe peering maken of wijzigen met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079061"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Een directe peering maken of wijzigen met behulp van Power shell

In dit artikel wordt beschreven hoe u een micro soft direct-peering maakt met behulp van Power shell-cmdlets en het Azure Resource Manager-implementatie model. In dit artikel leest u ook hoe u de status van de resource kunt controleren, bijwerken of verwijderen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Stapsgewijze instructies voor direct peering](walkthrough-direct-all.md) voordat u begint met de configuratie.
* Als u al directe peering-verbindingen met micro soft hebt die niet worden geconverteerd naar Azure-resources, raadpleegt u [een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Een directe peering maken en inrichten

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Aanmelden bij uw Azure-account en uw abonnement selecteren
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>De lijst met ondersteunde peering locaties voor directe peering ophalen
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Een directe peering maken
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Directe peering controleren
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Een directe peering wijzigen
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Inrichting van een directe peering ongedaan maken
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van Power shell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle parameters opvragen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.
