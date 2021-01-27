---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 4e2abda6e0e3ef3d638952c05c31a50d91d24e88
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900879"
---
Een IoT-hub maken met behulp van Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in de Azure-start pagina de optie **een resource maken** en voer *IOT hub* in op **de Marketplace zoeken**.

1. Selecteer **IoT Hub** in de zoekresultaten en selecteer vervolgens **Maken**.

1. Vul de velden in het tabblad **Basis** als volgt in:

   - **Abonnement**: Selecteer het abonnement dat u voor de hub wilt gebruiken.

   - **Resourcegroep**: Selecteer een Resourcegroep of maak een nieuwe. Als u een nieuwe wilt maken, selecteert u **Nieuwe maken** en vult u de gewenste naam in. Als u een bestaande resourcegroep wilt gebruiken, selecteert u die resourcegroep. Zie [Azure Resource Manager-resourcegroepen beheren](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie. In deze zelfstudie wordt de naam **tutorials-iot-hub-rg** gebruikt.

   - **Regio**: Selecteer de regio waarin u wilt dat uw hub zich bevindt. Selecteer de locatie die het dichtst bij u in de buurt is. Sommige functies, bijvoorbeeld [IoT Hub-apparaatstreams](../articles/iot-hub/iot-hub-device-streams-overview.md) zijn alleen beschikbaar in specifieke regio's. Voor deze beperkte functies moet u een van de ondersteunde regio's selecteren. In deze zelf studie wordt de regio **VS West** gebruikt.

   - **Naam van de IoT Hub**: Voer een naam in voor uw hub. Deze naam moet wereldwijd uniek zijn. In deze zelf studie wordt gebruikgemaakt van **zelf studies-IOT-hub**. U moet uw eigen unieke naam kiezen wanneer u uw hub maakt.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Een hub maken in Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png)

1. Selecteer **Volgende: Netwerken** om verder te gaan met het maken van uw hub.

   Kies de eindpunten die verbinding kunnen maken met uw IoT Hub. U kunt de standaardinstelling **Openbaar eindpunt (alle netwerken)** selecteren, of kiezen voor **Openbaar eindpunt (geselecteerde IP-bereiken)** of **Privé-eindpunt**. Accepteer de standaard instelling voor deze zelf studie.

   ![Kies de eindpunten die verbinding kunnen maken](media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png)

1. Selecteer **Volgende: Beheer** om verder te gaan met het maken van uw hub.

    ![De grootte en schaal instellen voor een nieuwe hub met Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-management.png)

    U kunt de standaardinstellingen accepteren. Indien gewenst kunt u de volgende velden bewerken:

    - **Prijs- en schaalniveau**: De geselecteerde laag. Kies de laag gratis. De gratis optie is bedoeld voor testen en evalueren. Hiermee kunnen 500 apparaten met de hub worden verbonden en maximaal 8000 berichten per dag verzonden. Met de gratis optie kunt u voor elk Azure-abonnement één IoT-hub maken.

    - **IoT Hub-eenheden**: het aantal toegestane berichten per eenheid is afhankelijk van de prijscategorie van uw hub. Als u bijvoorbeeld wilt dat de hub de invoer van 700.000 berichten moet kunnen ondersteunen, dan kiest u twee S1-laageenheden.
    Met de gratis optie kunt u voor elk Azure-abonnement één IoT-hub maken. Zie [De juiste laag kiezen voor uw IoT-hub](../articles/iot-hub/iot-hub-scaling.md) voor informatie over andere opties.

    - **Defender voor IoT**: Schakel dit in om een extra laag beveiligingsbescherming toe te voegen aan IoT en uw apparaten. Deze optie is niet beschikbaar voor hubs in de gratis laag. Raadpleeg [Azure Security Center for IoT](/azure/asc-for-iot/) voor meer informatie over deze functie.

    - **Geavanceerde instellingen** > **Partities voor apparaat-naar-cloud**: met deze eigenschap worden de apparaat-naar-cloud-berichten gerelateerd met het aantal gelijktijdige lezers van de berichten. De meeste hubs hebben maar vier partities nodig. Een hub met een gratis laag is beperkt tot twee partities.

1.  Selecteer **Volgende: Tags** om naar het volgende scherm te gaan.

    Tags zijn naam/waarde-paren. U kunt dezelfde tag aan meerdere resources en resourcegroepen toevoegen om resources te categoriseren en facturering te consolideren. Raadpleeg [Tags gebruiken om uw Azure-resources te organiseren](../articles/azure-resource-manager/management/tag-resources.md) voor meer informatie.

    ![Tags toewijzen voor de hub met Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png)

1.  Selecteer **Volgende: Beoordelen + maken** om uw keuzes te beoordelen. U ziet iets soortgelijks op dit scherm, maar met de waarden die u hebt geselecteerd toen u de hub maakte.

    ![Informatie raadplegen om de nieuwe hub te maken](media/iot-hub-tutorials-create-free-hub/hub-definition-create.png)

1. Noteer de naam van de IoT-hub die u hebt gekozen. U gebruikt deze waarde verderop in de zelfstudie.
