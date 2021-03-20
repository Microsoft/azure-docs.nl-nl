---
title: Feed aanpassen voor Windows-gebruikers met virtueel bureau blad-Azure
description: Het aanpassen van de feed voor Windows-virtuele bureau blad-gebruikers met Power shell-cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084291"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>De feed aanpassen voor Windows-virtuele bureau blad-gebruikers

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

U kunt de feed aanpassen zodat de RemoteApp-en extern bureau blad-resources op een herken bare manier worden weer gegeven voor uw gebruikers.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de Windows Power shell-module voor virtueel bureau blad al hebt gedownload en geïnstalleerd. Als dat niet het geval is, volgt u de instructies in [de Power shell-module instellen](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>De weergave naam voor een RemoteApp aanpassen

U kunt de weergave naam voor een gepubliceerde RemoteApp wijzigen door de beschrijvende naam in te stellen. De beschrijvende naam is standaard hetzelfde als de naam van het RemoteApp-programma.

Voer de volgende Power shell-cmdlet uit om een lijst met gepubliceerde RemoteApps voor een app-groep op te halen:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Voer de volgende cmdlet uit met de vereiste para meters om een beschrijvende naam toe te wijzen aan een RemoteApp:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Stel bijvoorbeeld dat u de huidige toepassingen hebt opgehaald met de volgende voor beeld-cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

De uitvoer ziet er als volgt uit:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Voer de volgende cmdlet uit om de beschrijvende naam bij te werken:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Voer de volgende cmdlet uit om te bevestigen dat u de beschrijvende naam hebt bijgewerkt:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

De cmdlet geeft u de volgende uitvoer:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>De weergave naam voor een Extern bureaublad aanpassen

U kunt de weergave naam voor een gepubliceerd extern bureau blad wijzigen door een beschrijvende naam in te stellen. Als u hand matig een hostgroep en een groep met desktop-apps hebt gemaakt via Power shell, is de standaard beschrijvende naam "Session Desktop". Als u een groep voor een hostgroep en een bureau blad-app hebt gemaakt via de GitHub Azure Resource Manager sjabloon of de Azure Marketplace-aanbieding, is de standaard beschrijvende naam hetzelfde als de naam van de hostgroep.

Voer de volgende Power shell-cmdlet uit om de resource van het externe bureau blad op te halen:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Voer de volgende Power shell-cmdlet uit om een beschrijvende naam toe te wijzen aan de extern bureau blad-resource:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Een weergave naam in Azure Portal aanpassen

U kunt de weergave naam voor een gepubliceerd extern bureau blad wijzigen door een beschrijvende naam in te stellen met behulp van de Azure Portal.

1. Meld u aan bij Azure Portal op <https://portal.azure.com>.

2. Zoeken naar **virtueel bureau blad van Windows**.

3. Onder Services selecteert u **virtueel bureau blad voor Windows**.

4. Selecteer op de pagina virtueel bureau blad van Windows de optie **toepassings groepen** aan de linkerkant van het scherm en selecteer vervolgens de naam van de app-groep die u wilt bewerken. (Als u bijvoorbeeld de weergave naam van de groep bureau blad-Apps wilt bewerken, selecteert u de app-groep met de naam **bureau blad**.)

5. Selecteer **toepassingen** in het menu aan de linkerkant van het scherm.

6. Selecteer de toepassing die u wilt bijwerken en voer vervolgens een nieuwe **weergave naam** in.

7. Selecteer **Opslaan**. De toepassing die u hebt bewerkt, moet nu de bijgewerkte naam weer geven.

## <a name="next-steps"></a>Volgende stappen

Nu u de feed voor gebruikers hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om het te testen. Als u dit wilt doen, gaat u naar de uitleg verbinding maken met Windows virtueel bureau blad:

 * [Verbinding maken met Windows 10 of Windows 7](connect-windows-7-10.md)
 * [Verbinding maken met de webclient](connect-web.md)
 * [Verbinding maken met de Android-client](connect-android.md)
 * [Verbinding maken met de iOS-client](connect-ios.md)
 * [Verbinding maken met de macOS-client](connect-macos.md)
