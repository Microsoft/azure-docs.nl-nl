---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358235"
---
1. Blader in Visual Studio code naar src/edge. U ziet het. env-bestand dat u hebt gemaakt, samen met een paar sjabloonbestanden voor de implementatie.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat met enkele tijdelijke waarden. Het. env-bestand bevat de waarden voor die variabelen.
1. Blader vervolgens naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json dat u hebt gemaakt, samen met enkele andere bestanden:

    * c2d-console-app.csproj: het projectbestand voor Visual Studio Code.
    * operations.json: dit bestand bevat de verschillende bewerkingen die u het programma wilt laten uitvoeren.
    * Program.cs: deze voorbeeldcode van het programma:

        * De app-instellingen laden.
        * De Live Video Analytics aanroepen op de directe methodes van de IoT Edge-module om een topologie te maken, de grafiek te instantiëren en de grafiek te activeren.
        * Pauzeren zodat u de uitvoer van de grafiek in het **TERMINAL**-venster en de gebeurtenissen die naar de IoT-hub zijn verzonden in het **OUTPUT**-venster kunt onderzoeken.
        * De grafiekinstantie deactiveren, de grafiekinstantie verwijderen en de grafiektopologie verwijderen.
