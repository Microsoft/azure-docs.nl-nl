---
title: Virtuele Windows-bureau blad maken (klassieke) hostgroep Power shell-Azure
description: Een hostgroep in virtueel bureau blad van Windows (klassiek) maken met Power shell-cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88003544"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Een hostgroep in virtueel bureau blad van Windows (klassiek) maken met Power shell

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../create-host-pools-powershell.md) als u probeert Azure Resource Manager Windows Virtual Desktop-objecten te beheren.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-tenantomgevingen. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureaublad.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>De Power shell-client gebruiken voor het maken van een hostgroep

Eerst [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan.

Voer de volgende cmdlet uit om u aan te melden bij de virtuele Windows-bureaublad omgeving

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voer vervolgens deze cmdlet uit om een nieuwe hostgroep te maken in uw Windows Virtual Desktop-Tenant:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Voer de volgende cmdlet uit om een registratie token te maken om een sessie-host te autoriseren om lid te worden van de hostgroep en deze op te slaan in een nieuw bestand op uw lokale computer. U kunt opgeven hoe lang het registratie token geldig is door gebruik te maken van de para meter-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Daarna voert u deze cmdlet uit om Azure Active Directory gebruikers toe te voegen aan de standaard groep bureau blad-apps voor de hostgroep.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

De cmdlet **add-RdsAppGroupUser** biedt geen ondersteuning voor het toevoegen van beveiligings groepen en voegt slechts één gebruiker tegelijk toe aan de app-groep. Als u meerdere gebruikers wilt toevoegen aan de app-groep, voert u de cmdlet opnieuw uit met de juiste UPN-namen.

Voer de volgende cmdlet uit om het registratie token te exporteren naar een variabele, die u later gaat gebruiken in [REGI Steren van de virtuele machines naar de hostgroep van het virtuele Windows-bureau blad](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuele machines voor de hostgroep maken

U kunt nu een virtuele Azure-machine maken die kan worden gekoppeld aan uw Windows Virtual Desktop-hostgroep.

U kunt op verschillende manieren een virtuele machine maken:

- [Een virtuele machine maken op basis van een installatie kopie van Azure galerie](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele machine maken op basis van een beheerde installatie kopie](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Een virtuele machine maken op basis van een onbeheerde installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Als u een virtuele machine implementeert met Windows 7 als besturings systeem van de host, is het proces voor maken en implementeren iets anders. Zie [een virtuele Windows 7-machine implementeren op het virtuele bureau blad van Windows](deploy-windows-7-virtual-machine.md)voor meer informatie.

Nadat u de virtuele machines van de sessiehost hebt gemaakt, [past u een Windows-licentie toe op een host-VM](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) om uw Windows-of Windows Server-machines uit te voeren zonder dat u voor een andere licentie betaalt.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>De virtuele machines voorbereiden voor installaties van Virtual Desktop agent voor Windows

U moet de volgende stappen uitvoeren om uw virtuele machines voor te bereiden voordat u de virtuele bureau blad-agents van Windows kunt installeren en de virtuele machines aan uw Windows Virtual Desktop host-groep moet registreren:

- U moet een domein-lid worden van de computer. Hierdoor kunnen binnenkomende Windows-bureaublad gebruikers worden toegewezen vanuit hun Azure Active Directory-account aan hun Active Directory account en kunnen ze toegang krijgen tot de virtuele machine.
- U moet de rol van de Extern bureaublad Session Host (RDSH) installeren als op de virtuele machine een Windows Server-besturings systeem wordt uitgevoerd. Met de rol van RDSH kunnen de virtuele bureau blad-agents van Windows correct worden geïnstalleerd.

Ga als volgt te werk op elke virtuele machine voor een geslaagde domein koppeling:

1. [Maak verbinding met de virtuele machine](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt ingevoerd tijdens het maken van de virtuele machine.
2. Start **configuratie scherm** op de virtuele machine en selecteer **systeem**.
3. Selecteer **computer naam**, selecteer **instellingen wijzigen** en selecteer vervolgens **wijzigen...**
4. Selecteer **domein** en voer vervolgens het Active Directory domein in op het virtuele netwerk.
5. Verifieer met een domein account dat bevoegdheden heeft voor computers die lid zijn van een domein.

    >[!NOTE]
    > Als u uw Vm's lid maakt van een Azure Active Directory Domain Services-omgeving (Azure AD DS), moet u ervoor zorgen dat uw domein deelname ook lid is van de [groep Aad DC-Administrators](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>De virtuele machines registreren bij de hostgroep voor virtuele Windows-Bureau bladen

Het registreren van de virtuele machines in een Windows-hostgroep voor virtueel bureau blad is net zo eenvoudig als het installeren van de virtuele bureau blad-agents van Windows.

Ga als volgt te werk op elke virtuele machine om de virtuele bureau blad-agents van Windows te registreren:

1. [Maak verbinding met de virtuele machine](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt ingevoerd tijdens het maken van de virtuele machine.
2. Down load en installeer de virtuele bureau blad-agent van Windows.
   - Down load de [Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klik met de rechter muisknop op het gedownloade installatie programma, selecteer **Eigenschappen**, **blok kering opheffen** en selecteer **OK**. Hiermee kan het installatie programma worden vertrouwd door uw systeem.
   - Voer het installatieprogramma uit. Wanneer het installatie programma u vraagt om het registratie token, voert u de waarde in die u hebt ontvangen van de cmdlet **export-RdsRegistrationInfo** .
3. Down load en installeer de bootloader voor het virtuele bureau blad-agent van Windows.
   - Down load de bootloader voor de [virtuele Windows-bureau blad-agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Klik met de rechter muisknop op het gedownloade installatie programma, selecteer **Eigenschappen**, **blok kering opheffen** en selecteer **OK**. Hiermee kan het installatie programma worden vertrouwd door uw systeem.
   - Voer het installatieprogramma uit.

>[!IMPORTANT]
>Voor het beveiligen van uw virtuele bureau blad-omgeving in azure, raden we u aan om de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor het virtuele bureau blad van Windows is geen open bare poort 3389 voor gebruikers nodig om toegang te krijgen tot de virtuele machines van de hostgroep. Als u poort 3389 moet openen voor het oplossen van problemen, raden we u aan [just-in-time-VM-toegang](../../security-center/security-center-just-in-time.md)te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u een hostgroep hebt gemaakt, kunt u deze vullen met RemoteApps. Zie de zelf studie app-groepen beheren voor meer informatie over het beheren van apps in Windows virtueel bureau blad.

> [!div class="nextstepaction"]
> [Zelfstudie: App-groepen beheren](../manage-app-groups.md)
