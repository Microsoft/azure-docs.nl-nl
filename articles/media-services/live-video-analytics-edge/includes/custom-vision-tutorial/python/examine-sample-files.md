---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358244"
---
1. Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

   * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren.
   * main.py - Dit is de voorbeeldcode van het programma. Deze doet het volgende:
    
        * De app-instellingen laden.
        * De Live Video Analytics aanroepen op de directe methodes van de IoT Edge-module om een topologie te maken, de grafiek te instantiëren en de grafiek te activeren.
        * Pauzeren zodat u de uitvoer van de grafiek in het **TERMINAL**-venster en de gebeurtenissen die naar de IoT-hub zijn verzonden in het **OUTPUT**-venster kunt onderzoeken.
        * De grafiekinstantie deactiveren, de grafiekinstantie verwijderen en de grafiektopologie verwijderen.
