---
title: Azure IoT Hub Device Provisioning Service automatische inrichting gebruiken om de MXChip IoT DevKit te registreren bij IoT Hub | Microsoft Docs
description: Het gebruik van automatische inrichting van Azure IoT Hub Device Provisioning Service (DPS) voor het registreren van de MXChip IoT DevKit met IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d6b6649d03da319171b24baa24983972bf270679
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954542"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Gebruik Azure IoT Hub Device Provisioning Service automatische inrichting om de MXChip IoT DevKit te registreren bij IoT Hub

In dit artikel wordt beschreven hoe u de Azure IoT Hub Device Provisioning Service kunt gebruiken om de MXChip IoT DevKit in te [richten](about-iot-dps.md#provisioning-process) op een Azure IOT hub. In deze zelfstudie leert u het volgende:

* Configureer het globale eind punt van de Device Provisioning Service op een apparaat.
* Een uniek apparaat Secret (UDS) gebruiken om een X. 509-certificaat te genereren.
* Een afzonderlijk apparaat inschrijven.
* Controleer of het apparaat is geregistreerd.

[MXChip IOT DevKit](https://aka.ms/iot-devkit) is een alles-in-een Arduino-compatibel bord met uitgebreide rand apparatuur en Sens oren. U kunt dit ontwikkelen met behulp van [Azure IOT Device Workbench](https://aka.ms/iot-workbench) of [Azure IOT tools](https://aka.ms/azure-iot-tools) Extension Pack in Visual Studio code. De DevKit wordt geleverd met een [catalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) voor groeiende projecten om uw prototype Internet of Things (IOT)-oplossingen te begeleiden die gebruikmaken van Azure-Services.

## <a name="before-you-begin"></a>Voordat u begint

Voer eerst de volgende taken uit om de stappen in deze zelf studie uit te voeren:

* Configureer de Wi-Fi van uw DevKit en bereid uw ontwikkel omgeving voor door de stappen in de sectie de ontwikkel omgeving voorbereiden in [verbinding maken met IOT DEVKIT AZ3166 met Azure IOT hub in de Cloud](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).
* Voer een upgrade uit naar de nieuwste firmware (1.3.0 of hoger) met de zelf studie [DevKit firmware bijwerken](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Maak en koppel een IoT Hub met een Device Provisioning Service-instantie door de stappen in [de IOT hub Device Provisioning Service in te stellen met de Azure Portal](./quick-setup-auto-provision.md).

## <a name="open-sample-project"></a>Voorbeeld project openen

1. Zorg ervoor dat uw IoT-DevKit **niet is verbonden** met uw computer. Start VS code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: voor beelden openen...**. Selecteer vervolgens **IOT DevKit** as Board.

1. Zoek op de pagina met IoT Workbench-voor beelden de apparaatregistratie **met DPS** en klik op voor **Beeld openen**. Selecteert vervolgens het standaardpad voor het downloaden van de voorbeeld code.
    ![Voor beeld openen](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Een uniek apparaat geheim opslaan in de beveiligings opslag van het apparaat

Automatische inrichting kan worden geconfigureerd op een apparaat op basis van het [Attestation-mechanisme](concepts-service.md#attestation-mechanism)van het apparaat. De MXChip IoT DevKit maakt gebruik van de engine voor het samen stellen van de [apparaat-id](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) van de [Trusted Computing Group](https://trustedcomputinggroup.org). Een **uniek apparaat Secret** (UDS) dat is opgeslagen in een STSAFE-beveiligings chip ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) op de DevKit wordt gebruikt voor het genereren van het unieke [X. 509-certificaat](concepts-x509-attestation.md)van het apparaat. Het certificaat wordt later gebruikt voor het inschrijvings proces in de Device Provisioning Service en tijdens de registratie tijdens runtime.

Een typische UDS is een teken reeks van 64, zoals in het volgende voor beeld wordt weer gegeven:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Een UDS opslaan op de DevKit:

1. Klik in VS code op de status balk om de COM-poort voor de DevKit te selecteren.
  ![COM-poort selecteren](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Houd op DevKit de **knop** **opnieuw instellen** in de vervolg keuzelijst en laat deze knop los en laat vervolgens **knop a** los. Uw DevKit voert de configuratie modus in.

1. Klik `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: Apparaatinstellingen configureren... > configuratie unieke teken reeks voor apparaat (UDS)**.
  ![UDS configureren](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Noteer de gegenereerde UDS-teken reeks. U hebt deze nodig voor het genereren van het X. 509-certificaat. Druk vervolgens op `Enter` .
  ![UDS kopiëren](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Controleer in de melding dat de UDS is geconfigureerd op de STSAFE.
  ![De configuratie van UDS geslaagd](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> U kunt ook instellen via een seriële poort met behulp van hulpprogram ma's zoals putty. Volg hiervoor de [configuratie modus](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) .

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Het eind punt van het globale apparaat en de ID-Scope bijwerken

In apparaatcode moet u het [eind punt](./concepts-service.md#device-provisioning-endpoint) voor het inrichten van het apparaat en de id-scope opgeven om de Tenant isolatie te garanderen.

1. Selecteer in de Azure Portal het deel venster **overzicht** van de Device Provisioning Service en noteer de waarden voor het **globale apparaat-eind punt** en de **id-Scope** .
  ![Globaal eind punt en-ID-bereik van Device Provisioning Service](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DevKitDPS. Ino**. Zoek en vervang `[Global Device Endpoint]` en door `[ID Scope]` de waarden die u zojuist hebt genoteerd.
  ![Device Provisioning Service-eind punt](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Vul de `registrationId` variabele in de code in. Alleen alfanumerieke combi Naties, kleine letters en afbreek streepjes met een maximum van 128 tekens zijn toegestaan. Noteer ook de waarde.
  ![Registratie-ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klik op `F1` , typ en selecteer **Azure IOT Device Workbench: code van het apparaat uploaden**. U begint met het compileren en uploaden van de code naar DevKit.
  ![Device Upload](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X. 509-certificaat genereren

Het [Attestation-mechanisme](./concepts-service.md#attestation-mechanism) dat door dit voor beeld wordt gebruikt, is X. 509-certificaat. U moet een hulp programma gebruiken om het te genereren.

1. Klik in VS code op `F1` , typ en selecteer **nieuwe terminal openen** om Terminal venster te openen.

1. Uitvoeren `dps_cert_gen.exe` in `tool` map.

1. Geef de locatie van de gecompileerde binaire bestanden op als `..\.build\DevKitDPS` . Plak vervolgens de **UDS** en **registratie** die u zojuist hebt genoteerd. 
  ![X. 509 genereren](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Een `.pem` X. 509-certificaat wordt in dezelfde map gegenereerd.
  ![X. 509-bestand](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Een vermelding voor apparaatinschrijving maken

1. Open in de Azure Portal uw Device Provisioning Service, navigeer naar de sectie registraties beheren en klik op **Individuele inschrijving toevoegen**.
  ![Afzonderlijke inschrijving toevoegen](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klik op het pictogram bestand naast het **primaire certificaat. pem-of. cer-bestand** om het `.pem` gegenereerde bestand te uploaden.
  ![Upload. pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Controleren of de DevKit is geregistreerd bij Azure IoT Hub

Druk op de knop **Reset** op uw DevKit. U ziet dat **DPS Connected is.** op het scherm DevKit. Nadat het apparaat opnieuw is opgestart, worden de volgende acties uitgevoerd:

1. Vanaf het apparaat wordt een registratie-aanvraag verzonden naar Device Provisioning Service.
1. De Device Provisioning service stuurt een registratie vraag naar die uw apparaat reageert.
1. Bij een geslaagde registratie verzendt de Device Provisioning Service de IoT Hub URI, de apparaat-ID en de versleutelde sleutel terug naar het apparaat.
1. De IoT Hub-client toepassing op het apparaat maakt verbinding met uw hub.
1. Wanneer de verbinding met de hub is geslaagd, ziet u dat het apparaat wordt weer gegeven in de IoT Hub Device Explorer.
  ![Apparaat geregistreerd](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleeg dan de [Veelgestelde vragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)over IOT DevKit of neem contact op met de volgende kanalen voor ondersteuning:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd om een apparaat veilig in te schrijven bij de Device Provisioning Service met behulp van de apparaat Identity compositie engine, zodat het apparaat automatisch kan worden geregistreerd bij Azure IoT Hub. 

In samen vatting hebt u het volgende geleerd:

> [!div class="checklist"]
> * Configureer het globale eind punt van de Device Provisioning Service op een apparaat.
> * Een uniek apparaat geheim gebruiken om een X. 509-certificaat te genereren.
> * Een afzonderlijk apparaat inschrijven.
> * Controleer of het apparaat is geregistreerd.

Meer informatie over het [maken en inrichten van een gesimuleerd apparaat](./quick-create-simulated-device.md).