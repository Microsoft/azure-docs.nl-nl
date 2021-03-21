---
title: Snelstart - Een Azure IoT Central-app maken | Microsoft Docs
description: Snelstart - Een nieuwe Azure IoT Central-app maken. Maak de toepassing met behulp van het gratis abonnement of een van de standaardabonnementen.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a1492593c24c2bc350205536006b648608cc5e46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712637"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Snelstart - Een Azure IoT Central-app maken

In deze quickstart ziet u hoe u een Azure IoT Central-toepassing maakt.

## <a name="prerequisite"></a>Vereiste 

U hebt een Azure-abonnement met *Inzender* toegang nodig.

## <a name="create-an-application"></a>Een app maken

Ga naar de website voor het [bouwen van Azure IoT Central-oplossingen](https://aka.ms/iotcentral). Meld u aan met een persoonlijk account of werk- of schoolaccount van Microsoft.

U kunt een nieuwe toepassing maken op basis van een sjabloon uit de lijst met branchespecifieke IoT Central-sjablonen om snel aan de slag te gaan, of een volledig nieuwe toepassing maken op basis van de sjabloon **Aangepaste apps**. In deze quickstart gebruikt u de sjabloon **Aangepaste toepassing**.

Ga als volgt te werk om een nieuwe Azure IoT Central-toepassing wilt maken op basis van de sjabloon **Aangepaste toepassing**:

1. Ga naar de pagina **Bouwen**:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Pagina voor het bouwen van uw IoT-toepassing":::

1. Kies **Aangepaste apps** en zorg ervoor dat de sjabloon **Aangepaste toepassing** is geselecteerd.

1. In Azure IoT Central wordt automatisch een **toepassingsnaam** voorgesteld op basis van de toepassingssjabloon die u hebt geselecteerd. U kunt deze naam gebruiken of uw eigen beschrijvende toepassingsnaam invoeren.

1. Azure IoT Central genereert ook een uniek voorvoegsel voor de **toepassings-URL** voor u op basis van de naam van de toepassing. U gebruikt deze URL voor de toegang tot uw toepassing. Wijzig dit URL-voorvoegsel in iets dat gemakkelijker te onthouden is als u dat wilt.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Pagina Een toepassing maken van Azure IoT Central":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Factureringsgegevens van Azure IoT Central":::

    > [!Tip]
    > Als u op de vorige pagina **Aangepaste app** hebt gekozen, ziet u een vervolgkeuzelijst voor een **toepassingssjabloon**. In de vervolgkeuzelijst kunnen andere sjablonen worden weergegeven die voor u beschikbaar zijn gesteld door uw organisatie.

1. Maak de toepassing met behulp van het gratis 7-daagse abonnement of een van de standaardabonnementen:

    - Toepassingen die u maakt met het *gratis* abonnement zijn gratis gedurende zeven dagen en ondersteunen maximaal vijf apparaten. U kunt ze op enig moment voor het aflopen van het abonnement overzetten naar een betaald abonnement.
        > [!NOTE]
        > Voor toepassingen die zijn gemaakt met het *gratis* abonnement zijn geen Azure-abonnementen vereist en daarom vindt u deze niet in uw Azure-abonnement op de Azure Portal. U kunt alleen gratis apps uit de IoT Central Portal bekijken en beheren.          
    - Toepassingen die u maakt met een *standaard*-abonnement worden gefactureerd per apparaat. U kunt kiezen tussen **Standard 0**, **Standard 1** en **Standard 2**, waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaardabonnementen vindt u op de [pagina met prijzen voor Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Als u een toepassing maakt met behulp van een standaardabonnement, moet u uw *Directory*, *Azure-abonnement* en *locatie* opgeven:
        - De *directory* is de Azure Active Directory waarin uw toepassing wordt gemaakt. Een Azure Active Directory bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure Active Directory hebt, wordt er een voor u gemaakt op het moment dat u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u er gratis een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Nieuwe toepassing**. Uw nieuwe abonnement wordt nu weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
        - De *locatie* is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Gewoonlijk kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om de beste prestaties te verkrijgen. Als u eenmaal een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

1. Controleer de voorwaarden en selecteer **Maken** onder aan de pagina. Na een paar minuten is uw IoT Central-toepassing klaar voor gebruik:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central-toepassing":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Hier volgt de voorgestelde volgende stap om te blijven leren over IoT Central:

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md)

Als u een apparaatontwikkelaar bent en een duik in de code wilt nemen, is dit de voorgestelde volgende stap:
> [!div class="nextstepaction"]
> [Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing](./tutorial-connect-device.md)