---
title: Een Azure-Cloud service (klassiek) schalen in Windows Power shell | Microsoft Docs
description: klassieke Meer informatie over het gebruik van Power shell om een webrole of worker-rol in of uit te schalen in Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a090da1933b0fcd6edb5b2415c773f9efcb27387
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743301"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Een Azure-Cloud service (klassiek) schalen in Power shell

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

U kunt Windows Power shell gebruiken om een webrol of werk rollen in of uit te schalen door instanties toe te voegen of te verwijderen.  

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Voordat u bewerkingen kunt uitvoeren op uw abonnement via Power shell, moet u zich aanmelden:

```powershell
Add-AzureAccount
```

Als er meerdere abonnementen aan uw account zijn gekoppeld, moet u mogelijk het huidige abonnement wijzigen, afhankelijk van waar uw Cloud service zich bevindt. Voer het volgende uit om het huidige abonnement te controleren:

```powershell
Get-AzureSubscription -Current
```

Als u het huidige abonnement wilt wijzigen, voert u de volgende handelingen uit:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Het huidige aantal instanties voor uw rol controleren

Als u de huidige status van uw rol wilt controleren, voert u de volgende handelingen uit:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

U moet informatie over de rol ophalen, met inbegrip van de huidige versie van het besturings systeem en het aantal instanties. In dit geval heeft de rol één exemplaar.

![Informatie over de rol](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>De rol uitschalen door meer instanties toe te voegen

Als u uw rol wilt uitschalen, geeft u het gewenste aantal instanties door als de para meter **aantal** voor de cmdlet **set-AzureRole** :

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

De cmdlet blokkeert tijdelijk terwijl de nieuwe instanties worden ingericht en gestart. Als u gedurende deze tijd een nieuw Power shell-venster opent en **Get-AzureRole** aanroept, zoals eerder weer gegeven, ziet u het aantal nieuwe doel exemplaren. En als u de status van de functie in de portal inspecteert, ziet u het nieuwe exemplaar dat wordt gestart:

![VM-instantie begint in Portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Zodra de nieuwe instanties zijn gestart, wordt de cmdlet geretourneerd:

![Het verhogen van de rolinstantie is voltooid](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>De rol schalen door instanties te verwijderen

U kunt een rol schalen door instanties op dezelfde manier te verwijderen. Stel de para meter **aantal** op **set-AzureRole** in op het aantal exemplaren dat u wilt hebben nadat de schaal bewerking is voltooid.

## <a name="next-steps"></a>Volgende stappen

Het is niet mogelijk om automatisch schalen te configureren voor Cloud Services vanuit Power shell. Zie [een Cloud service automatisch schalen](cloud-services-how-to-scale-portal.md)voor meer informatie.
