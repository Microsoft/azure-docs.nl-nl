---
title: Windows Virtual Desktop (classic)-hostgroep voor service-updates - Azure
description: Lees hoe u een validatiehostgroep in Windows Virtual Desktop (classic) maakt om service-updates te controleren voordat updates worden uitgerold naar productie.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ad79ad31678f698c0f034b39bab1e3a19a48d3f2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444969"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Zelfstudie: Een hostgroep maken voor het valideren van service-updates in Windows Virtual Desktop (classic)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (classic), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../create-validation-host-pool.md) als u probeert Azure Resource Manager Windows Virtual Desktop-objecten te beheren.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-tenantomgevingen. U kunt het beste een validatiehostgroep maken waarop service-updates eerst worden toegepast. Zo kunt u service-updates bewaken voordat deze met de service worden toegepast op uw standaardomgeving of omgeving zonder validatie worden toegepast. Zonder een validatiehostgroep kan het zijn dat u wijzigingen over het hoofd ziet die fouten veroorzaken. Dit kan leiden tot uitvaltijd voor gebruikers in uw productieomgeving.

Om ervoor te zorgen dat uw apps goed werken met de nieuwste updates, moet de validatiehostgroep zo veel mogelijk lijken op de hostgroepen in uw omgeving zonder validatie. Gebruikers moeten net zo vaak verbinding maken met de validatiehostgroep als met de standaardhostgroep. Als u automatische tests gebruikt voor uw hostgroep, moet u deze ook uitvoeren op de validatiehostgroep.

U kunt problemen in de validatiehostgroep opsporen met [de diagnostische functie](diagnostics-role-service-2019.md) of de [artikelen voor het oplossen van problemen met Windows Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> U wordt aangeraden de validatiehostgroep te laten staan om alle toekomstige updates te testen.

[Download en importeer eerst de PowerShell-module van Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Uw hostgroep maken

U kunt een hostgroep maken door de instructies in een van deze artikelen te volgen:
- [Zelfstudie: Een hostpool maken met Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Een hostpool maken met een Azure Resource Manager-sjabloon](create-host-pools-arm-template.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Uw hostgroep definiëren als een validatiehostgroep

Voer de volgende PowerShell-cmdlets uit om de nieuwe hostgroep te definiëren als een validatiehostgroep. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Voer de volgende PowerShell-cmdlet uit om te controleren of de validatie-eigenschap is ingesteld. Vervang de waarden tussen aanhalingstekens door de waarden die relevant zijn voor uw sessie.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

De resultaten van de cmdlet moeten er ongeveer als volgt uitzien:

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Updateplanning

Service-updates worden maandelijks uitgevoerd. Als er grote problemen zijn, worden er sneller essentiële updates uitgegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u een validatiehostgroep hebt gemaakt, kunt u leren hoe u Azure Service Health gebruikt om uw Windows Virtual Desktop-implementatie te bewaken.

> [!div class="nextstepaction"]
> [Servicewaarschuwingen instellen](set-up-service-alerts-2019.md)
