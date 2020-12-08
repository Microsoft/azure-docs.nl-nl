---
title: Snelstart - Een Azure IoT Central-app maken | Microsoft Docs
description: Snelstart - Een nieuwe Azure IoT Central-app maken. Maak de toepassing met behulp van het gratis abonnement of een van de standaardabonnementen.
author: viv-liu
ms.author: viviali
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a156150c2cd28964658fa8529181a70ef546b78f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457314"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Snelstart - Een Azure IoT Central-app maken

In deze quickstart ziet u hoe u een Azure IoT Central-toepassing maakt.

## <a name="create-an-application"></a>Een app maken

Ga naar de website voor het [bouwen van Azure IoT Central-oplossingen](https://aka.ms/iotcentral). Meld u aan met een persoonlijk account of werk- of schoolaccount van Microsoft.

U kunt een nieuwe toepassing maken op basis van een sjabloon uit de lijst met branchespecifieke IoT Central-sjablonen om snel aan de slag te gaan, of een volledig nieuwe toepassing maken op basis van de sjabloon **Aangepaste apps**. In deze quickstart gebruikt u de sjabloon **Aangepaste toepassing**.

Ga als volgt te werk om een nieuwe Azure IoT Central-toepassing wilt maken op basis van de sjabloon **Aangepaste toepassing**:

1. Ga naar de pagina **Bouwen**:

    ![Pagina voor het bouwen van uw IoT-toepassing](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Kies **Aangepaste apps** en zorg ervoor dat de sjabloon **Aangepaste toepassing** is geselecteerd.

1. In Azure IoT Central wordt automatisch een **toepassingsnaam** voorgesteld op basis van de toepassingssjabloon die u hebt geselecteerd. U kunt deze naam gebruiken of uw eigen beschrijvende toepassingsnaam invoeren.

1. Azure IoT Central genereert ook een uniek voorvoegsel voor de **toepassings-URL** voor u op basis van de naam van de toepassing. U gebruikt deze URL voor de toegang tot uw toepassing. Wijzig dit URL-voorvoegsel in iets dat gemakkelijker te onthouden is als u dat wilt.

    ![Pagina Een toepassing maken van Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Factureringsgegevens van Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Als u op de vorige pagina **Aangepaste app** hebt gekozen, ziet u een vervolgkeuzelijst voor een **toepassingssjabloon**. In de vervolgkeuzelijst kunnen andere sjablonen worden weergegeven die voor u beschikbaar zijn gesteld door uw organisatie.

    >[!IMPORTANT]
    >De sjabloon **Aangepaste toepassing (verouderd)** (V2) is buiten gebruik gesteld, omdat alle mogelijkheden die eerder beschikbaar waren in de oude toepassingssjabloon, nu beschikbaar zijn in de nieuwe sjabloon **Aangepaste toepassing** (V3).

1. Maak de toepassing met behulp van het gratis 7-daagse abonnement of een van de standaardabonnementen:

    - Toepassingen die u maakt met het *gratis* abonnement zijn gratis gedurende zeven dagen en ondersteunen maximaal vijf apparaten. U kunt ze op enig moment voor het aflopen van het abonnement overzetten naar een betaald abonnement.
    - Toepassingen die u maakt met een *standaard*-abonnement worden gefactureerd per apparaat. U kunt kiezen tussen **Standard 0**, **Standard 1** en **Standard 2**, waarbij de eerste twee apparaten gratis zijn. Meer informatie over de gratis en standaardabonnementen vindt u op de [pagina met prijzen voor Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Als u een toepassing maakt met behulp van een standaardabonnement, moet u uw *Directory*, *Azure-abonnement* en *locatie* opgeven:
        - De *directory* is de Azure Active Directory waarin uw toepassing wordt gemaakt. Een Azure Active Directory bevat gebruikers-id's, referenties en andere organisatiegegevens. Als u geen Azure Active Directory hebt, wordt er een voor u gemaakt op het moment dat u een Azure-abonnement maakt.
        - Als u een *Azure-abonnement* hebt, kunt u instanties van Azure-services maken. IoT Central zorgt ervoor dat resources in uw abonnement worden ingericht. Als u geen Azure-abonnement hebt, kunt u er gratis een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription). Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Nieuwe toepassing**. Uw nieuwe abonnement wordt nu weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.
        - De *locatie* is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Gewoonlijk kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om de beste prestaties te verkrijgen. Als u eenmaal een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

1. Controleer de voorwaarden en selecteer **Maken** onder aan de pagina. Na een paar minuten is uw IoT Central-toepassing klaar voor gebruik:

    ![Azure IoT Central-toepassing](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een IoT Central-toepassing gemaakt. Hier volgt de voorgestelde volgende stap om te blijven leren over IoT Central:

> [!div class="nextstepaction"]
> [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md)

Als u een apparaatontwikkelaar bent en een duik in de code wilt nemen, is dit de voorgestelde volgende stap:
> [!div class="nextstepaction"]
> [Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing](./tutorial-connect-device.md)