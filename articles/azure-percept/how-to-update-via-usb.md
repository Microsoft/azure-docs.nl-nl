---
title: Uw Azure percept DK bijwerken via een USB-verbinding
description: Meer informatie over hoe u Azure percept DK bijwerkt via een USB-verbinding
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 12f6acda632b9c0fbee2db570df5293c1daf32ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720811"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Azure percept DK bijwerken via een USB-verbinding

Hoewel het gebruik van OTA-updates (over-the-Air) de beste manier is om het besturings systeem en de firmware van uw dev kit up-to-date te houden, zijn er scenario's waarin het bijwerken (of ' knipperen ') de dev kit via een USB-verbinding nodig heeft:

- Een OTA-update is niet mogelijk vanwege connectiviteit of andere technische problemen
- Het apparaat moet opnieuw worden ingesteld op de fabrieks status

In deze hand leiding wordt uitgelegd hoe u het besturings systeem en de firmware van uw dev kit kunt bijwerken via een USB-verbinding.

> [!WARNING]
> Als u uw dev kit via USB bijwerkt, worden alle bestaande gegevens op het apparaat verwijderd, met inbegrip van AI-modellen en containers.
>
> Volg alle instructies in de aangegeven volg orde. Als u de stappen overs laat, kan de ontwikkelaars Kit een onbruik bare status hebben.

## <a name="prerequisites"></a>Vereisten

- Een Azure percept DK
- Een Windows-, Linux-of OS X-hostcomputer met Wi-Fi mogelijkheid en een beschik bare USB-C-of USB-poort
- Een USB-C naar USB-A-kabel (optioneel, apart verkrijgbaar)
- Een SSH-aanmelding, gemaakt tijdens de [installatie-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Software hulpprogramma's en update bestanden downloaden

1. [NXP UUU-hulp programma](https://github.com/NXPmicro/mfgtools/releases). Down load de **nieuwste versie** van uuu.exe bestand (voor Windows) of het UUU-bestand (voor Linux) op het tabblad **assets** .

1. [7-zip](https://www.7-zip.org/). Deze software wordt gebruikt voor het uitpakken van het onbewerkte installatie kopie bestand uit het gecomprimeerde XZ-bestand. Down load en installeer het juiste exe-bestand.

1. [Down load de update bestanden](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Zorg ervoor dat alle drie de build-artefacten aanwezig zijn:
    - Azure-percept-DK-*&lt; versie nummer &gt;*. RAW. xz
    - Fast-hab-FW. RAW
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Uw omgeving instellen

1. Maak een map op de hostcomputer op een locatie die toegankelijk is via de opdracht regel.

1. Kopieer het hulp programma UUU (**uuu.exe** of **UUU**) naar de nieuwe map.

1. Pak het bestand **Azure-percept-DK-*&lt; versie &gt; nummer*. RAW** uit het gecomprimeerde bestand door met de rechter muisknop op **Azure-percept-DK-*&lt; versie nummer &gt;*. RAW. xz** te klikken en **7-zip** &gt; **extract hier** te selecteren.

1. Verplaats het uitgepakte **Azure-percept-DK-*&lt; versie &gt; nummer*. RAW-** bestand, **Fast-hab-FW. RAW** en **emmc_full.txt** naar de map met het hulp programma UUU.

## <a name="update-your-device"></a>Uw apparaat bijwerken

1. [Ssh in uw dev kit](./how-to-ssh-into-percept-dk.md).

1. Open vervolgens een Windows-opdracht prompt (**Start**  >  **cmd**) of een Linux-Terminal en navigeer naar de map waarin het hulp programma Update bestanden en UUU is opgeslagen. Voer de volgende opdracht uit in de opdracht prompt of terminal om de computer voor te bereiden op het ontvangen van een versneld apparaat:

    - Windows:

        ```bash
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Koppel het Azure percept Vision-apparaat los van de USB-C-poort van de vervoerders kaart.

1. Verbind de geleverde USB-C-kabel met de USB-C-poort van de draaggolf kaart en de USB-C-poort van de hostcomputer. Als uw computer alleen een USB-A-poort heeft, sluit u een USB-C aan op een USB-kabel (apart verkocht) aan de draaggolf kaart en de hostcomputer.

1. In de SSH-client prompt voert u de volgende opdrachten in:

    1. Stel het apparaat in op de USB-update modus:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Start het apparaat opnieuw. De installatie van de update wordt gestart.

        ```bash
        sudo reboot -f
        ```

1. Ga terug naar de andere opdracht prompt of Terminal. Wanneer de update is voltooid, wordt het volgende bericht weer gegeven ```Success 1    Failure 0``` :

    > [!NOTE]
    > Na het bijwerken wordt uw apparaat opnieuw ingesteld op de fabrieks instellingen en verliest u de Wi-Fi verbinding en SSH-aanmelding.

1. Als de update is voltooid, schakelt u het verwerkings bord uit. Koppel de USB-kabel los van de PC.  

## <a name="next-steps"></a>Volgende stappen

Werk met de [Setup-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) om uw apparaat opnieuw te configureren.