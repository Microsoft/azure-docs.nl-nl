---
title: 'Azure Lab Services: een aangepaste installatie kopie uploaden naar de galerie met gedeelde afbeeldingen'
description: Hierin wordt beschreven hoe u een aangepaste installatie kopie uploadt naar de galerie met gedeelde afbeeldingen. De IT-afdelingen van de Universiteit zullen de installatie kopieën bijzonder nuttig kunnen importeren.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787160"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Een aangepaste afbeelding uploaden naar Shared Image Gallery

Galerie met gedeelde afbeeldingen is beschikbaar voor u voor het importeren van uw eigen aangepaste installatie kopieën voor het maken van Labs in Azure Lab Services. De IT-afdeling van de Universiteit heeft de installatie kopieën die bijzonder nuttig zijn om de volgende redenen te importeren: 

* U hoeft niet hand matig installatie kopieën te maken met behulp van de VM van de Lab-sjabloon.
* U kunt installatie kopieën die zijn gemaakt met andere hulpprogram ma's, zoals SCCM, Endpoint Manager, enzovoort uploaden.

In dit artikel worden de stappen beschreven die u kunt uitvoeren om een aangepaste installatie kopie te maken en te gebruiken in Azure Lab Services. 

> [!IMPORTANT]
> Wanneer u uw installatie kopieën van een fysieke test omgeving naar AZ Labs verplaatst, moet u deze appropriatly opnieuw structureren. Gebruik de bestaande installatie kopieën niet gewoon van fysieke Labs. <br/>Zie de [overstap van een fysiek lab naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blog post voor meer informatie.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Aangepaste installatie kopie van een fysieke test omgeving halen

De volgende stappen laten zien hoe u een aangepaste installatie kopie importeert die wordt gestart vanuit een fysieke test omgeving. Er wordt vervolgens een VHD gemaakt op basis van deze omgeving en geïmporteerd in de galerie met gedeelde afbeeldingen in azure, zodat deze kan worden gebruikt in Azure Lab Services.

Er zijn veel opties voor het maken van een VHD vanuit een fysieke test omgeving. De volgende stappen laten zien hoe u een VHD maakt op basis van een Windows Hyper-V-VM:

1. Begin met een Hyper-V-VM in uw fysieke test omgeving die is gemaakt op basis van uw installatie kopie.
    1. De virtuele machine moet worden gemaakt als virtuele machine van de eerste generatie.
    1. De virtuele machine moet een vaste schijf grootte gebruiken. U kunt ook de grootte van de schijf in dit venster opgeven. De schijf grootte mag niet groter zijn dan 128 GB.<br/>    
    Installatie kopieën met schijf grootte > 128 GB worden niet ondersteund door Azure Lab Services. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Virtuele harde schijf verbinden":::   
    1. Installatie kopie de virtuele machine zoals u dat gewend bent.
1. [Maak verbinding met de virtuele machine en bereid deze voor op Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Windows-configuraties instellen voor Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [Controleer de Windows-services die het mini maal nodig hebben om verbinding met de virtuele machine te garanderen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [Register instellingen voor extern bureau blad bijwerken](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Windows Firewall-regels configureren](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Windows-updates installeren
    1. [Installeer de Azure VM-agent en de aanvullende configuratie, zoals hier wordt weer gegeven](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    In de bovenstaande stappen wordt een gespecialiseerde afbeelding gemaakt. Als u een gegeneraliseerde installatie kopie maakt, moet u ook [Sysprep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)uitvoeren. <br/>
        U moet een gespecialiseerde installatie kopie maken als u wilt dat de gebruikers lijst (die bestanden, gebruikers accountgegevens, enzovoort) bevat die nodig is voor software die is opgenomen in de installatie kopie.
1. Omdat **Hyper-V** standaard een **VHDX** -bestand maakt, moet u dit converteren naar een VHD-bestand.
    1. Navigeer naar **Hyper-V-beheer**  ->  **actie**  ->  **schijf bewerken**.
    1. Hier hebt u de mogelijkheid om de schijf van een VHDX te **converteren** naar een VHD
    1. Wanneer u de schijf grootte wilt uitbreiden, moet u ervoor zorgen dat deze niet groter is dan 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Actie kiezen":::   
1. Upload de VHD naar Azure om een beheerde schijf te maken.
    1. U kunt Storage Explorer of AzCopy gebruiken vanaf de opdracht regel, zoals beschreven in [een VHD uploaden naar Azure of een beheerde schijf kopiëren naar een andere regio](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).        
    Als uw computer overschakelt naar de slaap stand of vergrendeld, kan het upload proces worden onderbroken en mislukt.
    1. Het resultaat van deze stap is dat u nu een beheerde schijf hebt die u in de Azure Portal kunt zien. 
        U kunt het tabblad ' Size\Performance ' van het Azure Portal gebruiken om de grootte van de schijf te kiezen. Zoals eerder vermeld, moet de grootte niet > 128 GB zijn.
1. Maak een moment opname van de beheerde schijf.
    Dit kan worden gedaan vanuit Power shell, met behulp van de Azure Portal, of vanuit Storage Explorer, zoals beschreven in [een moment opname maken met behulp van de portal of Power shell](../virtual-machines/windows/snapshot-copy-managed-disk.md).
1. Maak in de galerie gedeelde afbeeldingen een definitie en versie van de installatie kopie:
    1. [Een definitie van een installatie kopie maken](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).
    1. U moet hier ook opgeven of u een gespecialiseerde/gegeneraliseerde installatie kopie maakt.
1. Maak het lab in Azure Lab Services en selecteer de aangepaste installatie kopie in de galerie met gedeelde afbeeldingen.

    Als u schijf hebt uitgebreid nadat het besturings systeem is geïnstalleerd op de oorspronkelijke Hyper-V-VM, moet u ook het station C in Windows uitbreiden om de niet-toegewezen schijf ruimte te gebruiken. Als u dit wilt doen, meldt u zich aan bij de sjabloon-VM nadat het lab is gemaakt en voert u de stappen uit die vergelijkbaar zijn met wat wordt weer gegeven in [een basis volume uitbreiden](/windows-server/storage/disk-management/extend-a-basic-volume). Er zijn opties om dit te doen via de gebruikers interface en met behulp van Power shell.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Galerie gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md)
* [De galerie met gedeelde afbeeldingen gebruiken](how-to-use-shared-image-gallery.md)