---
title: IoT Plug en Play-apparaten certificeren | Microsoft Docs
description: Meer informatie over het uitvoeren van tests en het verzenden van een apparaat voor certificering
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 43a568149ccf85579b4f8190974fff872d6e09ea
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199642"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>IoT Plug en Play-apparaten certificeren

Het IoT Plug en Play-apparaatcertificeringsprogramma bevat hulpprogramma's om te controleren of een apparaat voldoet aan de vereisten voor de IoT-Plug en Play certificering. De hulpprogram ma's helpen organisaties ook de beschik baarheid van hun IoT Plug en Play-apparaten te belichten. Deze gecertificeerde apparaten zijn afgestemd op IoT-oplossingen en helpen de time-to-market te verminderen.

In dit artikel leest u informatie over:

- De Azure IoT-opdracht regel programma-extensie voor de Azure CLI installeren
- De IoT-Plug en Play tests uitvoeren om uw Device-toepassing te valideren tijdens de ontwikkelings fase  
- De Azure Certified Device Portal gebruiken om de device-app te valideren

## <a name="prepare-your-device"></a>Uw apparaat voorbereiden

De toepassings code die wordt uitgevoerd op uw IoT-Plug en Play moet:

- Verbinding maken met Azure IoT Hub met behulp van de [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).
- Volg de [IOT-plug a Play-conventies](concepts-convention.md) voor het implementeren van telemetrie, eigenschappen en opdrachten.

De toepassing is software die los van het besturings systeem is geïnstalleerd of die is gebundeld met het besturings systeem in een firmware-installatie kopie die is geflasht op het apparaat.

Het certificerings proces controleert of het apparaat IoT Plug en Play compatibel is door te controleren of de implementatie van het apparaat overeenkomt met de telemetrie, eigenschappen en opdrachten die zijn gedefinieerd in het [DTDL-apparaat model (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) en dat het model beschikbaar is in de [opslag plaats van het open bare Azure IOT-model](concepts-model-repository.md).

Om aan de certificerings vereisten te voldoen, moet uw apparaat:

- Maakt verbinding met Azure IoT Hub met behulp van de [DPS](../iot-dps/about-iot-dps.md).
- U kunt telemetrie, eigenschappen of opdrachten implementeren volgens de IoT-Plug en Play Conventie.
- Beschrijf de interacties van het apparaat met een [DTDL v2](https://aka.ms/dtdl) -model.
- Publiceer het model en alle vereiste interfaces in de opslag plaats van het open bare Azure IoT-model
- De model-ID verzenden tijdens de [registratie van DPS](./concepts-developer-guide-device.md#dps-payload) in de nettolading van de DPS-inrichting.
- De model-ID tijdens de [MQTT-verbinding](./concepts-developer-guide-device.md#model-id-announcement)aankondigen.
- Alle modellen van apparaten moeten compatibel zijn met [Azure IOT Central](../iot-central/core/overview-iot-central-developer.md).

> [!NOTE]
> Op dit moment ondersteunt Azure IoT Central de gegevens typen **matrix** en **georuimtelijke** DTDL niet volledig.

## <a name="test-with-the-azure-iot-extension-cli"></a>Testen met de Azure IoT-extensie-CLI

Met de [Azure IOT cli-extensie](/cli/azure/ext/azure-iot/iot/product) kunt u controleren of de implementatie van het apparaat overeenkomt met het model voordat u het apparaat verzendt voor certificering via de Azure Certified Device-Portal.

De volgende stappen laten zien hoe u de certificerings tests voorbereidt en uitvoert met behulp van de CLI:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>De Azure IoT-extensie voor de Azure CLI installeren

Zie de installatie-instructies voor het instellen van de [Azure cli](/cli/azure) in uw omgeving.

Als u de Azure IoT-extensie wilt installeren, voert u de volgende opdracht uit:

```azurecli
az extension add --name azure-iot
```

Zie [Azure CLI voor Azure IOT voor](/cli/azure/azure-cli-reference-for-iot)meer informatie.

### <a name="create-a-new-product-test"></a>Een nieuwe product test maken

Met de volgende opdracht maakt u een test met behulp van DPS met een Attestation-methode voor symmetrische sleutels:

- Hiermee wordt een nieuw product gemaakt om te testen en wordt een test configuratie gegenereerd. In de uitvoer wordt de DPS-informatie weer gegeven die het apparaat moet gebruiken voor het inrichten van: de primaire sleutel, de apparaat-ID en de ID-Scope.
- Hiermee geeft u de map met de DTDL-bestanden die uw model beschrijven.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> Wanneer u de CLI gebruikt, moet u [zich aanmelden](/cli/azure/authenticate-azure-cli) bij uw abonnement.

De JSON-uitvoer van de opdracht bevat de `primaryKey` , `registrationId` en `scopeID` om te gebruiken wanneer u verbinding maakt met uw apparaat.

Verwachte uitvoer:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Uw apparaat verbinden

Gebruik de DPS-gegevens uitvoer van de vorige opdracht om uw apparaat te verbinden met de test IoT Hub-exemplaar.

### <a name="manage-and-configure-the-product-tests"></a>De product tests beheren en configureren

Genereer een configuratie bestand voor een product test wanneer het apparaat is verbonden en klaar is om te communiceren met de IoT-hub. Het bestand maken:

- Gebruik de test `id` vanuit de uitvoer van de vorige opdracht.
- Gebruik de `--wait` para meter om de test case op te halen.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Verwachte uitvoer:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

U kunt de `az iot product test case update` opdracht gebruiken om het test configuratie bestand te wijzigen.

### <a name="run-the-tests"></a>De tests uitvoeren

Nadat u de test configuratie hebt gegenereerd, is de volgende stap het uitvoeren van de tests. Gebruik hetzelfde `devicetestId` van de vorige opdrachten als para meter om de tests uit te voeren. Controleer de test resultaten om er zeker van te zijn dat alle tests een status hebben `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Voorbeeld uitvoer van test uitvoering

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Testen met behulp van het Azure Certified Device-portal

De volgende stappen laten zien hoe u de [Azure-gecertificeerde Apparaatbeheer](https://aka.ms/acdp) kunt gebruiken om de onboarding uit te voeren, product gegevens te registreren, een aan de slag-hand leiding te verzenden en de certificerings tests uit te voeren.

### <a name="onboarding"></a>Onboarding

Als u de [certificerings Portal](https://aka.ms/acdp)wilt gebruiken, moet u een Azure Active Directory van uw werk-of school-Tenant gebruiken.

Als u de modellen wilt publiceren naar de [open bare Azure IOT-model opslagplaats](https://github.com/Azure/iot-plugandplay-models), moet uw account lid zijn van de [Microsoft Partner Network](https://partner.microsoft.com). Het systeem controleert of de Microsoft Partner Network-ID bestaat en of het account volledig gecontroleerd is voordat het naar de catalogus van het apparaat wordt gepubliceerd.

### <a name="company-profile"></a>Bedrijfs profiel

U kunt uw bedrijfs profiel beheren vanuit het navigatie menu aan de linkerkant. Het bedrijfs profiel bevat de bedrijfs-URL, het e-mail adres en het bedrijfs logo. De programma overeenkomst moet op deze pagina worden geaccepteerd voordat u certificerings bewerkingen uitvoert.

De gegevens van het bedrijfs profiel worden gebruikt in de beschrijving van het apparaat in de catalogus met apparaten.

### <a name="create-new-project"></a>Nieuw project maken

Als u een apparaat wilt certificeren, moet u eerst een nieuw project maken.

Navigeer naar de [certificerings Portal](https://aka.ms/acdp). Selecteer op de pagina **projecten** *+ Nieuw project maken*. Voer vervolgens een naam in voor het project, de naam van het apparaat en selecteer een apparaatklasse.

De product gegevens die u tijdens het certificerings proces opgeeft, worden in vier categorieën onderverdeeld:

- Apparaatgegevens. Verzamelt informatie over het apparaat, zoals de naam, beschrijving, certificeringen en het besturings systeem.
- De **aan de slag** -hand leiding. U moet de hand leiding verzenden als een PDF-document dat door de systeem beheerder moet worden goedgekeurd voordat het apparaat wordt gepubliceerd.
- Marketing Details. Bied marketing gegevens van de klant voor uw apparaat. De marketing informatie omvat als beschrijving, een foto en distributeurs.
- Aanvullende branche certificeringen. In deze optionele sectie kunt u aanvullende informatie geven over andere certificeringen die het apparaat heeft ontvangen.

### <a name="connect-and-test"></a>Verbinding maken en testen

De stap verbinding maken en testen controleert of uw apparaat voldoet aan de vereisten voor de IoT-Plug en Play-certificering.

Er zijn drie stappen die moeten worden uitgevoerd:

1. Interfaces verbinden en detecteren. Het apparaat moet via DPS verbinding maken met de Azure IoT-certificerings service. Kies de verificatie methode (X. 509-certificaat, symmetrische sleutels of trusted platform module) om de Device Application te gebruiken en bij te werken met de DPS-informatie.
1. Interfaces controleren. Controleer de interface en zorg ervoor dat elk een Payload-invoer heeft die zinvol is voor testen.
1. Proeven. Het systeem test elk apparaat model om te controleren of de telemetrie, eigenschappen en opdrachten die in het model worden beschreven, voldoen aan de IoT Plug en Play-conventies. Wanneer de test is voltooid, selecteert u de koppeling **Logboeken weer geven** om de telemetrie van het apparaat weer te geven en de onbewerkte gegevens die worden verzonden naar IOT hub apparaatspecifieke eigenschappen van het apparaat.

### <a name="submit-and-publish"></a>Verzenden en publiceren

De laatste vereiste fase is het indienen van het project voor controle. In deze stap wordt een lid van een Azure Certified-teamlid geïnformeerd over het volt ooien van het project, met inbegrip van de details van het apparaat en de marketing, en de aan de slag-hand leiding. Een teamlid kan contact met u opnemen in het e-mail adres van het bedrijf dat eerder is meegeleverd met vragen of aanvragen voor goed keuring heeft bewerkt.

Als uw apparaat verdere hand matige validatie als onderdeel van de certificering vereist, ontvangt u op dit moment een kennisgeving.

Wanneer een apparaat is gecertificeerd, kunt u ervoor kiezen om uw product gegevens te publiceren naar de Azure Certified-apparaatinstantie met behulp van de functie **publiceren op catalogus** op de pagina product overzicht.

## <a name="next-steps"></a>Volgende stappen

Nu de verzen ding van het apparaat is voltooid, kunt u contact opnemen met het certificerings team van het apparaat [iotcert@microsoft.com](mailto:iotcert@microsoft.com) om door te gaan met de volgende stappen, waaronder Microsoft Partner Network lidmaatschaps validatie en een overzicht van de aan de slag-hand leidingen. Als aan alle vereisten wordt voldaan, kunt u ervoor kiezen om uw apparaat te laten opnemen in de [catalogus met gecertificeerde Azure IOT-apparaten](https://aka.ms/devicecatalog).