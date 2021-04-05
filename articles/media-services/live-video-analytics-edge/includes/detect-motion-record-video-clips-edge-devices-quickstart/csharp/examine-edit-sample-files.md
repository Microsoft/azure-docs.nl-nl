---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486870"
---
Als een van de vereisten voor deze quickstart hebt u de voorbeeldcode naar een map gedownload. Volg deze stappen om de voorbeeldcode te bekijken e te bewerken.

1. Ga naar *src/edge* in Visual Studio Code. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    Het implementatiesjabloon verwijst naar de implementatiemanifest voor het edge-apparaat, waar voor sommige eigenschappen variabelen worden gebruikt. Het *.env*-bestand bevat de waarden voor die variabelen.
1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:
    * ***c2d-console-app.csproj***: het projectbestand voor Visual Studio Code.
    * ***operations.json***: de lijst met bewerkingen die u het programma wilt laten uitvoeren.
    * ***Program.cs***: de voorbeeldprogrammacode. Deze code:

        * De app-instellingen laden.
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om live-videostreams te analyseren door de bijbehorende [directe methoden](../../../direct-methods.md) aan te roepen. 
        * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
        * Roept directe methoden aan om resources op te schonen.

1. Bewerk het bestand *operations.json*:
    * Wijzig de link naar de graaftopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * Bewerk onder `GraphInstanceSet` de naam van de graaftopologie zodat deze overeenkomt met de waarde in de voorgaande link:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Bewerk de RTSP-URL zodat deze naar het videobestand wijst:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Bewerk de naam onder `GraphTopologyDelete`:

        `"name": "EVRToFilesOnMotionDetection"`
