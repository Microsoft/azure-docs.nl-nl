---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cf651a1a09662e3084a8a9bdb6365b69b6ea52b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99579741"
---
In deze sectie wordt beschreven hoe u een IoT-hub maakt met behulp van de [Azure-portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in de Azure-startpagina de knop **+ Een resource maken** en voer vervolgens *IoT Hub* in het veld **Marketplace doorzoeken** in.

1. Selecteer **IoT Hub** in de zoekresultaten en selecteer vervolgens **Maken**.

1. Vul de velden in het tabblad **Basis** als volgt in:

   - **Abonnement**: Selecteer het abonnement dat u voor de hub wilt gebruiken.

   - **Resourcegroep**: Selecteer een Resourcegroep of maak een nieuwe. Als u een nieuwe wilt maken, selecteert u **Nieuwe maken** en vult u de gewenste naam in. Als u een bestaande resourcegroep wilt gebruiken, selecteert u die resourcegroep. Zie [Azure Resource Manager-resourcegroepen beheren](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie.

   - **Regio**: Selecteer de regio waarin u wilt dat uw hub zich bevindt. Selecteer de locatie die het dichtst bij u in de buurt is. Sommige functies, bijvoorbeeld [IoT Hub-apparaatstreams](../articles/iot-hub/iot-hub-device-streams-overview.md) zijn alleen beschikbaar in specifieke regio's. Voor deze beperkte functies moet u een van de ondersteunde regio's selecteren.

   - **Naam van de IoT Hub**: Voer een naam in voor uw hub. Deze naam moet wereldwijd uniek zijn.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Maak een hub in de Azure Portal.":::

1. Selecteer **Volgende: Netwerken** om verder te gaan met het maken van uw hub.

   Kies de eindpunten die verbinding kunnen maken met uw IoT Hub. U kunt de standaardinstelling **Openbaar eindpunt (alle netwerken)** selecteren, of kiezen voor **Openbaar eindpunt (geselecteerde IP-bereiken)** of **Privé-eindpunt**. Accepteer voor dit voorbeeld de standaardinstellingen.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="Kies de eind punten die verbinding kunnen maken.":::

1. Selecteer **Volgende: Beheer** om verder te gaan met het maken van uw hub.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Stel de grootte en schaal voor een nieuwe hub in met behulp van de Azure Portal.":::

    U kunt de standaardinstellingen accepteren. Indien gewenst kunt u de volgende velden bewerken:

    - **Prijs- en schaalniveau**: De geselecteerde laag. U kunt kiezen uit diverse lagen, afhankelijk van hoeveel functies u wilt en hoeveel berichten u per dag wilt verzenden. De gratis optie is bedoeld voor testen en evalueren. Hiermee kunnen 500 apparaten met de hub worden verbonden en maximaal 8000 berichten per dag verzonden. Met de gratis optie kunt u voor elk Azure-abonnement één IoT-hub maken.

      Als u werkt met een quickstart voor IoT Hub-apparaatstreams, selecteert u de gratis laag.

    - **IoT Hub-eenheden**: het aantal toegestane berichten per eenheid is afhankelijk van de prijscategorie van uw hub. Als u bijvoorbeeld wilt dat de hub de invoer van 700.000 berichten moet kunnen ondersteunen, dan kiest u twee S1-laageenheden.
    Zie [De juiste laag kiezen voor uw IoT-hub](../articles/iot-hub/iot-hub-scaling.md) voor informatie over andere opties.

    - **Defender voor IoT**: Schakel dit in om een extra laag beveiligingsbescherming toe te voegen aan IoT en uw apparaten. Deze optie is niet beschikbaar voor hubs in de gratis laag. Raadpleeg [Azure Security Center for IoT](/azure/asc-for-iot/) voor meer informatie over deze functie.

    - **Geavanceerde instellingen** > **Partities voor apparaat-naar-cloud**: met deze eigenschap worden de apparaat-naar-cloud-berichten gerelateerd met het aantal gelijktijdige lezers van de berichten. De meeste hubs hebben maar vier partities nodig.

1. Selecteer **Volgende: Tags** om naar het volgende scherm te gaan.

    Tags zijn naam/waarde-paren. U kunt dezelfde tag aan meerdere resources en resourcegroepen toevoegen om resources te categoriseren en facturering te consolideren. Raadpleeg [Tags gebruiken om uw Azure-resources te organiseren](../articles/azure-resource-manager/management/tag-resources.md) voor meer informatie.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Wijs labels voor de hub toe met behulp van de Azure Portal.":::

1. Selecteer **Volgende: Beoordelen + maken** om uw keuzes te beoordelen. U ziet iets soortgelijks op dit scherm, maar met de waarden die u hebt geselecteerd toen u de hub maakte.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="Bekijk de informatie voor het maken van de nieuwe hub.":::

1. Selecteer **Maken** om de nieuwe hub te maken. De hub wordt binnen enkele minuten gemaakt.