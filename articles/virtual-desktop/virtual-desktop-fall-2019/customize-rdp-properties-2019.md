---
title: RDP-eigenschappen aanpassen met Power shell Windows virtueel bureau blad (klassiek)-Azure
description: RDP-eigenschappen voor virtueel bureau blad van Windows (klassiek) aanpassen met Power shell-cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020705"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Remote Desktop Protocol eigenschappen voor een hostgroep in een virtueel bureau blad (klassiek) van Windows aanpassen

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../customize-rdp-properties.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Als u de eigenschappen van de Remote Desktop Protocol (RDP) van een hostgroep wilt aanpassen, zoals de ervaring voor meerdere monitors en audio-omleiding, kunt u een optimale ervaring bieden aan uw gebruikers op basis van hun behoeften. U kunt RDP-eigenschappen in virtueel bureau blad van Windows aanpassen met behulp van de para meter **-CustomRdpProperty** in de cmdlet **set-RdsHostPool** .

Zie [ondersteunde RDP-Bestands instellingen](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) voor een volledige lijst met ondersteunde eigenschappen en hun standaard waarden.

Eerst [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standaard RDP-eigenschappen

Gepubliceerde RDP-bestanden bevatten standaard de volgende eigenschappen:

|RDP-eigenschappen | Desktopcomputers | RemoteApps |
|---|---| --- |
| Modus voor meerdere monitors | Ingeschakeld | N.v.t. |
| Omleidingen van stations ingeschakeld | Stations, klem bord, printers, COM-poorten, USB-apparaten en-Smart Cards| Stations, klem bord en printers |
| Modus voor externe audio | Lokaal afspelen | Lokaal afspelen |

Aangepaste eigenschappen die u voor de hostgroep definieert, overschrijven deze standaard waarden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Eén aangepaste RDP-eigenschap toevoegen of bewerken

Als u één aangepaste RDP-eigenschap wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlet uit:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName gemarkeerd voor het bewerken van een aangepaste R D P-eigenschap.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Meerdere aangepaste RDP-eigenschappen toevoegen of bewerken

Als u meerdere aangepaste RDP-eigenschappen wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlets uit door de aangepaste RDP-eigenschappen op te geven als een door punt komma's gescheiden teken reeks:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Set-RDSRemoteApp met de naam en FriendlyName gemarkeerd voor het bewerken van een aangepaste R D P-eigenschap.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Alle aangepaste RDP-eigenschappen opnieuw instellen

U kunt afzonderlijke aangepaste RDP-eigenschappen opnieuw instellen op de standaard waarden door de instructies in [een enkele aangepaste RDP-eigenschap toevoegen of bewerken](#add-or-edit-a-single-custom-rdp-property)te volgen, of u kunt alle aangepaste RDP-eigenschappen voor een hostgroep opnieuw instellen door de volgende Power shell-cmdlet uit te voeren:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Power shell-cmdlet Get-RDSRemoteApp met de naam en FriendlyName gemarkeerd.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de RDP-eigenschappen voor een bepaalde hostgroep hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. In de volgende twee procedures wordt uitgelegd hoe u verbinding maakt met een sessie met de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-10-2019.md)
- [Verbinding maken met de webclient](connect-web-2019.md)