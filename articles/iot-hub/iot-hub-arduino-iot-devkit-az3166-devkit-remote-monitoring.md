---
title: MXChip IoT DevKit verbinden met Azure IoT Hub externe controle
description: In deze zelf studie leert u hoe u de status van Sens oren op IoT DevKit AZ3166 kunt verzenden naar de oplossings versneller voor externe controle van Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785013"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>MXChip IoT DevKit verbinden met de oplossings versneller voor externe controle van Azure IoT

In deze zelf studie leert u hoe u een voor beeld-app kunt uitvoeren op uw DevKit voor het verzenden van sensor gegevens naar uw Azure IoT-oplossing voor externe controle.

[MXChip IOT DevKit](https://aka.ms/iot-devkit) is een alles-in-een Arduino-compatibel bord met rijke rand apparatuur en Sens oren. U kunt dit ontwikkelen met behulp van [Visual Studio code Extension voor Arduino](https://aka.ms/arduino). En het wordt geleverd met een [catalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) met groeiende projecten voor het begeleiden van oplossingen voor prototype Internet of Things (IOT) die gebruikmaken van Microsoft Azure Services.

## <a name="what-you-need"></a>Wat u nodig hebt

De aan de slag- [hand leiding](./iot-hub-arduino-iot-devkit-az3166-get-started.md) volt ooien om:

* Uw DevKit verbonden met Wi-Fi
* De ontwikkelomgeving voorbereiden

Een actief Azure-abonnement. Als u er geen hebt, kunt u zich registreren via een van de volgende twee methoden:

* Een [gratis Microsoft Azure-abonnement van 30 dagen](https://azure.microsoft.com/free/) activeren

* Claim uw [Azure-tegoed](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u een MSDN-of Visual Studio-abonnee bent

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Een Azure IoT-oplossing voor externe controle maken

1. Ga naar de [site met accelerators voor Azure IOT-oplossingen](https://www.azureiotsolutions.com/) en klik op **een nieuwe oplossing maken**.

   ![Type Azure IoT-oplossings Accelerator selecteren](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Standaard maakt dit voor beeld een S2 IoT Hub nadat er één IoT-oplossing voor externe controle is gemaakt. Als deze IoT hub niet met een groot aantal apparaten wordt gebruikt, raden wij u ten zeerste aan de downgrade uit te voeren van S2 naar S1 en de oplossing voor externe controle van IoT te verwijderen, zodat de gerelateerde IoT Hub ook kunnen worden verwijderd wanneer u deze niet meer nodig hebt. 

2. Selecteer **externe controle**.

3. Voer een oplossings naam in, selecteer een abonnement en een regio en klik vervolgens op **oplossing maken**. Het kan even duren voordat de oplossing is ingericht.
  
   ![Oplossing maken](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Nadat de inrichting is voltooid, klikt u op **starten**. Sommige gesimuleerde apparaten worden tijdens het inrichtings proces voor de oplossing gemaakt. Klik op **apparaten** om ze uit te checken.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klik op **een apparaat toevoegen**.

6. Klik op **Nieuw toevoegen** voor **aangepast apparaat**.
  
   ![Een nieuw apparaat toevoegen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klik op ik **wil mijn eigen apparaat-id definiëren**, Voer in `AZ3166` en klik vervolgens op **maken**.
  
   ![Apparaat met ID maken](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Noteer **IOT hub hostnaam** en klik op **gereed**.

## <a name="open-the-remotemonitoring-sample"></a>Het RemoteMonitoring-voor beeld openen

1. Verbreek de verbinding van de DevKit met de computer als deze is verbonden.

2. Start VS Code.

3. Verbind de DevKit met uw computer. VS code detecteert automatisch uw DevKit en opent de volgende pagina's:

   * De introductie pagina van DevKit.
   * Arduino-voor beelden: praktijk voorbeelden om aan de slag te gaan met DevKit.

4. Vouw de sectie **ARDUINO-voor beelden** aan de linkerkant uit, blader naar **voor beelden voor MXCHIP AZ3166 > AzureIoT** en selecteer **RemoteMonitoring**. Er wordt een nieuw versus code venster geopend met daarin een projectmap.

   > [!NOTE]
   > Als u het deel venster sluit, kunt u het opnieuw openen. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) om het opdracht palet te openen, typ **Arduino** en zoek en selecteer vervolgens **Arduino: voor beelden**.

## <a name="provision-required-azure-services"></a>Vereiste Azure-Services inrichten

Voer in het venster oplossing uw taak uit `Ctrl+P` (macOS: `Cmd+P` ) door het invoeren `task cloud-provision` van het tekstvak.

In de VS code-terminal wordt u door een interactieve opdracht regel begeleid bij het inrichten van de vereiste Azure-Services.

![Azure-resources inrichten](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Bouw en upload de toestel code

1. Gebruik `Ctrl+P` (macOS: `Cmd + P` ) en typ **taak configuratie-apparaat-Connection**.

2. De Terminal vraagt u of u een connection string wilt gebruiken dat uit de stap is opgehaald `task cloud-provision` . U kunt ook uw eigen apparaat connection string invoeren door te klikken op nieuwe maken...

3. De Terminal vraagt u de configuratie modus in te voeren. Als u dit wilt doen, houdt u de knop A ingedrukt en geeft u de knop opnieuw instellen op. In het scherm worden de DevKit-ID en configuratie weer gegeven.

   ![Invoer connection string](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Als `task config-device-connection` u klaar bent, klikt `F1` u op laden VS code opdrachten en selecteert u `Arduino: Upload` . VS code controleert de Arduino-schets en uploadt deze.
  
   ![Verificatie en upload van de Arduino-schets](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

De DevKit wordt opnieuw opgestart en het uitvoeren van de code wordt gestart.

## <a name="test-the-project"></a>Het project testen

Wanneer de voor beeld-app wordt uitgevoerd, verzendt DevKit sensor gegevens over WiFi naar uw oplossings versneller van Azure IoT-externe controle. Voer de volgende stappen uit om het resultaat te bekijken:

1. Ga naar de oplossings versneller van Azure IoT voor externe controle en klik op **dash board**.

2. Op de oplossings console voor externe controle ziet u de status van uw DevKit-sensor.

   ![Sensor gegevens in azure IoT-oplossings versneller voor externe controle](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Apparaat-ID wijzigen

Als u de hardcoded **AZ3166** wilt wijzigen in een aangepaste apparaat-id in de code, wijzigt u de regel met code die wordt weer gegeven in het [voor beeld externe controle](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2).

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleeg dan [de veelgestelde vragen over de IOT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neem contact op met de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat verbindt met uw Azure IoT-oplossing voor externe controle en de sensor gegevens visualiseren, zijn dit de voorgestelde volgende stappen:

* [Overzicht van de Azure IoT-oplossings Accelerators](/azure/iot-suite/)

* [IoT-ontwikkelaars pakket](https://microsoft.github.io/azure-iot-developer-kit/)