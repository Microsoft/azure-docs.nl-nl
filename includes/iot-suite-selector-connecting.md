---
title: bestand opnemen
description: bestand opnemen
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d4b93685160250a5abe7c050db89978ae8ceb1a9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556799"
---
> [!div class="op_single_selector"]
> * [C op Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C op Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C op Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (algemeen)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js op Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md)

In deze zelf studie implementeert u een **Chiller** -apparaat dat de volgende telemetrie verzendt naar de [oplossings versneller](../articles/iot-accelerators/about-iot-accelerators.md)voor externe controle:

* Temperatuur
* Druk
* Vochtigheid

Ter vereenvoudiging genereert de code voor beeld-telemetrie-waarden voor de **Chiller**. U kunt het voor beeld uitbreiden door echte Sens oren te verbinden met uw apparaat en daad werkelijk telemetrie te verzenden.

Het voor beeld-apparaat is ook:

* Hiermee worden meta gegevens naar de oplossing verzonden om de mogelijkheden ervan te beschrijven.
* Reageert op acties die zijn geactiveerd op de pagina **apparaten** in de oplossing.
* Reageert op configuratie wijzigingen verzenden vanaf de pagina **apparaten** in de oplossing.

U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Voordat u code voor uw apparaat schrijft, implementeert u de Accelerator voor externe controle en voegt u een nieuw echt apparaat aan de oplossing toe.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Uw oplossings versneller voor externe controle implementeren

Het **Chiller** -apparaat dat u in deze zelf studie maakt, verzendt gegevens naar een exemplaar van de oplossings versneller voor [externe controle](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) . Als u de oplossings versneller voor externe controle in uw Azure-account nog niet hebt ingericht, raadpleegt u [de oplossing voor externe controle van oplossingen implementeren](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Wanneer het implementatie proces voor de oplossing voor externe controle is voltooid, klikt u op **starten** om het dash board van de oplossing in uw browser te openen.

![Het dash board van de oplossing](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Uw apparaat toevoegen aan de oplossing voor bewaking op afstand

> [!NOTE]
> Als u al een apparaat in uw oplossing hebt toegevoegd, kunt u deze stap overs Laan. De volgende stap vereist echter dat uw apparaat connection string. U kunt de connection string van een apparaat ophalen via de [Azure Portal](https://portal.azure.com) of met het hulp programma [AZ IOT](/cli/azure/iot?view=azure-cli-latest) cli.

Een apparaat kan alleen verbinding maken met de oplossings versneller als het zichzelf identificeert IoT Hub met geldige referenties. U hebt de mogelijkheid om de apparaat-connection string die deze referenties bevat op te slaan wanneer u het apparaat aan de oplossing toevoegt. Verderop in deze zelf studie neemt u het apparaat connection string in uw client toepassing op.

Als u een apparaat wilt toevoegen aan uw oplossing voor controle op afstand, voert u de volgende stappen uit op de pagina **device Explorer** in de oplossing:

1. Kies **+ nieuw apparaat** en kies vervolgens **real** als **type apparaat**:

    ![Echt apparaat toevoegen](media/iot-suite-selector-connecting/devicesprovision.png)

1. Voer de **fysieke Chiller** in als de apparaat-id. Kies de opties **symmetrische sleutel** en **automatisch genereren van sleutels** :

    ![Opties voor apparaten kiezen](media/iot-suite-selector-connecting/devicesoptions.png)

1. Kies **Toepassen**. Noteer vervolgens de waarden voor de **apparaat-id**, de **primaire sleutel** en de **primaire sleutel van de verbindings reeks** :

    ![Referenties ophalen](media/iot-suite-selector-connecting/credentials.png)

U hebt nu een echt apparaat toegevoegd aan de Accelerator van de oplossing voor externe controle en de apparaatgegevens connection string. In de volgende secties implementeert u de client toepassing die gebruikmaakt van het apparaat connection string om verbinding te maken met uw oplossing.

De client toepassing implementeert het ingebouwde **Chiller** -model. In een oplossings versneller apparaat model geeft u het volgende op voor een apparaat:

* De eigenschappen die het apparaat rapporteert aan de oplossing. Een **Chiller** -apparaat rapporteert bijvoorbeeld informatie over de firmware en locatie.
* De typen telemetrie die het apparaat naar de oplossing verzendt. Een **Chiller** -apparaat verzendt bijvoorbeeld Tempe ratuur, vochtigheid en druk waarden.
* De methoden die u kunt plannen vanuit de oplossing om op het apparaat te worden uitgevoerd. Een **Chiller** -apparaat moet bijvoorbeeld methoden voor **opnieuw opstarten**, **FirmwareUpdate**, **EmergencyValveRelease** en **IncreasePressure** implementeren.