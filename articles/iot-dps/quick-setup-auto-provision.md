---
title: 'Quickstart: IoT Hub Device Provisioning Service instellen in de Azure-portal'
description: 'Quickstart: Azure IoT Hub DPS (Device Provisioning Service) instellen in de Azure-portal'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e1bd521e9798b09f7930b43ab95c7cd7ef9e693d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737916"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Quickstart: IoT Hub Device Provisioning Service instellen met Azure Portal

IoT Hub Device Provisioning Service kan worden gebruikt voor IoT Hub om Just-In-Time-inrichting naar de gewenste IoT-hub mogelijk te maken, zonder tussenkomst van de gebruiker, zodat klanten miljoenen IoT-apparaten op een veilige en schaalbare manier kunnen inrichten. Azure IoT Hub Device Provisioning Service biedt ondersteuning voor IoT-apparaten met verificatie via TPM, een symmetrische sleutel en een X.509-certificaat. Raadpleeg [Overzicht van IoT Hub Device Provisioning Service](./about-iot-dps.md) voor meer informatie

In deze quickstart leert u hoe u IoT Hub Device Provisioning Service instelt in de Azure-portal om uw apparaten in te richten met de volgende stappen:

* De Azure-portal gebruiken om een IoT-hub te maken
* Azure Portal gebruiken voor het maken van een IoT Hub Device Provisioning Service en het ophalen van het id-bereik
* De IoT-hub koppelen aan de Device Provisioning Service

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om aan de slag te gaan met dit artikel. Als u dat nog niet hebt gedaan, kunt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)maken.


## <a name="create-an-iot-hub"></a>Een IoT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Een nieuwe IoT Hub Device Provisioning Service maken

1. Selecteer opnieuw de knop **+ Een resource maken**.

2. *Zoek op de marketplace* naar de **Device Provisioning Service**. Selecteer **IoT Hub Device Provisioning Service** en tik op de knop **Maken**. 

3. Geef de volgende informatie op voor uw nieuwe exemplaar van de Device Provisioning Service en tik op **Maken**.

    * **Naam:** Geef een unieke naam op voor uw nieuwe Device Provisioning Service-exemplaar. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.
    * **Abonnement:** Kies het abonnement dat u wilt gebruiken om deze Device Provisioning Service-exemplaar te maken.
    * **Resourcegroep:** dit veld kunt u gebruiken om een nieuwe resourcegroep te maken of het nieuwe exemplaar aan een bestaande resourcegroep toe te voegen. Kies dezelfde resourcegroep als die de IoT-hub bevat die u hierboven hebt gemaakt, zoals **TestResources**. Door alle gerelateerde resources samen in een groep te plaatsen, kunt u ze samen beheren. Als u bijvoorbeeld de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. Zie [Azure Resource Manager-resourcegroepen beheren](../azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie.
    * **Locatie:** Selecteer de dichtstbijzijnde locatie voor uw apparaten.

      ![Basisinformatie over uw Device Provisioning Service-exemplaar invoeren op de portalblade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecteer de meldingsknop om het maken van het resource-exemplaar te bewaken. Wanneer de service is geïmplementeerd, selecteert u **Vastmaken aan dashboard** en vervolgens op **Naar de resource gaan**.

    ![De implementatiemelding bewaken](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>De IoT-hub en uw Device Provisioning Service koppelen

In deze sectie voegt u een configuratie toe aan het Device Provisioning Service-exemplaar. Deze configuratie bepaalt welke IoT-hub wordt ingericht voor welke apparaten.

1. Selecteer de knop **Alle resources** in het menu links in de Azure-portal. Selecteer het Device Provisioning Service-exemplaar dat u in de voorgaande sectie hebt gemaakt. 

    Als uw menu is geconfigureerd met behulp van de modus **Flyout** in plaats van de modus **Vastgezet** in de portalinstellingen, moet u linksboven op de 3 regels klikken om het portalmenu aan de linkerkant te openen.  

2. Selecteer **Gekoppelde IoT-hubs** in het menu van de Device Provisioning Service. Tik bovenaan op de knop **+ Toevoegen**. 

3. Geef, op de pagina **Koppeling toevoegen aan IoT-hub**, de volgende informatie op om uw nieuwe instantie van de Device Provisioning Service te koppelen aan een IoT-hub. Tik vervolgens op **Opslaan**. 

    * **Abonnement:** Selecteer het abonnement dat de IoT-hub bevat die u wilt koppelen aan uw nieuwe Device Provisioning Service-exemplaar.
    * **IoT-hub:** Selecteer de IoT-hub die u wilt koppelen aan uw nieuwe Device Provisioning Service-exemplaar.
    * **Toegangsbeleid:** selecteer **iothubowner** als de referenties om de koppeling met de IoT-hub tot stand te brengen.  

      ![Koppel de naam van de hub aan het Device Provisioning Service-exemplaar op de portalblade](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Nu ziet u de geselecteerde hub onder de blade **Gekoppelde IoT-hubs**. Mogelijk moet u op **Vernieuwen** tikken om deze weer te geven.


## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze Quick Start in Azure Portal zijn gemaakt.

1. Selecteer in het linkermenu in de Azure-portal **Alle resources** en selecteer vervolgens uw Device Provisioning Service. Selecteer **Verwijderen** bovenaan het detailvenster voor het apparaat.  
2. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Selecteer **Verwijderen** bovenaan het detailvenster voor de hub.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub en een Device Provisioning Service-exemplaar geïmplementeerd en de twee resources gekoppeld. Als u wilt weten hoe u deze instellingen gebruikt voor het inrichten van een gesimuleerd apparaat, gaat u verder met de rest van de quickstart voor het maken van een gesimuleerd apparaat.

> [!div class="nextstepaction"]
> [Quickstart voor het maken van een gesimuleerd apparaat](./quick-create-simulated-device-symm-key.md)
