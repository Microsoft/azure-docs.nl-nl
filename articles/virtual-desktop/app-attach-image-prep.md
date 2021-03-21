---
title: Windows virtueel bureau blad MSIX-app voor het koppelen van installatie kopieën voor beeld-Azure voorbereiden
description: Een MSIX-app voor het koppelen van een installatie kopie maken voor een Windows-hostgroep voor virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97425885"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Een MSIX-installatie kopie voorbereiden voor het virtuele bureau blad van Windows

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

MSIX app attach (preview) is een oplossing voor toepassings lagen waarmee u op dynamische wijze apps van een MSIX-pakket kunt koppelen aan een gebruikers sessie. Het MSIX-pakket systeem scheidt apps van het besturings systeem, waardoor het eenvoudiger is om installatie kopieën voor virtuele machines te maken. MSIX-pakketten geven u meer controle over welke apps uw gebruikers op hun virtuele machines kunnen gebruiken. U kunt zelfs apps scheiden van de master installatie kopie en deze later aan gebruikers geven.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Een VHD-of VHDX-pakket maken voor MSIX

MSIX-pakketten moeten zich in een VHD-of VHDX-indeling bezitten om goed te kunnen werken. Dit betekent dat u een VHD-of VHDX-pakket moet maken om aan de slag te gaan.

>[!NOTE]
>Als u dat nog niet hebt gedaan, moet u Hyper-V inschakelen door de instructies te volgen in [hyper-v installeren op Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Een VHD-of VHDX-pakket maken voor MSIX:

1. Open eerst Power shell.
2. Voer vervolgens de volgende cmdlet uit om een VHD te maken:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Zorg ervoor dat de VHD groot genoeg is om het uitgevouwen MSIX-pakket te bewaren.

3. Voer de volgende cmdlet uit om de VHD die u zojuist hebt gemaakt, te koppelen:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Voer vervolgens deze cmdlet uit om de gekoppelde VHD te initialiseren:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Daarna voert u deze cmdlet uit om een nieuwe partitie te maken voor de geïnitialiseerde VHD:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Voer deze cmdlet uit om de partitie te Format teren:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Maak ten slotte een bovenliggende map op de gekoppelde VHD. Deze stap is vereist omdat het MSIX-pakket een bovenliggende map moet hebben om goed te kunnen werken. Het maakt niet uit wat u de naam van de bovenliggende map hebt, zolang de bovenliggende map bestaat.

## <a name="expand-msix"></a>MSIX uitvouwen

Daarna moet u de MSIX-installatie kopie uitbreiden door de bestanden uit te pakken naar de VHD.

De MSIX-installatie kopie uitbreiden:

1. [Down load het msixmgr-hulp programma](https://aka.ms/msixmgr) en sla de zip-map op in een map in een sessie-host-VM.

2. Pak de map msixmgr. zip.

3. Plaats het bron MSIX-pakket in dezelfde map waarin u het msixmgr-hulp programma hebt uitgepakt.

4. Open een opdracht prompt als beheerder en navigeer naar de map waar u het hulp programma msixmgr hebt gedownload en uitgepakt.

5. Voer de volgende cmdlet uit om de MSIX uit te pakken naar de VHD die u in de vorige sectie hebt gemaakt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Het volgende bericht moet worden weer gegeven nadat u het pakket hebt uitgepakt:

    > De uitpakken en toegepaste Acl's voor het pakket:. msix zijn ongedaan gemaakt <package name>

    >[!NOTE]
    > Als u pakketten gebruikt van de Microsoft Store voor bedrijven of onderwijs in uw netwerk of op apparaten die niet zijn verbonden met internet, moet u pakket licenties downloaden en installeren via de Microsoft Store om de apps uit te voeren. Zie [pakketten offline gebruiken](app-attach.md#use-packages-offline)om de licenties op te halen.

6. Ga naar de gekoppelde VHD en open de map app om te controleren of de inhoud van het pakket zich daar bevindt.

7. Ontkoppel de VHD.

## <a name="upload-msix-image-to-share"></a>MSIX-afbeelding uploaden naar share

Nadat u het MSIX-pakket hebt gemaakt, moet u het resulterende VHD-, VHDX-of CIM-bestand uploaden naar een share waar de virtuele machines van uw gebruikers toegang hebben.

## <a name="next-steps"></a>Volgende stappen

Vraag onze vragen van de community over deze functie op het [virtuele bureau blad-TechCommunity van Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Hier volgen enkele andere artikelen die u mogelijk handig vindt:

- [Woorden lijst voor het toevoegen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)