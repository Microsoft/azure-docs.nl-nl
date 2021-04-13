---
title: Windows Virtual Desktop msix-app-attach-afbeelding voorbereiden - Azure
description: Een msix-app-attach-afbeelding maken voor een Windows Virtual Desktop hostgroep.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363662"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Een MSIX-afbeelding voorbereiden voor Windows Virtual Desktop

MsIX-app-attach is een oplossing voor toepassingslagen waarmee u apps van een MSIX-pakket dynamisch kunt koppelen aan een gebruikerssessie. Het MSIX-pakketsysteem scheidt apps van het besturingssysteem, waardoor het eenvoudiger wordt om installatie afbeeldingen voor virtuele machines te bouwen. MSIX-pakketten bieden u ook meer controle over de apps die uw gebruikers op hun virtuele machines kunnen gebruiken. U kunt zelfs apps scheiden van de hoofdafbeelding en deze later aan gebruikers geven.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Een VHD- of VHDX-pakket voor MSIX maken

MSIX-pakketten moeten een VHD- of VHDX-indeling hebben om goed te kunnen werken. Dit betekent dat u om te beginnen een VHD- of VHDX-pakket moet maken.

>[!NOTE]
>Als u dat nog niet hebt gedaan, moet u Hyper-V inschakelen door de instructies in [Hyper-V installeren op](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)Windows 10.

Een VHD- of VHDX-pakket voor MSIX maken:

1. Open eerst PowerShell.
2. Voer vervolgens de volgende cmdlet uit om een VHD te maken:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Zorg ervoor dat de VHD groot genoeg is voor het uitgebreide MSIX-pakket.

3. Voer de volgende cmdlet uit om de VHD te maken die u zojuist hebt gemaakt:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Voer vervolgens deze cmdlet uit om de gemonteerde VHD te initialiseren:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Voer daarna deze cmdlet uit om een nieuwe partitie te maken voor de geinitialiseerde VHD:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Voer deze cmdlet uit om de partitie op te maken:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Maak ten slotte een bovenliggende map op de bevestigings-VHD. Deze stap is vereist omdat het MSIX-pakket een bovenliggende map moet hebben om goed te kunnen werken. Het maakt niet uit wat u de bovenliggende map noemt, zolang de bovenliggende map bestaat.

## <a name="expand-msix"></a>MSIX uitbreiden

Daarna moet u de MSIX-afbeelding uitbreiden door de bestanden uit te pakken in de VHD.

De MSIX-afbeelding uitbreiden:

1. [Download het hulpprogramma msixmgr en](https://aka.ms/msixmgr) sla de map .zip op in een map in een sessiehost-VM.

2. Unzip the msixmgr tool .zip folder.

3. Plaats het MSIX-bronpakket in dezelfde map waarin u het hulpprogramma msixmgr hebt uitgepakt.

4. Open als beheerder een opdrachtprompt en navigeer naar de map waar u het hulpprogramma msixmgr hebt gedownload en uitgepakt.

5. Voer de volgende cmdlet uit om de MSIX uit te pakken in de VHD die u in de vorige sectie hebt gemaakt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Het volgende bericht wordt weergegeven nadat u klaar bent met uitpakken:

    > ACL's zijn uitgepakt en toegepast voor pakket: <package name> .msix

    >[!NOTE]
    > Als u pakketten van de Microsoft Store voor Bedrijven of Education in uw netwerk gebruikt of op apparaten die niet zijn verbonden met internet, moet u pakketlicenties downloaden en installeren van de Microsoft Store om de apps uit te voeren. Zie Pakketten offline gebruiken om de [licenties op te halen.](app-attach.md#use-packages-offline)

6. Ga naar de VHD die is bevestigd en open de map app om te controleren of de inhoud van het pakket daar staat.

7. Ontkoppel de VHD.

## <a name="upload-msix-image-to-share"></a>MSIX-afbeelding uploaden om te delen

Nadat u het MSIX-pakket hebt gemaakt, moet u het resulterende VHD-, VHDX- of CIM-bestand uploaden naar een share waar de virtuele machines van uw gebruikers toegang toe hebben.

## <a name="next-steps"></a>Volgende stappen

Stel onze communityvragen over deze functie op de [Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

U kunt ook feedback geven voor Windows Virtual Desktop in Windows Virtual Desktop [feedbackhub.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Hier zijn enkele andere artikelen die u mogelijk nuttig vindt:

- [Verklarende woordenlijst voor het koppelen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)