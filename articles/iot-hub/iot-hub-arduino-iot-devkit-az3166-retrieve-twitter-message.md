---
title: Een Twitter-bericht met Azure Functions ophalen | Microsoft Docs
description: Gebruik de bewegings sensor voor het detecteren van schudden en het gebruik van Azure Functions om een wille keurige Tweet te vinden met een hashtag die u opgeeft
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147895"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Schud, schud voor een Tweet--een Twitter-bericht ophalen met Azure Functions

In dit project leert u hoe u de bewegings sensor kunt gebruiken om een gebeurtenis te activeren met behulp van Azure Functions. De app haalt een wille keurige Tweet op met een #hashtag u in uw Arduino-schets hebt geconfigureerd. De Tweet wordt weer gegeven op het scherm DevKit.

## <a name="what-you-need"></a>Wat u nodig hebt

De aan de slag- [hand leiding](./iot-hub-arduino-iot-devkit-az3166-get-started.md) volt ooien om:

* Laat uw DevKit verbonden met Wi-Fi.
* Bereid de ontwikkel omgeving voor.

Een actief Azure-abonnement. Als u er nog geen hebt, kunt u zich registreren via een van de volgende methoden:

* Een [gratis Microsoft Azure-abonnement van 30 dagen](https://azure.microsoft.com/free/) activeren
* Claim uw [Azure-tegoed](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u een MSDN-of Visual Studio-abonnee bent

## <a name="open-the-project-folder"></a>Open de projectmap

Begin met het openen van de projectmap. 

### <a name="start-vs-code"></a>Begin VS code

* Zorg ervoor dat uw DevKit is verbonden met uw computer.

* Start VS Code.

* Verbind de DevKit met uw computer.

   > [!NOTE]
   > Wanneer u VS code start, wordt er mogelijk een fout bericht weer gegeven dat het Arduino IDE of het bijbehorende bord pakket niet kan worden gevonden. Als deze fout optreedt, sluit u VS code en start u de Arduino-IDE opnieuw. VS code moet het Arduino IDE-pad nu correct vinden.

### <a name="open-the-arduino-examples-folder"></a>Open de map Arduino-voor beelden

Vouw de sectie **ARDUINO-voor beelden** aan de linkerkant uit, blader naar voor **beelden voor MXCHIP AZ3166 > AzureIoT** en selecteer **ShakeShake**. Er wordt een nieuw versus code venster geopend met daarin de projectmap. Als u de sectie MXCHIP AZ3166 niet kunt zien, controleert u of het apparaat juist is verbonden en Start Visual Studio code opnieuw.  
de ![ Mini-Solution-voor beelden](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

U kunt het voorbeeld project ook openen vanuit het opdracht palet. Klik op `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) om het opdracht palet te openen, typ **Arduino** en zoek en selecteer vervolgens **Arduino: voor beelden**.

## <a name="provision-azure-services"></a>Azure-Services inrichten

Voer in het oplossings venster uw taak uit `Ctrl+P` (macOS: `Cmd+P` ) door in te voeren `task cloud-provision` .

In de VS code-terminal wordt u door een interactieve opdracht regel begeleid bij het inrichten van de vereiste Azure-Services:

![Scherm afbeelding toont de Visual Studio code Terminal Interactive-opdracht regel.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Als de pagina in de laad status vastloopt wanneer u zich probeert aan te melden bij Azure, raadpleegt u de [stap ' aanmeldings pagina loopt vast ' in de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>De #hashtag wijzigen

Open `ShakeShake.ino` en zoek naar deze regel code:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Vervang de teken reeks binnen de accolades door `iot` de gewenste hashtag. De DevKit haalt later een wille keurige Tweet op die de hashtag bevat die u in deze stap opgeeft.

## <a name="deploy-azure-functions"></a>Azure Functions implementeren

Gebruik `Ctrl+P` (macOS: `Cmd+P` ) om uit `task cloud-deploy` te voeren om de Azure functions code te implementeren:

![In de scherm afbeelding ziet u Visual Studio code waar u de taak Cloud-Deploy kunt uitvoeren om Azure Functions code te implementeren.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Af en toe werkt de functie Azure mogelijk niet goed. Als u dit probleem wilt oplossen, schakelt u het [gedeelte ' compilatie fout ' van de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)in.

## <a name="build-and-upload-the-device-code"></a>Bouw en upload de toestel code

Vervolgens bouwt en uploadt u de code van het apparaat.

### <a name="windows"></a>Windows

1. Gebruiken `Ctrl+P` om uit te voeren `task device-upload` .

2. De Terminal vraagt u de configuratie modus in te voeren. Hiervoor doet u het volgende:

   * Knop ingedrukt houden

   * Push de reset-knop en laat deze los.

3. In het scherm worden de DevKit-ID en configuratie weer gegeven.

### <a name="macos"></a>macOS

1. De DevKit in de configuratie modus zetten:

   Houd de knop A ingedrukt en druk vervolgens de knop Reset. In het scherm wordt ' configuratie ' weer gegeven.

2. Gebruik `Cmd+P` om uit `task device-upload` te voeren om de Connection String in te stellen die wordt opgehaald uit de `task cloud-provision` stap.

### <a name="verify-upload-and-run"></a>Controleren, uploaden en uitvoeren

Nu de connection string is ingesteld, wordt deze gecontroleerd en wordt de app geüpload en vervolgens uitgevoerd. 

1. VS code controleert en uploadt de Arduino-schets naar uw DevKit:

   ![Scherm afbeelding toont Visual Studio code voor het controleren en uploaden van de Arduino-schets.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. De DevKit wordt opnieuw opgestart en het uitvoeren van de code wordt gestart.

Het fout bericht ' fout: AZ3166: Unknown package ' wordt weer gegeven. Deze fout treedt op wanneer de pakket index van het board niet correct is vernieuwd. Om dit probleem op te lossen, controleert u de [fout ' Unknown package ' in de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Het project testen

Klik na de initialisatie van de app op knop A en laat deze los en schud vervolgens het DevKit-bord voorzichtig. Met deze actie wordt een wille keurige Tweet opgehaald, die de hashtag bevat die u eerder hebt opgegeven. Binnen een paar seconden wordt een Tweet weer gegeven op het scherm DevKit:

### <a name="arduino-application-initializing"></a>Initialisatie van Arduino-toepassing...

![Arduino-toepassing-initialiseren](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Druk op A om te schudden...

![Druk op A-to-Shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Gereed om te schudden...

![Gereed-to-Shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Verwerken...

![Wordt verwerkt](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Druk op B om te lezen...

![Druk op-B-lezen](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Een wille keurige Tweet weer geven...

![Weer gave-a-wille keurig-Tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Druk nogmaals op de knop en schud vervolgens naar een nieuwe Tweet.
- Druk op de knop B om door de rest van de Tweet te schuiven.

## <a name="how-it-works"></a>Uitleg

![Diagram toont een mobiel apparaat dat een gebeurtenis verzendt naar de Azure I o T-hub, waarmee een Azure function-app wordt geactiveerd om een Tweet aan te vragen. deze wordt teruggestuurd naar de app en doorgestuurd naar de hub en naar het mobiele apparaat.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

De Arduino-schets stuurt een gebeurtenis naar de Azure-IoT Hub. Met deze gebeurtenis wordt de Azure Functions-app geactiveerd. De Azure Functions-app bevat de logica om verbinding te maken met de API van Twitter en een Tweet op te halen. Vervolgens wordt de Tweet-tekst in een C2D-bericht (Cloud-naar-apparaat) gewikkeld en teruggestuurd naar het apparaat.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Optioneel: uw eigen Twitter Bearer-token gebruiken

Voor test doeleinden maakt dit voorbeeld project gebruik van een vooraf geconfigureerd Twitter Bearer-token. Er is echter een [frequentie limiet](https://dev.twitter.com/rest/reference/get/search/tweets) voor elke Twitter-account. Als u uw eigen token wilt gebruiken, voert u de volgende stappen uit:

1. Ga naar de [Twitter-ontwikkelaars Portal](https://dev.twitter.com/) om een nieuwe Twitter-app te registreren.

2. [Consumenten-en consumenten geheimen](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) van uw app ophalen.

3. Gebruik een [hulp programma](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) om een Twitter Bearer-token te genereren op basis van deze twee sleutels.

4. Ga in de [Azure Portal](https://portal.azure.com/){: target = "_blank"} naar de **resource groep** en zoek de Azure-functie (type: app service) voor het project "Shake, Shake". De naam bevat altijd ' Shake... ' tekenreeksexpressie.

   ![Scherm afbeelding van de Azure Portal toont een app service voor het project.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Werk de code voor `run.csx` in **functions > shakeshake-CS** met uw eigen token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Scherm afbeelding toont de C#-code voor de functie waarin u uw token kunt invoeren.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Sla het bestand op en klik op **uitvoeren**.

## <a name="problems-and-feedback"></a>Problemen en feedback

Problemen oplossen of feedback geven. 

### <a name="problems"></a>Problemen

Een probleem dat u kunt zien als het scherm ' No tweets ' weergeeft terwijl elke stap is uitgevoerd. Deze voor waarde gebeurt normaal gesp roken de eerste keer dat u het voor beeld implementeert en uitvoert, omdat de functie-app van een paar seconden tot één minuut vereist is om de app koud te starten. 

Of wanneer de code wordt uitgevoerd, zijn er een aantal problemen die ervoor zorgen dat de app opnieuw wordt gestart. Als dit probleem zich voordoet, kan de apparaat-app een time-out krijgen voor het ophalen van de Tweet. In dit geval kunt u een of beide van de volgende methoden proberen om het probleem op te lossen:

1. Klik op de knop opnieuw instellen op de DevKit om de app van het apparaat opnieuw uit te voeren.

2. Zoek in de [Azure Portal](https://portal.azure.com/)de Azure functions-app die u hebt gemaakt en start deze opnieuw op:

   ![Scherm afbeelding toont de Azure Portal met een Azure Functions-app en de knop opnieuw opstarten.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Als u andere problemen ondervindt, raadpleegt u de [Veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neemt u contact met ons op met behulp van de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat verbindt met uw oplossings versneller voor externe controle van Azure IoT en een Tweet ophaalt, zijn dit de voorgestelde volgende stappen:

* [Overzicht van de oplossing voor externe controle van Azure IoT](/azure/iot-suite/)