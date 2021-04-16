---
title: Azure IoT Hub Device Provisioning Service - Apparaatconcepten
description: Beschrijft concepten voor het opnieuw inrichten van apparaten voor Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbc83ec62c10fae00e371cd9ad95cf2860495fad
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575765"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub concepten voor het opnieuw in de inrichting van apparaten

Tijdens de levenscyclus van een IoT-oplossing is het gebruikelijk om apparaten te verplaatsen tussen IoT-hubs. De redenen voor deze overstap kunnen de volgende scenario's zijn:

* **Geolocatie/GeoLatency:** Wanneer een apparaat tussen locaties wordt verplaatst, wordt de netwerklatentie verbeterd door het apparaat te migreren naar een dichtere IoT-hub.

* **Multitenancy:** een apparaat kan binnen dezelfde IoT-oplossing worden gebruikt en opnieuw worden toegewezen aan een nieuwe klant of klantsite. Deze nieuwe klant kan worden gebruikt voor een andere IoT-hub.

* **Oplossingswijziging:** een apparaat kan worden verplaatst naar een nieuwe of bijgewerkte IoT-oplossing. Voor deze opnieuw toewijzen moet het apparaat mogelijk communiceren met een nieuwe IoT-hub die is verbonden met andere back-endonderdelen.

* **Quarantaine:** vergelijkbaar met een oplossingswijziging. Een apparaat dat defect is, beschadigd of verouderd is, kan opnieuw worden toegewezen aan een IoT-hub die alleen kan worden bijgewerkt en weer conform kan worden gemaakt. Zodra het apparaat goed werkt, wordt het weer gemigreerd naar de hoofdhub.

De ondersteuning voor het opnieuw inrichten van apparaten in Device Provisioning Service is aan deze behoeften voldoen. Apparaten kunnen automatisch opnieuw worden toegewezen aan nieuwe IoT-hubs op basis van het herinrichtingsbeleid dat is geconfigureerd op basis van de inschrijvingsinvoer van het apparaat.

## <a name="device-state-data"></a>Apparaattoestandsgegevens

De apparaattoestandgegevens bestaan uit de [mogelijkheden van de apparaat](../iot-hub/iot-hub-devguide-device-twins.md) dubbel en het apparaat. Deze gegevens worden opgeslagen in het Device Provisioning Service-exemplaar en de IoT-hub waar een apparaat aan is toegewezen.

![Diagram dat laat zien hoe inrichting werkt met Device Provisioning Service.](./media/concepts-device-reprovisioning/dps-provisioning.png)

Wanneer een apparaat in eerste instantie is ingericht met een Device Provisioning Service-exemplaar, worden de volgende stappen uitgevoerd:

1. Het apparaat verzendt een inrichtingsaanvraag naar een Device Provisioning Service-exemplaar. Het service-exemplaar verifieert de apparaat-id op basis van een inschrijvingsinvoer en maakt de eerste configuratie van de statusgegevens van het apparaat. Het service-exemplaar wijst het apparaat toe aan een IoT-hub op basis van de inschrijvingsconfiguratie en retourneert die IoT-hubtoewijzing aan het apparaat.

2. Het inrichtingsservice-exemplaar geeft een kopie van de initiële statusgegevens van het apparaat aan de toegewezen IoT-hub. Het apparaat maakt verbinding met de toegewezen IoT-hub en begint bewerkingen.

Na een periode kunnen de apparaattoestandgegevens op de IoT-hub worden bijgewerkt door apparaatbewerkingen [en](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) [back-endbewerkingen.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) De initiële statusinformatie van het apparaat die is opgeslagen in het Device Provisioning Service-exemplaar blijft ongewijzigd. Deze ongewijzigde apparaattoestandsgegevens zijn de eerste configuratie.

![Inrichten met device provisioning service](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Afhankelijk van het scenario kan het, wanneer een apparaat tussen IoT-hubs wordt verplaatst, ook nodig zijn om de apparaattoestand bijgewerkt op de vorige IoT-hub te migreren naar de nieuwe IoT-hub. Deze migratie wordt ondersteund door het opnieuw inrichten van beleid in Device Provisioning Service.

## <a name="reprovisioning-policies"></a>Beleid voor opnieuw in te stellen

Afhankelijk van het scenario verzendt een apparaat meestal een aanvraag naar een instantie van de inrichtingsservice bij opnieuw opstarten. Het biedt ook ondersteuning voor een methode voor het handmatig activeren van inrichting op aanvraag. Het beleid voor het opnieuw inrichten van een inschrijvingsinvoer bepaalt hoe het device provisioning service-exemplaar deze inrichtingsaanvragen verwerkt. Het beleid bepaalt ook of de statusgegevens van het apparaat moeten worden gemigreerd tijdens het opnieuw invisioneren. Hetzelfde beleid is beschikbaar voor afzonderlijke inschrijvingen en registratiegroepen:

* **Gegevens opnieuw inrichten en migreren:** dit beleid is de standaardinstelling voor nieuwe inschrijvingsgegevens. Dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvingsinvoer een nieuwe aanvraag indienen (1). Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs verandert, wordt de apparaatregistratie bij de eerste IoT-hub verwijderd. De bijgewerkte statusinformatie van het apparaat van die initiële IoT-hub wordt gemigreerd naar de nieuwe IoT-hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **Toewijzen.**

    ![Diagram waarin wordt weergegeven dat een beleid actie onderneemt wanneer apparaten die zijn gekoppeld aan de inschrijvingsinvoer een nieuwe aanvraag indienen.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Opnieuw inrichten en opnieuw** instellen naar eerste configuratie: dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvingsinvoer een nieuwe inrichtingsaanvraag indienen (1). Afhankelijk van de configuratie van de inschrijvingsinvoer kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT-hubs verandert, wordt de apparaatregistratie bij de eerste IoT-hub verwijderd. De eerste configuratiegegevens die het exemplaar van de inrichtingsservice heeft ontvangen toen het apparaat werd ingericht, worden verstrekt aan de nieuwe IoT-hub (2). Tijdens de migratie wordt de status van het apparaat gerapporteerd als **Toewijzen.**

    Dit beleid wordt vaak gebruikt voor het terugzetten van de fabrieksinstellingen zonder IoT-hubs te wijzigen.

    ![Diagram waarin wordt weergegeven hoe een beleid actie onderneemt wanneer apparaten die zijn gekoppeld aan de inschrijvingsinvoer een nieuwe inrichtingsaanvraag indienen.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nooit opnieuw inrichten:** het apparaat wordt nooit opnieuw toegewezen aan een andere hub. Dit beleid wordt verstrekt voor het beheren van achterwaartse compatibiliteit.

> [!NOTE]
> DPS roept altijd de webhook voor aangepaste toewijzing aan, ongeacht het beleid voor opnieuw inrichten voor het geval er nieuwe [ReturnData](how-to-send-additional-data.md) voor het apparaat is. Als het beleid voor opnieuw inrichten zo is ingesteld dat deze nooit opnieuw wordt **ingericht,** wordt de webhook aangeroepen, maar wordt de toegewezen hub niet door het apparaat gewijzigd.

### <a name="managing-backwards-compatibility"></a>Achterwaartse compatibiliteit beheren

Vóór september 2018 vertonen apparaattoewijzingen aan IoT-hubs een plakkerig gedrag. Wanneer een apparaat het inrichtingsproces heeft doorlopen, wordt het alleen weer toegewezen aan dezelfde IoT-hub.

Voor oplossingen die afhankelijk zijn van dit gedrag, bevat de inrichtingsservice achterwaartse compatibiliteit. Dit gedrag wordt momenteel gehandhaafd voor apparaten volgens de volgende criteria:

1. De apparaten maken verbinding met een API-versie vóór de beschikbaarheid van systeemeigen ondersteuning voor opnieuw inrichten in Device Provisioning Service. Raadpleeg de onderstaande API-tabel.

2. Voor de inschrijvingsinvoer voor de apparaten is geen beleid voor opnieuw inrichtingen ingesteld.

Deze compatibiliteit zorgt ervoor dat eerder geïmplementeerde apparaten hetzelfde gedrag ervaren als tijdens de eerste test. Als u het vorige gedrag wilt behouden, moet u geen beleid voor opnieuw in te voeren in deze inschrijvingen opslaan. Als een beleid voor opnieuw in te stellen is ingesteld, heeft het beleid voor opnieuw in te stellen voorrang op het gedrag. Door toe te staan dat het beleid voor opnieuw in te stellen voorrang krijgt, kunnen klanten het gedrag van het apparaat bijwerken zonder dat ze het apparaat opnieuw moeten maken.

In het volgende stroomdiagram kunt u zien wanneer het gedrag aanwezig is:

![stroomdiagram met achterwaartse compatibiliteit](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

In de volgende tabel ziet u de API-versies vóór de beschikbaarheid van systeemeigen ondersteuning voor opnieuw inrichten in Device Provisioning Service:

| REST-API | C SDK | Python-SDK |  Node SDK | Java-SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [04-01-2018 en eerder](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 en eerder](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 en eerder](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 of eerder](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 of eerder](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 of eerder](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Deze waarden en koppelingen zullen waarschijnlijk veranderen. Dit is slechts een tijdelijke aanduiding die probeert te bepalen waar de versies door een klant kunnen worden bepaald en wat de verwachte versies zijn.

## <a name="next-steps"></a>Volgende stappen

* [Apparaten opnieuw inprovisionen](how-to-reprovision.md)
