---
title: De Defender micro agent bouwen op basis van de bron code (preview-versie)
description: Micro agent bevat een infra structuur, die kan worden gebruikt om uw distributie aan te passen.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: aac9bf224064dd8393acfeb2928f5ed2d1f84381
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781106"
---
# <a name="build-the-defender-micro-agent-from-source-code-preview"></a>De Defender micro agent bouwen op basis van de bron code (preview-versie)

De micro agent bevat een infra structuur, die kan worden gebruikt om uw distributie aan te passen. Als u een lijst met beschik bare configuratie parameters wilt zien, bekijkt u het `configs/LINUX_BASE.conf` bestand.

Wijzig het basis bestand voor één distributie `.conf` . 

Als u meer dan één distributie nodig hebt, kunt u overnemen van de basis configuratie en de waarden ervan negeren. 

De waarden overschrijven:

1. Maak een nieuw `.dist` bestand.

1. Voeg `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` aan de bovenkant toe.
 
1. Definieer nieuwe waarden op wat u nodig hebt, bijvoorbeeld: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Geef het `.dist` bestand een verwijzing bij het maken van. Bijvoorbeeld: 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Basis configuratie hand tekening 

De agent verifieert standaard de authenticiteit van configuratie bestanden die op de schijf worden geplaatst om te voor komen dat onrecht matig wordt geknoeid.

U kunt dit proces beëindigen door de preprocessor-vlag te definiëren `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Het wordt niet aanbevolen om de handtekening controle voor productie omgevingen uit te scha kelen. 

Als u een andere configuratie voor productie scenario's nodig hebt, neemt u contact op met het team van Defender voor IoT. 

## <a name="prerequisites"></a>Vereisten 

1. Neem contact op met uw account manager om te vragen om toegang te krijgen tot Defender voor IoT-bron code.
 
1. Kloon of Extraheer de bron code naar een map op de schijf.

1. Navigeer naar die directory.

1. Haal de submodules op met de volgende code:

    ```bash
    git submodule update --init
    ```
    
1. Vervolgens haalt u de submodules voor de Azure IoT SDK op met de volgende code: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Voeg een uitvoerings machtiging toe en voer het installatie script voor de ontwikkelaars omgeving uit:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Maak een map voor de build-uitvoer: 

    ```bash
    mkdir cmake 
    ```

1. Beschrijving [VSCode](https://code.visualstudio.com/download ) downloaden en installeren 

1. Beschrijving Installeer de [C/C++-extensie](https://code.visualstudio.com/docs/languages/cpp ) voor VSCode.

## <a name="building-the-defender-iot-micro-agent"></a>De Defender IoT micro agent bouwen 

1. Open de map met VSCode 

1. Navigeer naar de `cmake` map. 

1. Voer de volgende opdracht uit: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Bijvoorbeeld: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Volgende stappen

[Configureer uw Azure Defender voor IOT-oplossing](quickstart-configure-your-solution.md).
