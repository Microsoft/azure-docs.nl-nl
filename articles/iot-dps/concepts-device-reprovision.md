---
title: Concepten van Azure IoT Hub Device Provisioning Service-apparaten
description: Hierin worden de concepten beschreven van het opnieuw inrichten van apparaten voor Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842849"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Concepten IoT Hub apparaten opnieuw inrichten

Tijdens de levens cyclus van een IoT-oplossing is het gebruikelijk om apparaten te verplaatsen tussen IoT-hubs. De redenen hiervoor kunnen de volgende scenario's omvatten:

* **Geolocatie/geolatentie**: wanneer een apparaat tussen locaties wordt verplaatst, wordt de netwerk latentie verbeterd doordat het apparaat wordt gemigreerd naar een dichter IOT-hub.

* **Multitenancy: een** apparaat kan worden gebruikt binnen dezelfde IOT-oplossing en opnieuw worden toegewezen aan een nieuwe klant of klant site. Deze nieuwe klant kan worden verwerkt met behulp van een andere IoT-hub.

* **Oplossings wijziging**: een apparaat kan worden verplaatst naar een nieuwe of bijgewerkte IOT-oplossing. Bij deze opnieuw toewijzen kan het apparaat worden gecommuniceerd met een nieuwe IoT-hub die is verbonden met andere back-end-onderdelen.

* **Quarantaine**: vergelijkbaar met een oplossings wijziging. Een apparaat dat defecte, gemanipuleerd of verouderd is, kan opnieuw worden toegewezen aan een IoT-hub, die alleen kan worden bijgewerkt en weer in overeenstemming kan worden gebracht. Zodra het apparaat correct werkt, wordt het opnieuw gemigreerd naar de hoofd hub.

De ondersteuning voor het opnieuw inrichten binnen de Device Provisioning Service is bedoeld voor deze behoeften. Apparaten kunnen automatisch opnieuw worden toegewezen aan nieuwe IoT-hubs op basis van het beleid voor opnieuw inrichten dat is geconfigureerd op de inschrijvings vermelding van het apparaat.

## <a name="device-state-data"></a>Status gegevens van apparaat

De status gegevens van het apparaat bestaan uit de [dubbele](../iot-hub/iot-hub-devguide-device-twins.md) en apparaats mogelijkheden. Deze gegevens worden opgeslagen in het Device Provisioning service-exemplaar en de IoT-hub waaraan een apparaat is toegewezen.

![Diagram dat laat zien hoe het inrichten werkt met de Device Provisioning Service.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Wanneer een apparaat voor het eerst wordt ingericht met een Device Provisioning service-exemplaar, worden de volgende stappen uitgevoerd:

1. Het apparaat verzendt een inrichtings aanvraag naar een Device Provisioning service-exemplaar. Het service-exemplaar verifieert de apparaat-id op basis van een inschrijvings vermelding en maakt de initiële configuratie van de status gegevens van het apparaat. Met het service-exemplaar wordt het apparaat toegewezen aan een IoT-hub op basis van de registratie configuratie en wordt de toewijzing van de IoT-hub naar het apparaat geretourneerd.

2. Het inrichtings service-exemplaar geeft een kopie van alle initiële status gegevens van het apparaat aan de toegewezen IoT-hub. Het apparaat maakt verbinding met de toegewezen IoT-hub en begint met bewerkingen.

Na verloop van tijd kunnen de status gegevens van het apparaat op de IoT-hub worden bijgewerkt door de bewerkingen van het [apparaat](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) en de [back-end-bewerkingen](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). De initiële status informatie voor de apparaten die is opgeslagen in het Device Provisioning service-exemplaar blijft ongewijzigd. Deze ongewijzigde apparaatstatus is de initiële configuratie.

![Inrichten met Device Provisioning Service](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Afhankelijk van het scenario is het mogelijk dat er, als een apparaat wordt verplaatst tussen IoT-hubs, ook de apparaatstatus moet worden gemigreerd die is bijgewerkt op de vorige IoT-hub naar de nieuwe IoT hub. Deze migratie wordt ondersteund door het opnieuw inrichten van beleid in de Device Provisioning Service.

## <a name="reprovisioning-policies"></a>Beleid opnieuw inrichten

Afhankelijk van het scenario verzendt een apparaat doorgaans een aanvraag naar een inrichtings service-exemplaar bij het opnieuw opstarten. Het ondersteunt ook een methode om het inrichten op aanvraag hand matig te activeren. Het beleid voor opnieuw inrichten voor een inschrijvings vermelding bepaalt hoe het Device Provisioning service-exemplaar deze inrichtings aanvragen verwerkt. Het beleid bepaalt ook of de status gegevens van het apparaat tijdens het opnieuw inrichten moeten worden gemigreerd. Dezelfde beleids regels zijn beschikbaar voor individuele inschrijvingen en inschrijvings groepen:

* **Gegevens opnieuw inrichten en migreren**: dit beleid is de standaard instelling voor nieuwe inschrijvings vermeldingen. Met dit beleid wordt actie ondernomen wanneer apparaten die zijn gekoppeld aan de inschrijvings vermelding een nieuwe aanvraag verzenden (1). Afhankelijk van de configuratie van de registratie vermelding, kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT hubs wijzigt, wordt de registratie van het apparaat met de eerste IoT-hub verwijderd. De bijgewerkte Apparaatstatus van de eerste IoT-hub wordt gemigreerd naar de nieuwe IoT hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **toewijzing**.

    ![Diagram dat laat zien dat een beleid actie onderneemt wanneer apparaten die zijn gekoppeld aan de inschrijvings vermelding een nieuwe aanvraag indienen.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Opnieuw inrichten en opnieuw instellen op de eerste configuratie**: dit beleid voert actie uit wanneer aan de inschrijvings vermelding een nieuwe inrichtings aanvraag wordt verzonden (1). Afhankelijk van de configuratie van de registratie vermelding, kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT hubs wijzigt, wordt de registratie van het apparaat met de eerste IoT-hub verwijderd. De initiële configuratie gegevens die het exemplaar van de inrichtings service dat is ontvangen toen het apparaat werd ingericht, worden geleverd aan de nieuwe IoT hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **toewijzing**.

    Dit beleid wordt vaak gebruikt voor het opnieuw instellen van de fabriek zonder IoT-hubs te wijzigen.

    ![Diagram dat laat zien hoe een beleid actie onderneemt wanneer apparaten die zijn gekoppeld aan de inschrijvings vermelding een nieuwe inrichtings aanvraag indienen.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nooit opnieuw inrichten**: het apparaat wordt nooit opnieuw toegewezen aan een andere hub. Dit beleid is bedoeld voor het beheer van neerwaartse compatibiliteit.

### <a name="managing-backwards-compatibility"></a>Achterwaartse compatibiliteit beheren

Vóór september 2018 heeft het toewijzen van apparaten aan IoT-hubs een plak gedrag. Wanneer een apparaat het inrichtings proces terugkeert, wordt het alleen toegewezen aan dezelfde IoT-hub.

Voor oplossingen die afhankelijk zijn van dit gedrag bevat de inrichtings service achterwaartse compatibiliteit. Dit gedrag wordt momenteel onderhouden voor apparaten op basis van de volgende criteria:

1. De apparaten maken verbinding met een API-versie vóór de beschik baarheid van native ondersteuning voor het opnieuw inrichten in de Device Provisioning Service. Raadpleeg de onderstaande API-tabel.

2. Er is voor de inschrijvings vermelding voor de apparaten geen Revisioning-beleid op ingesteld.

Deze compatibiliteit zorgt ervoor dat eerder geïmplementeerde apparaten hetzelfde gedrag hebben als tijdens de eerste test. Als u het vorige gedrag wilt behouden, slaat u geen beleid voor opnieuw inrichten op bij deze inschrijvingen. Als er een beleid voor opnieuw inrichten is ingesteld, heeft het beleid voor opnieuw inrichten voor rang op het gedrag. Klanten kunnen het gedrag van het apparaat bijwerken zonder dat ze de installatie kopie van het apparaat hoeven te wijzigen.

In het volgende stroom diagram kunt u zien wanneer het gedrag aanwezig is:

![stroom diagram voor achterwaartse compatibiliteit](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

In de volgende tabel ziet u de API-versies vóór de beschik baarheid van native ondersteuning voor het opnieuw inrichten van de Device Provisioning Service:

| REST-API | C SDK | Python-SDK |  Node SDK | Java-SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 en eerder](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 en eerder](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 en eerder](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 of eerder](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 of eerder](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 of eerder](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Deze waarden en koppelingen zijn waarschijnlijk gewijzigd. Dit is alleen een tijdelijke aanduiding om te bepalen waar de versies door een klant kunnen worden bepaald en wat de verwachte versies zullen zijn.

## <a name="next-steps"></a>Volgende stappen

* [Apparaten opnieuw inrichten](how-to-reprovision.md)
