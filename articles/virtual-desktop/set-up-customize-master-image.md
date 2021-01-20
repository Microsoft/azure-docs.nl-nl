---
title: Een VHD-model installatie kopie voorbereiden en aanpassen-Azure
description: Een installatie kopie van een virtueel bureau blad van Windows voorbereiden, aanpassen en uploaden naar Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33af83934e8ecc5745f2edad3a7832a870406452
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602382"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Een VHD-hoofdinstallatiekopie voorbereiden en aanpassen

In dit artikel leest u hoe u een installatie kopie van een virtuele harde schijf (VHD) voor het uploaden naar Azure voorbereidt, inclusief het maken van virtuele machines (Vm's) en het installeren van software. Deze instructies gelden voor een Windows-specifieke configuratie voor virtueel bureau blad die kan worden gebruikt met de bestaande processen van uw organisatie.

>[!IMPORTANT]
>U wordt aangeraden een installatie kopie te gebruiken uit de galerie met installatie kopieën van Azure. Als u echter een aangepaste installatie kopie nodig hebt, moet u ervoor zorgen dat de virtuele WIndows-bureau blad-agent nog niet op uw apparaat is geïnstalleerd. Het gebruik van een aangepaste installatie kopie met de virtueel-bureaublad agent van Windows kan problemen veroorzaken met de installatie kopie.  

## <a name="create-a-vm"></a>Een virtuele machine maken

Windows 10 Enter prise multi-session is beschikbaar in de galerie met installatie kopieën van Azure. Er zijn twee opties voor het aanpassen van deze installatie kopie.

De eerste optie is het inrichten van een virtuele machine (VM) in azure door de instructies te volgen in [een VM maken op basis van een beheerde installatie kopie](../virtual-machines/windows/create-vm-generalized-managed.md)en vervolgens door te gaan naar de voor [bereiding en installatie van software](set-up-customize-master-image.md#software-preparation-and-installation).

De tweede optie is om de installatie kopie lokaal te maken door de installatie kopie te downloaden, een Hyper-V-VM in te richten en deze aan te passen aan uw behoeften, die in de volgende sectie worden behandeld.

### <a name="local-image-creation"></a>Lokale installatie kopie maken

Zodra u de installatie kopie naar een lokale locatie hebt gedownload, opent u **Hyper-V-beheer** om een virtuele machine te maken met de VHD die u hebt gekopieerd. De volgende instructies zijn een eenvoudige versie, maar u vindt meer gedetailleerde instructies voor het [maken van een virtuele machine in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Een virtuele machine maken met de gekopieerde VHD:

1. Open de **wizard Nieuwe virtuele machine**.

2. Selecteer op de pagina generatie opgeven de optie **generatie 1**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de pagina voor het genereren van opgeven. De optie ' generatie 1 ' is geselecteerd.](media/a41174fd41302a181e46385e1e701975.png)

3. Schakel onder controlepunt type de controle punten uit door het selectie vakje uit te scha kelen.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de sectie type controle punt van de pagina met controle punten.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

U kunt ook de volgende cmdlet uitvoeren in Power shell om controle punten uit te scha kelen.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Vaste schijf

Als u een virtuele machine maakt op basis van een bestaande VHD, wordt standaard een dynamische schijf gemaakt. U kunt het wijzigen naar een vaste schijf door **schijf bewerken** te selecteren, zoals wordt weer gegeven in de volgende afbeelding. Zie [een Windows VHD of VHDX voorbereiden voor het uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)voor meer gedetailleerde instructies.

> [!div class="mx-imgBorder"]
> ![Een scherm afbeelding van de optie voor het bewerken van de schijf.](media/35772414b5a0f81f06f54065561d1414.png)

U kunt ook de volgende Power shell-cmdlet uitvoeren om de schijf naar een vaste schijf te wijzigen.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Software voorbereiding en-installatie

In deze sectie wordt beschreven hoe u FSLogix en Windows Defender voorbereidt en installeert, evenals een aantal basis configuratie opties voor apps en het REGI ster van uw installatie kopie.

Als u Microsoft 365 apps voor Enter prise en OneDrive op uw VM installeert, gaat u naar [Office installeren op een Master-VHD-installatie kopie](install-office-on-wvd-master-image.md) en volgt u de instructies voor het installeren van de apps. Wanneer u klaar bent, keert u terug naar dit artikel.

Als uw gebruikers toegang moeten hebben tot bepaalde LOB-toepassingen, raden we u aan deze te installeren nadat u de instructies van deze sectie hebt voltooid.

### <a name="set-up-user-profile-container-fslogix"></a>Gebruikers profiel container instellen (FSLogix)

Als u de container FSLogix wilt opnemen als onderdeel van de installatie kopie, volgt u de instructies in [een profiel container maken voor een hostgroep met een bestands share](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). U kunt de functionaliteit van de FSLogix-container testen met [deze Snelstartgids](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Windows Defender configureren

Als Windows Defender is geconfigureerd in de virtuele machine, moet u ervoor zorgen dat de volledige inhoud van VHD-en VHDX-bestanden tijdens een bijlage niet wordt gescand.

Deze configuratie verwijdert alleen het scannen van VHD-en VHDX-bestanden tijdens een bijlage, maar heeft geen invloed op real-time scans.

Zie [Windows Defender anti virus-uitsluitingen configureren op Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)voor meer gedetailleerde instructies voor het configureren van Windows Defender op Windows Server.

Zie [uitsluitingen configureren en valideren op basis van bestands extensie en maplocatie](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)voor meer informatie over het configureren van Windows Defender om bepaalde bestanden uit te sluiten.

### <a name="disable-automatic-updates"></a>Automatische updates uitschakelen

Automatische updates via lokale groepsbeleid uitschakelen:

1. Open **Lokale groepsbeleidsobjecteditor \\ Beheersjablonen \\ Windows-onderdelen \\ Windows Update**.
2. Klik met de rechter muisknop op **Automatische updates configureren** en stel deze in op **uitgeschakeld**.

U kunt ook de volgende opdracht uitvoeren op een opdracht prompt om Automatische updates uit te scha kelen.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Start indeling voor Windows 10-Pc's opgeven (optioneel)

Voer deze opdracht uit om een start indeling op te geven voor Windows 10-Pc's.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Tijd zone-omleiding instellen

Tijdzone omleiding kan worden afgedwongen op groepsbeleid niveau, omdat alle virtuele machines in een hostgroep deel uitmaken van dezelfde beveiligings groep.

Tijd zones omleiden:

1. Open de **console Groepsbeleidbeheer** op de Active Directory-server.
2. Breid uw domein en groepsbeleid objecten uit.
3. Klik met de rechter muisknop op het **Groepsbeleid-object** dat u hebt gemaakt voor de instellingen voor groeps beleid en selecteer **bewerken**.
4. Ga in het **Groepsbeleidsbeheer-editor** naar **computer configuratie**  >  **beleid**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad** apparaat voor sessie-host  >  **en bron omleiding**.
5. Schakel de instelling **tijd zone omleiding toestaan** in.

U kunt deze opdracht ook uitvoeren op de hoofd installatie kopie om tijd zones om te leiden:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Opslag Sense uitschakelen

Voor Windows Virtual Desktop Session Host die gebruikmaakt van Windows 10 Enter prise of Windows 10 Enter prise multi-session, raden we u aan om opslag Sense uit te scha kelen. U kunt opslag Sense uitschakelen in het menu instellingen onder **opslag**, zoals wordt weer gegeven in de volgende scherm afbeelding:

> [!div class="mx-imgBorder"]
> ![Een scherm opname van het menu opslag onder instellingen. De optie ' opslag Sense ' is uitgeschakeld.](media/storagesense.png)

U kunt de instelling ook wijzigen met het REGI ster door de volgende opdracht uit te voeren:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Aanvullende taal ondersteuning toevoegen

Dit artikel heeft geen betrekking op het configureren van taal-en regionale ondersteuning. Raadpleeg voor meer informatie de volgende artikelen:

- [Talen toevoegen aan Windows-installatie kopieën](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Onderdelen op aanvraag](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Taal-en regio functies op aanvraag (DOM)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Andere toepassingen en register configuratie

In deze sectie wordt de configuratie van toepassingen en besturings systemen besproken. Alle configuratie in deze sectie wordt uitgevoerd via Register vermeldingen die kunnen worden uitgevoerd met behulp van de opdracht regel-en Regedit-hulpprogram ma's.

>[!NOTE]
>U kunt best practices in de configuratie implementeren met behulp van groepsbeleid objecten (Gpo's) of register invoer. De beheerder kan een van beide opties kiezen op basis van de vereisten van de organisatie.

Voer de volgende opdracht uit voor het verzamelen van telemetrie-gegevens over Windows 10 Enter prise met een feedback hub:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Voer de volgende opdracht uit om Watson-crashes op te lossen:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Voer de volgende opdrachten in de REGI ster-editor in om ondersteuning voor 5k-oplossingen te herstellen. U moet de opdrachten uitvoeren voordat u de side-by-side-stack kunt inschakelen.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>De installatie kopie voorbereiden voor uploaden naar Azure

Nadat u de configuratie hebt voltooid en alle toepassingen hebt geïnstalleerd, volgt u de instructies in [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) om de installatie kopie voor te bereiden.

Nadat de installatie kopie is voor bereid voor het uploaden, moet u ervoor zorgen dat de virtuele machine de status uitgeschakeld of niet-toegewezen heeft.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Master installatie kopie uploaden naar een opslag account in azure

Deze sectie is alleen van toepassing wanneer de master installatie kopie lokaal is gemaakt.

In de volgende instructies wordt uitgelegd hoe u uw master installatie kopie uploadt naar een Azure-opslag account. Als u nog geen Azure Storage-account hebt, volgt u de instructies in [dit artikel](/azure/developer/javascript/tutorial-vscode-static-website-node-03) om er een te maken.

1. Converteer de VM-installatie kopie (VHD) naar vast als u dat nog niet hebt gedaan. Als u de afbeelding niet naar vast hebt geconverteerd, is het niet mogelijk om de installatie kopie te maken.

2. Upload de VHD naar een BLOB-container in uw opslag account. U kunt snel uploaden met het [Storage Explorer-hulp programma](https://azure.microsoft.com/features/storage-explorer/). Zie [dit artikel](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)voor meer informatie over het Storage Explorer-hulp programma.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van het zoek venster van het Microsoft Azure Storage Explorer-hulp programma. Het selectie vakje upload. VHD-of vhdx-bestanden als pagina-blobs (aanbevolen) is ingeschakeld.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ga vervolgens naar de Azure Portal in uw browser en zoek naar ' installatie kopieën '. Uw zoek opdracht leidt u naar de pagina **installatie kopie maken** , zoals wordt weer gegeven in de volgende scherm afbeelding:

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de pagina afbeelding maken van het Azure Portal, gevuld met voorbeeld waarden voor de afbeelding.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Wanneer u de installatie kopie hebt gemaakt, ziet u een melding zoals de afbeelding in de volgende scherm afbeelding:

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de melding ' installatie kopie is gemaakt '.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een installatie kopie hebt, kunt u hostgroepen maken of bijwerken. Raadpleeg de volgende artikelen voor meer informatie over het maken en bijwerken van hostgroepen:

- [Een hostpool maken met een Azure Resource Manager-sjabloon](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Zelfstudie: Een hostpool maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)
- [Een profielcontainer maken voor een hostpool met behulp van een bestandsshare](create-host-pools-user-profile.md)
- [De taakverdelingsmethode voor Windows Virtual Desktop configureren](configure-host-pool-load-balancing.md)