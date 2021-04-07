---
title: 'Zelfstudie: Gesimuleerde apparaatgegevens genereren: Machine Learning op Azure IoT Edge'
description: 'Zelfstudie: virtuele apparaten maken die gesimuleerde telemetrie genereren die later kunnen worden gebruikt om een machine learning-model te trainen.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463126"
---
# <a name="tutorial-generate-simulated-device-data"></a>Zelfstudie: Gesimuleerde apparaatgegevens genereren

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In dit artikel gebruiken we machine learning-trainingsgegevens om een apparaat te simuleren dat telemetrie naar Azure IoT Hub verzendt. Zoals gezegd in de inleiding, maakt deze zelfstudie gebruik van de [Turbofan Engine Degradation Simulation Data Set](https://c3.nasa.gov/dashlink/resources/139/) voor het simuleren van gegevens uit een set vliegtuigmotoren voor training en tests.

In het experimentele scenario weten we dat:

* De gegevens bestaan uit meerdere multidimensionale tijdreeksen.
* Elke gegevensset wordt onderverdeeld in trainings- en testsubsets.
* Elke tijdreeks bevat gegevens van een andere motor.
* Elke motor begint met een andere mate van eerste slijtage en productievariaties.

Voor deze zelfstudie gebruiken we de subset met trainingsgegevens van één gegevensset (FD003).

In werkelijkheid zou elke motor een onafhankelijk IoT-apparaat zijn. Ervan uitgaande dat u niet over een verzameling met internet verbonden turbofan-motoren beschikt, zullen we een softwarevervanger voor deze apparaten bouwen.

De simulator is een C#-programma dat gebruikmaakt van de IoT Hub-API's voor het programmatisch registreren van virtuele apparaten met IoT Hub. Vervolgens lezen we de gegevens voor elk apparaat uit de subset met NASA-gegevens en verzenden deze naar uw IoT-hub met behulp van een gesimuleerd IoT-apparaat. Alle code voor dit deel van de zelfstudie vindt u in de map DeviceHarness van de opslagplaats.

Het DeviceHarness-project is een .NET core-project dat is geschreven in C# en bestaat uit vier klassen:

* **Program:** Het ingangspunt voor uitvoering dat verantwoordelijk is voor het verwerken van gebruikersinvoer en algemene coördinatie.
* **TrainingFileManager:** Verantwoordelijk voor het lezen en parseren van het geselecteerde gegevensbestand.
* **CycleData:** Vertegenwoordigt één rij met gegevens in een bestand dat is geconverteerd naar een berichtindeling.
* **TurbofanDevice:** Verantwoordelijk voor het maken van een IoT-apparaat dat overeenkomt met één apparaat (tijdreeks) in de gegevens en voor het verzenden van de gegevens naar IoT Hub.

Het uitvoeren van de taken die in dit artikel worden beschreven, zou ongeveer 20 minuten moeten duren.

In werkelijkheid zou het equivalent van het werk in deze stap waarschijnlijk worden gedaan door apparaatontwikkelings en cloudontwikkelaars.

In dit deel van de zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een extern project opnemen in uw ontwikkelomgeving.
> * Het voorbeeldproject DeviceHarness gebruiken om gesimuleerde IoT-apparaatgegevens te genereren.
> * Gegenereerde gegevens in uw IoT Hub bekijken.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code configureren en DeviceHarness-project bouwen

1. Open een extern-bureaubladsessie naar je ontwikkelings-VM.

1. Open de map `C:\source\IoTEdgeAndMlSample\DeviceHarness` in Visual Studio Code.

1. Omdat u voor de eerste keer extensies op deze computer gebruikt, worden de afhankelijkheden van sommige extensies bijgewerkt en geïnstalleerd. U wordt mogelijk gevraagd om de extensie bij te werken. Als dit het geval is, selecteert u **Reload Window** (Venster opnieuw laden).

   Als er OmniSharp-fouten in het uitvoervenster worden weergegeven, moet u de C#-extensie verwijderen.

1. U wordt gevraagd om de vereiste assets voor DeviceHarness toe te voegen. Selecteer **Ja** om ze toe te voegen.

   * Het kan een paar seconden duren voordat de melding wordt weergegeven.
   * Klik op het klokpictogram in de rechterbenedenhoek als u deze melding hebt gemist.

   ![VS Code-extensiepop-up](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecteer **Restore** (Herstellen) om de pakketafhankelijkheden te herstellen.

   ![VS Code-herstelprompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Als u deze meldingen niet krijgt, sluit u Visual Studio Code, verwijdert u de mappen bin en obj in `C:\source\IoTEdgeAndMlSample\DeviceHarness`, opent u Visual Studio Code en opent u de map DeviceHarness opnieuw.

1. Controleer of uw omgeving juist is ingesteld door een build te activeren met **Ctrl** + **Shift** + **B** of **Terminal** > **Run Build Task** (Buildtaak uitvoeren).

1. U wordt gevraagd de buildtaak te selecteren die u wilt uitvoeren. Selecteer **Build**.

1. De build wordt uitgevoerd en voert een bericht uit.

   ![Uitvoerbericht van geslaagde build](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. U kunt deze build de standaard-buildtaak maken door **Terminal** > **Configure Default Build Task...** (Standaardbuildtaak configureren) te selecteren en bij de prompt **Build** te kiezen.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Verbinding maken met IoT Hub en DeviceHarness uitvoeren

Nu het project is gemaakt, maakt u verbinding met uw IoT-hub om toegang te krijgen tot de verbindingsreeks en de voortgang van het genereren van de gegevens te controleren.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Aanmelden bij Azure in Visual Studio Code

1. Meld u aan bij uw Azure-abonnement in Visual Studio Code door het opdrachtenpalet te openen met `Ctrl + Shift + P` of **View** (Beeld) > **Command Palette** (Opdrachtenpalet).

1. Zoek de opdracht **Azure: Aanmelden**.

   Er wordt een browservenster geopend en u wordt gevraagd om uw referenties. Wanneer u bent omgeleid naar een succespagina, kunt u de browser sluiten.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Verbinding maken met uw IoT hub en de verbindingsreeks voor de hub ophalen

1. Selecteer in het onderste gedeelte van de Visual Studio Code-verkenner het **Azure IoT Hub**-frame om het uit te vouwen.

1. Klik in het uitgevouwen frame op **Select IoT Hub** (IoT Hub selecteren).

1. Selecteer uw Azure-abonnement en vervolgens uw IoT-hub wanneer hierom wordt gevraagd.

1. Klik op **...** rechts van **Azure IoT Hub** voor meer acties. Selecteer **Copy IoT Hub connection string** (IoT Hub-verbindingsreeks kopiëren).

   ![IoT Hub-verbindingsreeks kopiëren](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Het DeviceHarness-project uitvoeren

1. Selecteer **View** (Beeld) > **Terminal** om de Visual Studio Code-terminal te openen.

   Druk op Enter als er geen prompt wordt weergegeven.

1. Voer `dotnet run` in de terminal in.

1. Wanneer u om de IoT Hub Connection String wordt gevraagd, plakt u de verbindingsreeks die u in de vorige sectie hebt gekopieerd.

1. Klik in het frame **Azure IoT Hub devices** (Azure IoT Hub-apparaten) op de knop Refresh (Vernieuwen).

   ![Lijst met IoT Hub-apparaten vernieuwen](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. U ziet dat er apparaten worden toegevoegd aan de IoT Hub en dat de apparaten groen worden weergegeven om aan te geven dat er gegevens via dat apparaat worden verzonden. Nadat de apparaten berichten naar de IoT-hub hebben verzonden, wordt de verbinding verbroken en worden ze blauw weergegeven.

1. U kunt naar de hub verzonden berichten bekijken door met de rechtermuisknop op een apparaat te klikken en **Start Monitoring Built-in Event Endpoint** (Bewaking ingebouwd gebeurteniseindpunt starten) te selecteren. De berichten worden weergegeven in het uitvoervenster in Visual Studio Code.

1. Stop de bewaking door in het uitvoervenster van **Azure IoT Hub** te klikken en **Stop Monitoring Built-in Event Endpoint** (Bewaking ingebouwd gebeurteniseindpunt stoppen) te selecteren.

1. Laat de toepassing uitvoeren tot deze klaar is, wat enkele minuten duurt.

## <a name="check-iot-hub-for-activity"></a>IoT Hub controleren op activiteit

De gegevens die door DeviceHarness worden verzonden, zijn naar uw IoT-hub gegaan, waar u ze kunt controleren in Azure Portal.

1. Open [Azure Portal](https://portal.azure.com/) en navigeer naar de IoT-hub die voor deze zelfstudie is gemaakt.

1. Selecteer in het linkerdeelvenster **Metrics** (Metrische gegevens) onder **Monitoring** (Bewaking).

1. Klik in de grafiekdefinitiepagina op de vervolgkeuzelijst **Metric**, schuif omlaag in de lijst en selecteer **Routing: data delivered to storage** (Routering: gegevens bezorgd in opslag). In de grafiek wordt een piek weergegeven op het moment dat de gegevens zijn doorgestuurd naar de opslag.

   ![Grafiek toont een piek wanneer gegevens worden bezorgd in de opslag](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Gegevens valideren in Azure Storage

De gegevens die we zojuist naar uw IoT-hub hebben verzonden, zijn doorgestuurd naar de opslagcontainer die we in het vorige artikel hebben gemaakt. Laten we eens kijken naar de gegevens in het opslagaccount.

1. Ga in Azure Portal naar uw opslagaccount.

1. Selecteer in de opslagaccountnavigator **Storage Explorer (preview)** (Opslagverkenner (preview)).

1. Selecteer in de opslagverkenner **Blob Containers** (Blobcontainers) en vervolgens `devicedata`.

1. Klik in het inhouddeelvenster op de map voor de naam van de IoT-hub, gevolgd door het jaar, de maand, de dag en het uur. U ziet een aantal mappen waarin de minuten worden weergegeven wanneer de gegevens zijn geschreven.

   ![Mappen in blobopslag bekijken](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klik in een van deze mappen om gegevensbestanden met het label **00** en **01** te vinden die overeenkomen met de partitie.

1. De bestanden zijn geschreven in [Avro](https://avro.apache.org/)-indeling. Dubbelklik op een van deze bestanden om een ander browsertabblad te openen en de gegevens gedeeltelijk weer te geven. Als u wordt gevraagd om het bestand in een programma te openen, kunt u VS Code kiezen en wordt het bestand correct weergegeven.

1. U hoeft de gegevens nu niet te lezen of te interpreteren; dit gaan we in het volgende artikel doen.

## <a name="clean-up-resources"></a>Resources opschonen

Deze zelfstudie maakt deel uit van een reeks, waarvan elk artikel is gebaseerd op het werk dat in de voorgaande artikelen is uitgevoerd. Wacht met het opschonen van resources totdat u de laatste zelfstudie hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een .NET Core-project gebruikt om een set virtuele IoT-apparaten te maken en via deze apparaten gegevens naar onze IoT-hub en in een Azure Storage-container te verzenden. In dit project wordt een praktijkscenario gesimuleerd waarbij fysieke IoT-apparaten gegevens verzenden naar een IoT-hub en vervolgens naar een gecureerde opslag. Deze gegevens bestaan uit sensorgegevens, operationele instellingen, foutsignalen en modi, enzovoort. Wanneer er voldoende gegevens zijn verzameld, gebruiken we deze om modellen te trainen die de resterende levensduur van het apparaat voorspellen. In het volgende artikel wordt deze machine learning gedemonstreerd.

Ga door naar het volgende artikel om een machine learning-model met de gegevens te trainen.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md)
