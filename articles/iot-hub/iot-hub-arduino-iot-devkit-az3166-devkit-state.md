---
title: Azure Device apparaatdubbels gebruiken om MXChip IoT DevKit-LED voor gebruikers te beheren | Microsoft Docs
description: In deze zelf studie leert u hoe u DevKit Staten kunt bewaken en hoe u de gebruiker bestuurt met Azure IoT Hub apparaat apparaatdubbels.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148785"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

U kunt dit voor beeld gebruiken om de MXChip IoT DevKit WiFi-gegevens en sensor statussen te bewaken en de kleur van de gebruiker te bepalen met behulp van Azure IoT Hub Device apparaatdubbels.

## <a name="what-you-learn"></a>Wat u leert

- De MXChip IoT DevKit-sensor statussen bewaken.

- Azure Device apparaatdubbels gebruiken om de kleur van de RGB-LED van DevKit te bepalen.

## <a name="what-you-need"></a>Wat u nodig hebt

- Stel uw ontwikkel omgeving in door de aan de slag- [hand leiding](./iot-hub-arduino-iot-devkit-az3166-get-started.md)te volgen.

- Typ in het GitBash-Terminal venster (of een andere Git-opdracht regel Interface) de volgende opdrachten:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure-Services inrichten

1. Klik in Visual Studio code op de vervolg keuzelijst **taken** en selecteer **taak uitvoeren...**  -  **Cloud-inrichting**.

2. De voortgang wordt weer gegeven op het tabblad **Terminal** van het **welkomst** venster.

3. Wanneer u wordt gevraagd met het bericht *welk abonnement u wilt kiezen*, selecteert u een abonnement.

4. Selecteer of kies een resource groep. 
 
   > [!NOTE]
   > Als u al een gratis IoT Hub hebt, kunt u deze stap overs Laan.

5. Wanneer u wordt gevraagd met het bericht *welke IOT-hub u wilt kiezen*, selecteert of maakt u een IOT hub.

6. Iets vergelijkbaar met *functie-app: functie app name: xxx*, wordt weer gegeven. Noteer de naam van de functie-app. deze wordt gebruikt in een latere stap.

7. Wacht totdat de implementatie van de Azure Resource Manager-sjabloon is voltooid, wat wordt aangegeven wanneer de implementatie van de sjabloon voor bericht *bron beheer: gereed* wordt weer gegeven.

## <a name="deploy-function-app"></a>functie-app implementeren

1. Klik in Visual Studio code op de vervolg keuzelijst **taken** en selecteer **taak uitvoeren...**  -  **Cloud-implementeren**.

2. Wacht tot het upload proces van de functie-app code is voltooid; de functie voor het implementeren van de tekst van de *app: voltooid* wordt weer gegeven.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>De verbindings reeks voor een IoT Hub apparaat configureren in DevKit

1. Verbind uw MXChip IoT DevKit met uw computer.

2. Klik in Visual Studio code op de vervolg keuzelijst **taken** en selecteer **taak uitvoeren...**  -  **configuratie-apparaat-verbinding**

3. Op de MXChip IoT DevKit, houdt u de knop **a** ingedrukt, drukt u op de knop **Reset** en geeft u vervolgens knop **a** op om de configuratie modus DekKit in te stellen.

4. Wacht totdat het connection string configuratie proces is voltooid.

## <a name="upload-arduino-code-to-devkit"></a>Arduino-code uploaden naar DevKit

Met uw MXChip IoT DevKit verbonden met uw computer:

1. Klik op de vervolg keuzelijst **taken** in Visual Studio code en selecteer **Build-taak uitvoeren...** De Arduino-schets wordt gecompileerd en geüpload naar de DevKit.

2. Wanneer de schets is geüpload, wordt een *Build & upload schets: het succes* bericht wordt weer gegeven.

## <a name="monitor-devkit-state-in-browser"></a>Status van DevKit in browser controleren

1. Open in een webbrowser het `DevKitState\web\index.html` bestand--dat is gemaakt tijdens de stap wat u nodig hebt.

2. De volgende webpagina wordt weer gegeven:![Geef de naam van de functie-app op.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Voer de naam van de functie-app in die u eerder hebt geschreven.

4. Klik op de knop **verbinding maken**

5. Binnen een paar seconden wordt de pagina vernieuwd en worden de Wi-Fi-verbindings status van de DevKit en de status van elk van de trein sensors weer gegeven.

## <a name="control-the-devkits-user-led"></a>De LED van de DevKit van de gebruiker bepalen

1. Klik op de afbeelding van de gebruiker LED voor de webpagina.

2. Binnen een paar seconden wordt het scherm vernieuwd en wordt de huidige kleur status van de LED van de gebruiker weer gegeven.

3. Wijzig de kleur waarde van de RGB-LED door te klikken op verschillende locaties op de RGB-schuif regelaars.

## <a name="example-operation"></a>Voorbeeld bewerking

![Voorbeeld test procedure](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> U kunt onbewerkte gegevens van het apparaat dubbele in azure Portal bekijken: IOT hub- \> IOT-apparaten- \> *\<your device\>*  - \> apparaat-twee.

## <a name="next-steps"></a>Volgende stappen

U hebt het volgende geleerd:
- Verbind een MXChip IoT DevKit-apparaat met uw Azure IoT-oplossing voor externe controle.
- Gebruik de Azure IoT-functie voor apparaatdubbels om de kleur van de RGB-LED van DevKit te bedenken en te beheren.

Hier volgt de voorgestelde volgende stap: [overzicht van de Azure IOT-oplossing voor externe controle](/azure/iot-suite/)