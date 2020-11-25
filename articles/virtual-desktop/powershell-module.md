---
title: Power shell-module Windows virtueel bureau blad-Azure
description: De Power shell-module voor virtueel bureau blad van Windows installeren en instellen.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016808"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>De Power shell-module voor Windows virtueel bureau blad instellen

>[!IMPORTANT]
>Deze inhoud is van toepassing op virtueel bureau blad van Windows met Azure Resource Manager-integratie.

De Windows Power shell-module voor virtueel bureau blad is geïntegreerd in de module Azure PowerShell. In dit artikel leest u hoe u de Power shell-module instelt, zodat u cmdlets kunt uitvoeren voor virtuele Windows-Bureau bladen.

## <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen

Om aan de slag te gaan met het gebruik van de module, installeert u eerst de [meest recente versie van Power shell core](/powershell/scripting/install/installing-powershell#powershell-core). Virtuele bureau blad-cmdlets van Windows werken momenteel alleen met Power shell core.

Vervolgens moet u de DesktopVirtualization-module installeren voor gebruik in uw Power shell-sessie.

Voer de volgende Power shell-cmdlet uit in de modus met verhoogde bevoegdheden om de module te installeren:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Als deze cmdlet niet werkt, probeert u deze opnieuw uit te voeren met verhoogde machtigingen.

Voer vervolgens de volgende cmdlet uit om verbinding te maken met Azure:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Als u verbinding maakt met de US Gov Portal, voert u de volgende cmdlet uit:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Als u zich aanmeldt bij uw Azure-account, moet er een code worden gegenereerd wanneer u de Connect-cmdlet uitvoert. Als u zich wilt aanmelden, gaat u naar <https://microsoft.com/devicelogin> , voert u de code in en meldt u zich aan met de referenties van uw Azure-beheerder.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Hiermee wordt u rechtstreeks aangemeld bij het abonnement dat standaard is voor uw beheerders referenties.

## <a name="change-the-default-subscription"></a>Het standaard abonnement wijzigen

Als u het standaard abonnement wilt wijzigen nadat u zich hebt aangemeld, voert u deze cmdlet uit:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

U kunt er ook een selecteren in een lijst met behulp van de cmdlet Out-GridView:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Wanneer u een nieuw abonnement selecteert, hoeft u de abonnements-ID niet op te geven in cmdlets die u later uitvoert. Met de volgende cmdlet wordt bijvoorbeeld een specifieke sessiehost opgehaald zonder dat hiervoor de abonnements-ID is vereist:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

U kunt abonnementen ook per cmdlet wijzigen door de gewenste abonnements naam als para meter toe te voegen. De volgende cmdlet is hetzelfde als het vorige voor beeld, behalve als de abonnements-ID is toegevoegd als een para meter om te wijzigen welk abonnement door de cmdlet wordt gebruikt.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Locaties ophalen

De locatie parameter is verplicht voor alle **nieuwe AzWVD-** cmdlets waarmee nieuwe objecten worden gemaakt.

Voer de volgende cmdlet uit om een lijst met locaties op te halen die uw abonnement ondersteunt:

```powershell
Get-AzLocation
```

De uitvoer voor **Get-AzLocation** ziet er als volgt uit:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Zodra u de locatie van uw account kent, kunt u deze gebruiken in een cmdlet. Hier volgt bijvoorbeeld een cmdlet waarmee een hostgroep wordt gemaakt in de locatie ' southeastasia ':

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Volgende stappen

Nu u de Power shell-module hebt ingesteld, kunt u cmdlets uitvoeren voor het maken van allerlei dingen in Windows virtueel bureau blad. Hier volgen enkele van de locaties waar u de module kunt gebruiken:

- Voer de [Windows-zelf studies voor virtueel bureau blad]() uit om uw eigen virtuele Windows-desktop omgeving in te stellen.
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)
- [De taakverdelingsmethode voor Windows Virtual Desktop configureren](configure-host-pool-load-balancing.md)
- [Het toewijzings type voor de hostgroep voor persoonlijk bureau blad configureren](configure-host-pool-personal-desktop-assignment-type.md)
- En nog veel meer!

Als u problemen ondervindt, raadpleegt u ons [artikel over het oplossen van problemen met Power shell](troubleshoot-powershell.md) voor meer informatie.

