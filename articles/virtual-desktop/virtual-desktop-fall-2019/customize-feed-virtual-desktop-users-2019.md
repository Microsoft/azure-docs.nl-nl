---
title: Feed aanpassen voor Windows-gebruikers met virtueel bureau blad (klassiek)-Azure
description: Het aanpassen van de feed voor Windows Virtual Desktop-gebruikers (klassiek) met Power shell-cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540758"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Feed aanpassen voor Windows-gebruikers met virtueel bureau blad (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../customize-feed-for-virtual-desktop-users.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

U kunt de feed aanpassen zodat de RemoteApp-en extern bureau blad-resources op een herken bare manier worden weer gegeven voor uw gebruikers.

Eerst [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>De weergave naam voor een RemoteApp aanpassen

U kunt de weergave naam voor een gepubliceerde RemoteApp wijzigen door de beschrijvende naam in te stellen. De beschrijvende naam is standaard hetzelfde als de naam van het RemoteApp-programma.

Voer de volgende Power shell-cmdlet uit om een lijst met gepubliceerde RemoteApps voor een app-groep op te halen:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName gemarkeerd om de weergave naam aan te passen.](../media/get-rdsremoteapp.png)

Voer de volgende Power shell-cmdlet uit om een beschrijvende naam toe te wijzen aan een RemoteApp:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Set-RDSRemoteApp met de naam en de nieuwe FriendlyName gemarkeerd om de weergave naam aan te passen.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>De weergave naam voor een Extern bureaublad aanpassen

U kunt de weergave naam voor een gepubliceerd extern bureau blad wijzigen door een beschrijvende naam in te stellen. Als u hand matig een hostgroep en een groep met desktop-apps hebt gemaakt via Power shell, is de standaard beschrijvende naam "Session Desktop". Als u een groep voor een hostgroep en een bureau blad-app hebt gemaakt via de GitHub Azure Resource Manager sjabloon of de Azure Marketplace-aanbieding, is de standaard beschrijvende naam hetzelfde als de naam van de hostgroep.

Voer de volgende Power shell-cmdlet uit om de resource van het externe bureau blad op te halen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName gemarkeerd.](../media/get-rdsremotedesktop.png)

Voer de volgende Power shell-cmdlet uit om een beschrijvende naam toe te wijzen aan de extern bureau blad-resource:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Set-RDSRemoteApp met de naam en de nieuwe FriendlyName is gemarkeerd.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de feed voor gebruikers hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om het te testen. Als u dit wilt doen, gaat u naar de uitleg verbinding maken met Windows virtueel bureau blad:

 * [Verbinding maken vanaf Windows 10 of Windows 7](connect-windows-7-10-2019.md)
 * [Verbinding maken via een webbrowser](connect-web-2019.md)
