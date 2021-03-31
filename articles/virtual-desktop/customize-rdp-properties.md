---
title: RDP-eigenschappen aanpassen met Power shell-Azure
description: RDP-eigenschappen voor virtuele Windows-Bureau bladen aanpassen met Power shell-cmdlets.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95018304"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Eigenschappen van Remote Desktop Protocol (RDP) voor een hostgroep aanpassen

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Als u de eigenschappen van de Remote Desktop Protocol (RDP) van een hostgroep wilt aanpassen, zoals de ervaring voor meerdere monitors en audio-omleiding, kunt u een optimale ervaring bieden aan uw gebruikers op basis van hun behoeften. U kunt RDP-eigenschappen in virtueel bureau blad van Windows aanpassen door gebruik te maken van de Azure Portal of door gebruik te maken van de para meter *-CustomRdpProperty* in de cmdlet **Update-AzWvdHostPool** .

Zie [ondersteunde RDP-Bestands instellingen](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) voor een volledige lijst met ondersteunde eigenschappen en hun standaard waarden.

## <a name="default-rdp-file-properties"></a>Standaard eigenschappen van RDP-bestand

RDP-bestanden hebben standaard de volgende eigenschappen:

|RDP-eigenschap|Op bureau blad|Als een RemoteApp|
|---|---|---|
|Modus voor meerdere monitors|Uitgeschakeld|Ingeschakeld|
|Omleidingen van stations ingeschakeld|Stations, klem bord, printers, COM-poorten en Smart Cards|Stations, klem bord en printers|
|Modus voor externe audio|Lokaal afspelen|Lokaal afspelen|

## <a name="prerequisites"></a>Vereisten

Voordat u begint, volgt u de instructies in [De PowerShell-module van Windows Virtual Desktop instellen](powershell-module.md) om uw PowerShell-module in te stellen en zich bij Azure aan te melden.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>RDP-eigenschappen configureren in de Azure Portal

RDP-eigenschappen configureren in de Azure Portal:

1. Meld u aan bij Azure op <https://portal.azure.com>.
2. Voer **Windows virtueel bureau blad** in op de zoek balk.
3. Onder Services selecteert u **virtueel bureau blad voor Windows**.
4. Selecteer op de pagina virtueel bureau blad van Windows de optie **hostgroepen** in het menu aan de linkerkant van het scherm.
5. Selecteer **de naam van de hostgroep** die u wilt bijwerken.
6. Selecteer **RDP-eigenschappen** in het menu aan de linkerkant van het scherm.
7. Stel de gewenste eigenschap in.
   - U kunt ook het tabblad **Geavanceerd** openen en uw RDP-eigenschappen toevoegen in een door punt komma's gescheiden indeling zoals de Power shell-voor beelden in de volgende secties.
8. Wanneer u klaar bent, selecteert u **Opslaan** om uw wijzigingen op te slaan.

In de volgende secties wordt uitgelegd hoe u aangepaste RDP-eigenschappen hand matig kunt bewerken in Power shell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Eén aangepaste RDP-eigenschap toevoegen of bewerken

Als u één aangepaste RDP-eigenschap wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlet uit:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Voer de volgende cmdlet uit om te controleren of de cmdlet die u zojuist hebt uitgevoerd, de eigenschap heeft bijgewerkt:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Als u bijvoorbeeld controleert op de eigenschap ' audiocapturemode ' van een hostgroep met de naam 0301HP, voert u de volgende cmdlet in:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Meerdere aangepaste RDP-eigenschappen toevoegen of bewerken

Als u meerdere aangepaste RDP-eigenschappen wilt toevoegen of bewerken, voert u de volgende Power shell-cmdlets uit door de aangepaste RDP-eigenschappen op te geven als een door punt komma's gescheiden teken reeks:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

U kunt controleren of de RDP-eigenschap is toegevoegd door de volgende cmdlet uit te voeren:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Op basis van onze eerder cmdlet-voor beeld: als u meerdere RDP-eigenschappen voor de 0301HP-hostgroep instelt, ziet uw cmdlet er als volgt uit.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Alle aangepaste RDP-eigenschappen opnieuw instellen

U kunt afzonderlijke aangepaste RDP-eigenschappen opnieuw instellen op de standaard waarden door de instructies in [een enkele aangepaste RDP-eigenschap toevoegen of bewerken](#add-or-edit-a-single-custom-rdp-property)te volgen, of u kunt alle aangepaste RDP-eigenschappen voor een hostgroep opnieuw instellen door de volgende Power shell-cmdlet uit te voeren:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Voer de volgende cmdlet in om ervoor te zorgen dat de instelling is verwijderd:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Volgende stappen

Nu u de RDP-eigenschappen voor een bepaalde hostgroep hebt aangepast, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. In deze volgende hand leidingen wordt uitgelegd hoe u verbinding maakt met een sessie met de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-10.md)
- [Verbinding maken met de webclient](connect-web.md)
- [Verbinding maken met de Android-client](connect-android.md)
- [Verbinding maken met de macOS-client](connect-macos.md)
- [Verbinding maken met de iOS-client](connect-ios.md)