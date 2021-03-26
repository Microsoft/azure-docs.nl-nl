---
title: Overzicht van het installatie programma Azure percept dev tools pack
description: Meer informatie over het gebruik van het installatie programma dev tools pack om geavanceerde ontwikkeling met Azure percept te versnellen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608592"
---
# <a name="dev-tools-pack-installer-overview"></a>Overzicht van het installatie programma dev tools pack

Het installatie programma voor dev tools pack is een oplossing voor het oplossen van oplossingen waarmee alle hulpprogram ma's die nodig zijn voor het ontwikkelen van een geavanceerde intelligente rand oplossing, worden geïnstalleerd en geconfigureerd.

## <a name="mandatory-tools"></a>Verplichte hulpprogram ma's

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 of hoger](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [Tensor flow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Optionele hulpprogram ma's

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit voor het ontwikkelen van oplossingen voor nvidia-Accelerators)
* [Intel Openwijn toolkit 2020,2](https://docs.openvinotoolkit.org/) (Toolkit voor het ontwikkelen van oplossingen voor Intel-Accelerators)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) of 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Bekende problemen

- Optionele Caffe-installatie kan mislukken als docker niet goed wordt uitgevoerd. Als u Caffe wilt installeren, moet u ervoor zorgen dat docker is geïnstalleerd en wordt uitgevoerd voordat u de installatie van Caffe via het installatie programma dev tools pack uitvoert.

- Optionele CUDA-installatie mislukt op niet-compatibele systemen. Voordat u probeert de [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) te installeren via het installatie programma dev tools pack, moet u de systeem compatibiliteit controleren.

## <a name="docker-minimum-requirements"></a>Minimale vereisten voor docker

### <a name="windows"></a>Windows

- Windows 10 64-bits: Pro, Enter prise of Education (build 16299 of hoger).

- De Windows-onderdelen van Hyper-V en containers moeten zijn ingeschakeld. De volgende hardware-vereisten zijn vereist voor het uitvoeren van Hyper-V in Windows 10:

    - 64-bits processor met het [tweede niveau adres omzetting (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - 4 GB RAM-geheugen
    - Ondersteuning voor hardware-virtualisatie op BIOS-niveau moet zijn ingeschakeld in de BIOS-instellingen. Zie virtualisatie voor meer informatie.

> [!NOTE]
> Docker ondersteunt docker desktop in Windows op basis van de ondersteunings levenscyclus van micro soft voor Windows 10-besturings systeem. Zie het [Windows levenscyclus](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)-overzicht voor meer informatie.

Meer informatie over het [installeren van docker desktop in Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- Mac moet een 2010-of een nieuwer model zijn met de volgende kenmerken:
    - Intel-processor
    - Ondersteuning voor MMU-virtualisatie (Extended Page Tables) (EPT) en onbeperkte modus voor Intel-hardware. U kunt controleren of de computer deze ondersteuning heeft door de volgende opdracht uit te voeren in een Terminal: ```sysctl kern.hv_support``` . Als uw Mac het Hyper Visor-Framework ondersteunt, wordt de opdracht afgedrukt ```kern.hv_support: 1``` .

- macOS-versie 10,14 of hoger (Mojave, Catalina of Big Sur). U kunt het beste een upgrade uitvoeren naar de nieuwste versie van macOS. Als u problemen ondervindt nadat u uw macOS hebt bijgewerkt naar versie 10,15, moet u de nieuwste versie van docker Desktop installeren om compatibel te zijn met deze versie van macOS.

- Ten minste 4 GB RAM-geheugen.

- Installeer VirtualBox niet vóór versie 4.3.30--het is niet compatibel met docker Desktop.

- Het installatie programma wordt niet ondersteund op Apple M1.

Meer informatie over het [installeren van docker Desktop op Mac](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Start het installatieprogramma

Down load het installatie programma voor dev tools pack voor [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)of [Mac](https://go.microsoft.com/fwlink/?linkid=2132296). Start het installatie programma op basis van uw platform, zoals hieronder wordt beschreven.

### <a name="windows"></a>Windows

1. Klik op **dev-tools-Pack-Installer** om de installatie wizard te openen.

### <a name="mac"></a>Mac

1. Nadat u het bestand hebt gedownload, verplaatst u het **dev-tools-Pack-Installer.app** naar de map **toepassingen** .

1. Klik op **dev-tools-Pack-Installer.app** om de installatie wizard te openen.

1. Als u het beveiligings dialoogvenster ' onbekende ontwikkelaar ' ontvangt:

    1. Ga naar **systeem voorkeuren**  ->  **beveiliging & privacy**  ->  **Algemeen** en klik op **toch openen** naast **dev-tools-Pack-Installer.app**.
    1. Klik op het pictogram van het elektroon.
    1. Klik in het dialoog venster beveiliging op **openen** .

### <a name="linux"></a>Linux

1. Wanneer u wordt gevraagd door de browser, klikt u op **Opslaan** om het downloaden van het installatie programma te volt ooien.

1. Uitvoerings machtigingen toevoegen aan het **. appimage** -bestand:

    1. Open een Linux-Terminal.

    1. Voer het volgende in de terminal in om naar de map **down loads** te gaan:

        ```bash
        cd ~/Downloads/
        ```

    1. Maak het uitvoer bare AppImage-bestand:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Voer het installatie programma uit:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Uitvoerings machtigingen toevoegen aan het **. appimage** -bestand:

    1. Klik met de rechter muisknop op het. appimage-bestand en selecteer **Eigenschappen**.
    1. Open het tabblad **machtigingen** .
    1. Schakel het selectie vakje in naast het **uitvoeren van het bestand als een programma toestaan**.
    1. Sluit de **Eigenschappen** en open het **. appimage** -bestand.

## <a name="run-the-installer"></a>Het installatieprogramma uitvoeren

1. Op de installatie pagina **dev tools pack installeren** klikt u op **licentie weer geven** om de licentie overeenkomsten weer te geven van elk software pakket dat is opgenomen in het installatie programma. Als u akkoord gaat met de voor waarden in de licentie overeenkomsten, schakelt u het selectie vakje in en klikt u op **volgende**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Het scherm licentie overeenkomst in het installatie programma.":::

1. Klik op **privacyverklaring** om de privacyverklaring van micro soft te bekijken. Als u akkoord gaat met de voor waarden van de privacyverklaring en diagnostische gegevens naar micro soft wilt verzenden, selecteert u **Ja** en klikt u op **volgende**. Als dat niet het geval is, selecteert u **Nee** en klikt u op **volgende**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Het venster overeenkomst met de privacyverklaring in het installatie programma.":::

1. Selecteer op de pagina **onderdelen configureren** de optionele hulpprogram ma's die u wilt installeren (de verplichte hulpprogram ma's worden standaard geïnstalleerd).

    1. Als u werkt met het SoM van Azure percept audio, dat deel uitmaakt van Azure percept DK, moet u ervoor zorgen dat u de Intel Open Toolkit en Miniconda3 installeert.

    1. Klik op **installeren** om door te gaan met de installatie.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Scherm van installatie programma waarin beschik bare software pakketten worden weer gegeven.":::

1. Nadat de installatie van alle geselecteerde onderdelen is voltooid, gaat de wizard door naar de pagina **de wizard Setup volt ooien** . Klik op **volt ooien** om het installatie programma af te sluiten.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Scherm voor het volt ooien van het installatie programma.":::

## <a name="docker-status-check"></a>Controle van docker-status

Als het installatie programma u waarschuwt dat docker Desktop goed wordt uitgevoerd, raadpleegt u de volgende stappen:

### <a name="windows"></a>Windows

1. Vouw verborgen pictogrammen in systeemvak uit.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Systeemvak.":::

1. Controleer of het pictogram docker Desktop wordt uitgevoerd op het bureau blad van **docker**.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-status.":::

1. Als het bovenstaande pictogram in het systeemvak niet wordt weer gegeven, start u docker Desktop vanuit het menu Start.

1. Als docker u vraagt om opnieuw op te starten, kunt u het installatie programma sluiten en opnieuw starten nadat het opnieuw opstarten is voltooid en de docker actief is. Alle geslaagde toepassingen van derden moeten worden gedetecteerd en worden niet automatisch opnieuw geïnstalleerd.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [Azure percept Advanced Development-opslag plaats](https://github.com/microsoft/azure-percept-advanced-development) om aan de slag te gaan met geavanceerde ontwikkeling voor Azure percept DK.
