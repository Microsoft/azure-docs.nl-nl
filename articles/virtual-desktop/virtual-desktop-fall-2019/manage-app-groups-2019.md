---
title: App-groepen voor Windows Virtual Desktop (klassiek) beheren - Azure
description: Meer informatie over hoe u (klassieke) Windows Virtual Desktop-tenants instelt in Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2a1f38918b2ea6af8a334b6648a463753f5c7b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295206"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Zelfstudie: App-groepen voor Windows Virtual Desktop (klassiek) beheren

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../manage-app-groups.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

De standaard app-groep die is aangemaakt voor een nieuwe Windows Virtual Desktop-hostgroep publiceert ook het volledige bureaublad. Daarnaast kunt u een of meer RemoteApp-toepassingsgroepen aanmaken voor de hostgroep. Volg deze zelfstudie om een RemoteApp-app-groep te maken en individuele apps voor het menu **Start** te publiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een RemoteApp-groep maken.
> * Toegang verlenen tot RemoteApp-programma's.

Voor u begint, [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Een RemoteApp-groep maken

1. Voer de volgende PowerShell-cmdlet uit om een nieuwe lege RemoteApp-toepassingsgroep te maken.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Optioneel) Als u wilt controleren of de app-groep is gemaakt, kunt u de volgende cmdlet uitvoeren om een lijst met alle app-groepen van de hostgroep weer te geven.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Voer de volgende cmdlet uit om een lijst op te halen van **startmenu**-apps in de installatiekopie van de virtuele machine van de hostgroep. Noteer de waarden voor **FilePath**, **IconPath**, **IconIndex** en andere belangrijke informatie voor de toepassing die u wilt publiceren.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Voer de volgende cmdlet uit om de toepassing te installeren op basis van `AppAlias`. `AppAlias` wordt zichtbaar wanneer u de uitkomst uit stap 3 uitvoert.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Optioneel) Voer de volgende cmdlet uit om een nieuw RemoteApp-programma te publiceren naar de toepassingsgroep die u in stap 1 hebt gemaakt.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Voer de volgende cmdlet uit om te controleren of de app is gepubliceerd.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Herhaal de stappen 1 tot en met 5 voor elke toepassing die u voor deze app-groep wilt publiceren.
8. Voer de volgende cmdlet uit om gebruikers toegang te verlenen tot de RemoteApp-programma's in de app-groep.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een app-groep kunt maken, deze kunt vullen met RemoteApp-programma's en gebruikers kunt toewijzen aan de app-groep. Bekijk de volgende zelfstudie om te leren hoe u een validatiehostgroep kunt maken. U kunt een validatiehostgroep gebruiken om service-updates te controleren voor u ze implementeert naar uw productieomgeving.

> [!div class="nextstepaction"]
> [Een hostpool voor het valideren van service-updates maken](create-validation-host-pool-2019.md)
