---
title: Uittreding van de gast besturingssysteem familie 1 | Microsoft Docs
description: Bevat informatie over wanneer de Azure-gast besturingssysteem familie 1 buiten gebruik is gesteld en hoe u kunt bepalen of dit geldt
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743012"
---
# <a name="guest-os-family-1-retirement-notice"></a>Kennisgeving van buitengebruikstelling van serie 1 van gastbesturingssysteem

De buiten gebruiks telling van besturingssysteem familie 1 werd voor het eerst aangekondigd op 1 juni 2013.

**Sept 2, 2014** Het Azure Guest Operating System (gast besturingssysteem), dat is gebaseerd op het Windows Server 2008-besturings systeem, is officieel buiten gebruik gesteld. Alle pogingen om nieuwe services te implementeren of bestaande services te upgraden met familie 1, mislukken met een fout bericht waarin wordt gemeld dat de gast besturingssysteem familie 1 buiten gebruik is gesteld.

**3 November 2014** Uitgebreide ondersteuning voor gast besturingssysteem familie 1 is beëindigd en is volledig buiten gebruik gesteld. Alle services die nog op familie 1 worden getroffen, worden beïnvloed. We kunnen deze services op elk gewenst moment stoppen. Er is geen garantie dat uw services blijven worden uitgevoerd, tenzij u deze hand matig bijwerkt.

Als u meer vragen hebt, gaat u naar de [pagina micro soft Q&een vraag voor Cloud Services](/answers/topics/azure-cloud-services.html) of [neemt u contact op met de ondersteuning van Azure](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Hebt u last van dit probleem?
Uw Cloud Services worden beïnvloed als een van de volgende van toepassing is:

1. U hebt de waarde ' osFamily = ' 1 ' expliciet opgegeven in het bestand ServiceConfiguration. cscfg voor uw Cloud service.
2. U hebt geen waarde voor osFamily expliciet opgegeven in het bestand ServiceConfiguration. cscfg voor uw Cloud service. Op dit moment gebruikt het systeem de standaard waarde ' 1 ' in dit geval.
3. De Azure Portal bevat een lijst met de waarde van uw gast besturingssysteem familie als ' Windows Server 2008 '.

Als u wilt weten welke van uw Cloud Services worden uitgevoerd, kunt u het volgende script uitvoeren in Azure PowerShell, maar u moet [Azure PowerShell eerst instellen](/powershell/azure/) . Meer informatie over het script vindt u in [Azure Guest OS Family 1 end of Life: juni 2014](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Uw Cloud Services worden beïnvloed door de besturingssysteem familie 1 buiten gebruik gesteld als de kolom osFamily in de script uitvoer leeg is of een 1 bevat.

## <a name="recommendations-if-you-are-affected"></a>Aanbevelingen als u dit probleem ondervindt
U wordt aangeraden uw Cloud service rollen te migreren naar een van de ondersteunde gast besturingssysteem families:

**Gast besturingssysteem familie 4. x** -Windows Server 2012 R2 *(aanbevolen)*

1. Zorg ervoor dat uw toepassing SDK 2,1 of hoger gebruikt met .NET Framework 4,0, 4,5 of 4.5.1.
2. Stel het kenmerk osFamily in op ' 4 ' in het bestand ServiceConfiguration. cscfg en implementeer uw Cloud service opnieuw.

**Gast besturingssysteem Family 3. x** -Windows Server 2012

1. Zorg ervoor dat uw toepassing SDK 1,8 of hoger met .NET Framework 4,0 of 4,5 gebruikt.
2. Stel het kenmerk osFamily in op 3 in het bestand ServiceConfiguration. cscfg en implementeer uw Cloud service opnieuw.

**Gast besturingssysteem familie 2. x** -Windows Server 2008 R2

1. Zorg ervoor dat uw toepassing SDK 1,3 en hoger gebruikt met .NET Framework 3,5 of 4,0.
2. Stel het kenmerk osFamily in op ' 2 ' in het bestand ServiceConfiguration. cscfg en implementeer uw Cloud service opnieuw.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Uitgebreide ondersteuning voor gast besturingssysteem familie 1 is beëindigd op 3 november 2014
Cloud Services op gast besturingssysteem familie 1 worden niet meer ondersteund. U kunt familie 1 zo snel mogelijk migreren om te voor komen dat de service wordt onderbroken.  

## <a name="next-steps"></a>Volgende stappen
Bekijk de nieuwste [versies van het gast besturingssysteem](cloud-services-guestos-update-matrix.md).