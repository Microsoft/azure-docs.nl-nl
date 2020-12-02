---
title: 'Zelfstudie: Voorbeeld van IoT Visual Alerts'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gebruikt u Custom Vision met een IoT-apparaat om visuele toestanden van de videofeed van een camera te herkennen en te rapporteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020174"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Zelfstudie: Gebruik Custom Vision met een IoT-apparaat om visuele toestanden te melden

In deze voorbeeld-app ziet u hoe u Custom Vision kunt gebruiken om een apparaat met een camera te trainen om visuele toestanden te detecteren. U kunt dit detectiescenario uitvoeren op een IoT-apparaat met behulp van een geëxporteerd ONNX-model.

Een visuele toestand beschrijft de inhoud van een afbeelding: een lege kamer of een kamer met mensen, een lege oprit of een oprit met een vrachtwagen, enzovoort. In de onderstaande afbeelding ziet u dat de app detecteert wanneer een banaan of een appel vóór de camera wordt geplaatst.

![Animatie van een gebruikersinterface waarop aan de voorzijde namen van fruit worden gelabeld](./media/iot-visual-alerts-tutorial/scoring.gif)

In deze zelfstudie leert u hoe u het volgende kunt doen:
> [!div class="checklist"]
> * Configureer de voorbeeld-app om uw eigen Custom Vision- en IoT Hub-resources te kunnen gebruiken.
> * Gebruik de app voor het trainen van uw Custom Vision-project.
> * Gebruik de app om nieuwe afbeeldingen in realtime te scoren en de resultaten naar Azure te verzenden.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Dit project moet een **compact** afbeeldingsclassificatieproject zijn, omdat het model later naar ONNX wordt geëxporteerd.
* U moet ook [een IoT Hub-resource maken](https://ms.portal.azure.com/#create/Microsoft.IotHub) in Azure.
* [Visual Studio 2015 of nieuwer](https://www.visualstudio.com/downloads/)
* Optioneel: een IoT-apparaat met Windows 10 IoT Core, versie 17763 of hoger. U kunt de app ook rechtstreeks op de pc uitvoeren.
   * Voor Raspberry Pi 2 en 3 kunt u Windows 10 rechtstreeks vanuit de IoT Dashboard-app instellen. Voor andere apparaten, zoals DrangonBoard, moet u deze flashen met behulp van de [eMMC-methode](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Als u hulp nodig hebt bij het instellen van een nieuw apparaat, raadpleegt u [Uw apparaat instellen](/windows/iot-core/tutorials/quickstarter/devicesetup) in de Windows IoT-documentatie.

## <a name="about-the-visual-alerts-app"></a>Over de Visual Alerts-app

De app IoT Visual Alerts wordt uitgevoerd in een doorlopende lus, waarbij zo nodig tussen vier verschillende toestanden wordt geschakeld:

* **Geen model**: Een niet functionerende toestand. De app is telkens één seconde in de slaapstand waarna de camera wordt gecontroleerd.
* **Trainingsafbeeldingen vastleggen**: In deze toestand legt de app een afbeelding vast en uploadt deze als een trainingsafbeelding naar het Custom Vision-doelproject. De app gaat vervolgens 500 ms in de slaapstand. De bewerking wordt herhaald totdat het ingestelde aantal afbeelding is vastgelegd. Vervolgens wordt de training van het Custom Vision-model geactiveerd.
* **Wachten op getraind model**: In deze toestand roept de app elke seconde de Custom Vision-API aan om te controleren of het doelproject een getrainde iteratie bevat. Wanneer er een wordt gevonden, wordt het bijbehorende ONNX-model gedownload naar een lokaal bestand en wordt overgeschakeld naar de toestand **Scoren** toestand.
* **Scoren**: In deze toestand gebruikt de app Windows ML om één frame van de camera te evalueren op basis van het lokale ONNX-model. De resulterende afbeeldingsclassificatie wordt weergegeven op het scherm en als een bericht naar IoT Hub verzonden. De app gaat vervolgens één seconde in de slaapstand voordat een nieuwe afbeelding wordt gescoord.

## <a name="examine-the-code-structure"></a>De codestructuur controleren

Met de volgende bestanden wordt de belangrijkste functionaliteit van de app afgehandeld.

| File | Beschrijving |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Dit bestand definieert de XAML-gebruikersinterface. Het bestand host het besturingselement van de webcamera en bevat de labels die voor toestand-updates worden gebruikt.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Deze code regelt het gedrag van de XAML-gebruikersinterface. Het bevat de verwerkingscode van de toestandcomputer.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Deze klasse is een wrapper waarmee de integratie met de Custom Vision-service wordt afgehandeld.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Deze klasse is een wrapper om de integratie met Windows ML voor het laden van het ONNX-model en het scoren van afbeeldingen in vergelijking tot dit model af te handelen.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Deze klasse is een wrapper waarmee de integratie met IoT Hub voor het uploaden van scoringsresultaten naar Azure wordt afgehandeld.|

## <a name="set-up-the-visual-alerts-app"></a>Visual Alerts-app instellen

Volg deze stappen om de app IoT Visual Alerts op uw pc of IoT-apparaat te laten uitvoeren.

1. Kloon of download het [IoTVisualAlerts-voorbeeld](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) op GitHub.
1. Open de oplossing _IoTVisualAlerts.sln_ in Visual Studio
1. Het Custom Vision-project integreren:
    1. Werk in het script _CustomVision\CustomVisionServiceWrapper.cs_ de variabele `ApiKey` bij met uw trainingssleutel.
    1. Werk vervolgens de variabele `Endpoint` bij met de eindpunt-URL die aan de sleutel is gekoppeld.
    1. Werk de variabele `targetCVSProjectGuid` bij met de overeenkomende id van het Custom Vision-project dat u wilt gebruiken. 
1. De IoT Hub-resource instellen:
    1. Werk in het script _IoTHub\IotHubWrapper.cs_ de variabele `s_connectionString` bij met de juiste verbindingsreeks voor uw apparaat. 
    1. Laad in Azure Portal het IoT Hub-exemplaar, klik op **IoT-apparaten** onder **Explorers**, selecteer uw doelapparaat (of maak er zo nodig een) en zoek de verbindingsreeks onder **Primaire verbindingsreeks**. De reeks bevat de naam van uw IoT Hub, de apparaat-id en de sleutel voor gedeelde toegang. De reeks heeft de volgende indeling: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>De app uitvoeren

Als u de app op de pc uitvoert, selecteert u **Lokale computer** voor het doelapparaat in Visual Studio en selecteert u **x64** of **x86** voor het doelplatform. Druk vervolgens op F5 om het programma uit te voeren. De app wordt gestart en geeft de livefeed van de camera en een statusbericht weer.

Als u implementeert op een IoT-apparaat met een ARM-processor, moet u **ARM** selecteren als het doelplatform en **Externe computer** als het doelapparaat. Geef het IP-adres van uw apparaat op wanneer u hierom wordt gevraagd (het moet zich op hetzelfde netwerk bevinden als uw pc). U kunt het IP-adres van de Windows IoT-standaard-app verkrijgen nadat het apparaat is opgestart en met het netwerk is verbonden. Druk op F5 om het programma uit te voeren.

Wanneer u de app voor de eerste keer uitvoert, bevat deze geen kennis van de visuele toestanden. Het statusbericht wordt weergegeven dat er geen model beschikbaar is. 

## <a name="capture-training-images"></a>Trainingsafbeeldingen vastleggen

Als u een model wilt instellen, moet u de app in de toestand **Trainingsafbeeldingen vastleggen** brengen. Voer een van de volgende stappen uit:
* Als u de app op de pc uitvoert, gebruikt u de knop in de rechterbovenhoek van de gebruikersinterface.
* Als u de app uitvoert op een IoT-apparaat, roept u methode `EnterLearningMode` op het apparaat aan via IoT Hub. U kunt de methode aanroepen via de apparaatvermelding in het IoT Hub-menu in Azure Portal of met een hulpprogramma als [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp).
 
Wanneer de app de toestand **Trainingsafbeeldingen vastleggen** heeft, worden er elke seconde twee afbeeldingen vastgelegd totdat het beoogde aantal afbeeldingen is bereikt. Het beoogde aantal is standaard dertig, maar u kunt deze parameter instellen door het gewenste aantal als argument door te geven aan IoT Hub-methode `EnterLearningMode`. 

Terwijl de app afbeeldingen vastlegt, moet u de camera diverse typen visuele toestanden laten zien die u wilt detecteren (bijvoorbeeld een lege kamer, een kamer met mensen, een leeg bureau, een bureau met een speelgoedvrachtwagen, enzovoort).

## <a name="train-the-custom-vision-model"></a>Het Custom Vision-model trainen

Zodra de app klaar is met het vastleggen van afbeeldingen, worden deze geüpload en wordt vervolgens overgeschakeld naar de toestand **Wachten op getraind model**. Op dit punt gaat u naar de [Custom Vision-website](https://www.customvision.ai/) en bouwt u een model op basis van de nieuwe trainingsafbeeldingen. In de volgende animatie ziet u een voorbeeld van dit proces.

![Animatie: meerdere afbeeldingen van bananen taggen](./media/iot-visual-alerts-tutorial/labeling.gif)

Als u dit proces wilt herhalen met uw eigen scenario, gaat u als volgt te werk:

1. Meld u aan bij de [Custom Vision-website](http://customvision.ai).
1. Zoek uw doelproject. Dit moet nu alle trainingsafbeeldingen bevatten die door de app zijn geüpload.
1. Selecteer voor elke visuele toestand die u wilt identificeren de juiste afbeeldingen en voeg de tag handmatig toe.
    * Als u bijvoorbeeld onderscheid wilt maken tussen een lege kamer en een kamer met personen, wordt u aangeraden vijf of meer afbeeldingen met personen als een nieuwe klasse, **Personen**, te taggen. Geef vijf of meer afbeeldingen zonder personen de tag **Negatief**. Hierdoor leert het model beter onderscheid maken tussen de twee toestanden.
    * Een ander voorbeeld: als u bij benadering wilt aangeven hoe vol een plank is, kunt u tags als **LegePlank**, **GedeeltelijkVollePlank** en **VollePlank** gebruiken.
1. Wanneer u klaar bent, selecteert u de knop **Trainen**.
1. Zodra de training is voltooid, detecteert de app dat er een getrainde iteratie beschikbaar is. Het proces voor het exporteren van het getrainde model naar ONNX wordt gestart en naar het apparaat gedownload.

## <a name="use-the-trained-model"></a>Het getrainde model gebruiken

Zodra de app het getrainde model downloadt, wordt er overgeschakeld naar de toestand **Scoren** en worden afbeeldingen van de camera in een doorlopende lus gescoord.

Voor elke vastgelegde afbeelding wordt de bovenste tag op het scherm weergegeven. Als de visuele toestand niet wordt herkend, wordt **Geen overeenkomsten** weergegeven. De app verzendt deze berichten ook naar IoT Hub. Als er een klasse wordt gedetecteerd, bevat het bericht het label, de betrouwbaarheidsscore en een eigenschap met de naam `detectedClassAlert`. Deze kan worden gebruikt door IoT Hub-clients die zijn geïnteresseerd in een snelle berichtroutering op basis van eigenschappen.

Daarnaast maakt het voorbeeld gebruik van een [Sense HAT-bibliotheek](https://github.com/emmellsoft/RPi.SenseHat) om te detecteren wanneer het wordt uitgevoerd op een Raspberry Pi met een Sense HAT-eenheid. In dat geval kan de eenheid worden gebruikt als een uitvoerweergave door alle weergavelampjes op rood in te stellen wanneer er een klasse wordt gedetecteerd. Wanneer er niets wordt gedetecteerd, branden ze niet.

## <a name="reuse-the-app"></a>De app opnieuw gebruiken

Als u de oorspronkelijke toestand van de app opnieuw wilt instellen, kunt u dit doen door op de knop in de rechterbovenhoek van de gebruikersinterface te klikken of door methode `DeleteCurrentModel` via IoT Hub aan te roepen.

U kunt op elk gewenst moment de stap voor het uploaden van trainingsafbeeldingen herhalen door op de knop rechtsboven in de gebruikersinterface te klikken of door methode `EnterLearningMode` opnieuw aan te roepen.

Als u de app op een apparaat uitvoert en het IP-adres opnieuw wilt ophalen (bijvoorbeeld om een externe verbinding tot stand te brengen via de [Windows IoT Remote-client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)), kunt u methode `GetIpAddress` aanroepen via IoT Hub.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het Custom Vision-project als u het niet meer wilt bewaren. Ga op de [Custom Vision-website](https://customvision.ai) naar **Projecten** en selecteer de prullenbak onder het nieuwe project.

![Schermafbeelding van een deelvenster met de naam Mijn nieuwe project met een prullenbakpictogram](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie kunt u een toepassing instellen en uitvoeren die informatie over de visuele toestand op een IoT-apparaat detecteert en de resultaten naar IoT Hub verzendt. Bekijk vervolgens de broncode of breng een van de hieronder voorgestelde wijzigingen aan.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-voorbeeld (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Voeg een IoT Hub-methode toe om de app rechtstreeks naar de toestand **Wachten op getraind model** te schakelen. Op deze manier kunt u het model trainen met afbeeldingen die niet worden vastgelegd door het apparaat zelf en kunt u vervolgens het nieuwe model na een opdracht naar het apparaat pushen.
* Volg de zelf studie [Sensorgegevens in realtime visualiseren](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) om een Power BI-dashboard te maken voor het visualiseren van de IoT Hub-waarschuwingen die door het voorbeeld worden verzonden.
* Volg de zelfstudie [Externe IoT-bewaking](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) om een logische app te maken die reageert op IoT Hub-waarschuwingen wanneer een visuele toestand wordt gedetecteerd.