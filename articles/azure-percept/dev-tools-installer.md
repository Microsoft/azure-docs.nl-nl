---
title: Overzicht van het installatie programma Azure percept dev tools pack
description: Meer informatie over het gebruik van het installatie programma dev tools pack om geavanceerde ontwikkeling met Azure percept te versnellen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503212"
---
# <a name="dev-tools-pack-installer-overview"></a>Overzicht van het installatie programma dev tools pack

Het installatie programma voor dev tools pack is een oplossing voor het oplossen van oplossingen waarmee alle hulpprogram ma's die nodig zijn voor het ontwikkelen van een intelligente EDGE-oplossing, worden geïnstalleerd en geconfigureerd. Als u een van de hieronder vermelde software pakketten al hebt geïnstalleerd, installeert het installatie programma voor dev tools pack die pakketten opnieuw zodat uw hulpprogram ma's consistent zijn met de versie van de Installer-software.

## <a name="mandatory-tools-installed"></a>Verplichte Hulpprogram Ma's geïnstalleerd

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 (Windows) of 3,5 (Linux)](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [Tensor flow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Optionele Hulpprogram Ma's die beschikbaar zijn voor installatie

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

- Optionele Caffe-installatie kan mislukken als docker niet op de juiste wijze op het systeem wordt uitgevoerd. Als u Caffe wilt installeren, moet u ervoor zorgen dat docker is geïnstalleerd en wordt uitgevoerd voordat u probeert Caffe te installeren via het installatie programma voor dev tools pack. 

- Optionele CUDA-installatie mislukt op niet-compatibele systemen. Voordat u probeert de [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) te installeren via het installatie programma dev tools pack, moet u de systeem compatibiliteit controleren.

## <a name="minimum-requirements"></a>Minimale vereisten

* Minimale vereisten voor docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bits: Pro, Enter prise of Education (build 16299 of hoger).

             Zie docker Desktop installeren op Windows Home voor Windows 10 Home.
           - De Windows-onderdelen van Hyper-V en containers moeten zijn ingeschakeld.
           - De volgende hardware-vereisten zijn vereist voor het uitvoeren van client Hyper-V in Windows 10:

              - 64-bits processor met het [tweede niveau adres omzetting (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 GB RAM-geheugen
              - Ondersteuning voor hardware-virtualisatie op BIOS-niveau moet zijn ingeschakeld in de BIOS-instellingen. Zie virtualisatie voor meer informatie.

        > [!NOTE]
        > Docker ondersteunt docker desktop in Windows op basis van de ondersteunings levenscyclus van micro soft voor Windows 10-besturings systeem. Zie het [Windows levenscyclus](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)-overzicht voor meer informatie.

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Uw Mac moet voldoen aan de volgende vereisten om docker Desktop te kunnen installeren:
         
         - **Mac-hardware moet een 2010-of nieuwer model zijn met een Intel-processor**, met ondersteuning voor MMU-virtualisatie (Extended Page Tables) en een onbeperkte modus. U kunt controleren of deze ondersteuning voor uw computer is door de volgende opdracht uit te voeren in een Terminal: ```sysctl kern.hv_support```

        Als uw Mac het Hyper Visor-Framework ondersteunt, wordt de opdracht afgedrukt ```kern.hv_support: 1``` .

         - **macOS moet versie 10,14 of nieuwer zijn**. Dat wil zeggen, Mojave, Catalina of Big Sur. U kunt het beste een upgrade uitvoeren naar de nieuwste versie van macOS.

        Als u problemen ondervindt nadat u uw macOS hebt bijgewerkt naar versie 10,15, moet u de nieuwste versie van docker Desktop installeren om compatibel te zijn met deze versie van macOS.

        - Ten minste 4 GB RAM-geheugen.
        - VirtualBox vóór versie 4.3.30 moet niet worden geïnstalleerd omdat het niet compatibel is met docker Desktop.

        > [!NOTE]
        > Docker ondersteunt docker Desktop voor de meest recente versies van macOS. Dat wil zeggen, de huidige release van macOS en de vorige twee versies. Als nieuwe primaire versies van macOS zijn algemeen beschikbaar, docker stopt met de ondersteuning van de oudste versie en ondersteunt de nieuwste versie van macOS (naast de vorige twee releases). Docker Desktop biedt momenteel ondersteuning voor macOS Mojave, macOS Catalina en macOS Big Sur.
        > 
        - Het installatie programma wordt niet ondersteund op Apple M1.

## <a name="instructions"></a>Instructies

1. Down load het installatie programma voor dev tools pack voor [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)en [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. Afhankelijk van uw platform zijn er enkele verschillen bij het starten van het installatie programma.

    1. Voor Windows:
    
        1. Klik op het **installatie programma dev-tools-Pack-Installer** om de installatie wizard te openen.
        
    1. Voor Mac:
    
        1. Na het downloaden verplaatst u het bestand Dev-Tools-Pack-Installer. app naar de map toepassingen.
        
        1. Klik op **dev-tools-Pack-Installer. app** om de installatie wizard te openen.
        
        1. Als u het beveiligings dialoogvenster ' onbekende ontwikkelaar ' krijgt:
        
            1. Ga naar systeem voorkeuren-> Security & privacy-> algemeen en klik op de knop "toch openen" naast "Dev-Tools-Pack-Installer. app"
        
            1. Klik opnieuw op het pictogram van het Elektroon op de Dock
        
            1. Klik op de knop openen in het dialoog venster beveiliging
    
    1. Voor Linux:
    
        1. Klik op Opslaan om het downloaden van het installatie programma te volt ooien, wanneer u hierom wordt gevraagd
        
        1. Uitvoerings machtigingen toevoegen aan de **. appimage** -bestands methode 1 (commandline):
            
            1. De Linux-Terminal openen
            
            1. Typ het volgende in de terminal om naar de map down loads te gaan
            
                1. CD ~/downloads/
                
            1. Typ het volgende in de terminal om het uitvoer bare bestand van AppImage te maken
            
                1. chmod + x **dev-tools-Pack-Installer. AppImage**
                
            1. Typ het volgende in de terminal om het installatie programma uit te voeren
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Uitvoerings machtigingen toevoegen aan de **. appimage** -bestands methode 2 (UI):
        
            1. Klik met de rechter muisknop op het. appimage-bestand en selecteer Eigenschappen
            
            1. Tabblad machtigingen openen
            
            1. Schakel het selectie vakje bestand uitvoeren als programma toestaan in
            
            1. Eigenschappen sluiten en het. appimage-bestand openen

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

   1. Windows:
   
      1. Verborgen pictogrammen in systeemvak uitvouwen:
      
         1. Verborgen pictogrammen in systeemvak uitvouwen indien verborgen:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Systeemvak.":::
         
         1. Controleer of het pictogram van het docker-bureau blad wordt uitgevoerd:

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-status.":::
         
         1. Als het bovenstaande pictogram in het systeemvak niet wordt weer gegeven, start u docker Desktop vanuit het menu Start.
         
         1. Als docker u vraagt om opnieuw op te starten, kunt u het installatie programma sluiten en opnieuw starten nadat het opnieuw opstarten is voltooid en de docker actief is. Alle geslaagde toepassingen van derden moeten worden gedetecteerd en worden niet automatisch opnieuw geïnstalleerd.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [Azure percept Advanced Development-opslag plaats](https://github.com/microsoft/azure-percept-advanced-development) om aan de slag te gaan met geavanceerde ontwikkeling voor Azure percept DK.