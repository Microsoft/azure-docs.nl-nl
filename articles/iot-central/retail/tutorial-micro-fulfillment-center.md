---
title: Zelfstudie voor de app-sjabloon voor micro-fulfillmentcentra | Microsoft Docs
description: Een zelfstudie over de toepassingssjabloon voor micro-fulfillmentcentra voor Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: ab4d609b1638b47ad73b6f1143adee2870577305
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390467"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Zelfstudie: Een toepassingssjabloon voor een micro-fulfillmentcentrum implementeren en doorlopen

In deze zelfstudie gebruikt u het toepassingssjabloon van het Azure IoT Central-micro-fulfillmentcentrum om een handelsoplossing te bouwen.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * De toepassings sjabloon implementeren
> * De toepassings sjabloon gebruiken

## <a name="prerequisites"></a>Vereisten
U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. U kunt eventueel een gratis proefversie van zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Een app maken 
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de rest van de zelfstudiereeks om een volledige oplossing te ontwikkelen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Ga naar de website [Azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral).
1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen tot dat abonnement. Of meld u aan met behulp van een Microsoft-account.

   ![Schermopname van het dialoogvenster voor aanmelding bij Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **Detailhandel**.  De detailhandelspagina toont verschillende toepassingssjablonen voor detailhandels.

Een nieuwe app voor het micro-fulfillmentcentrum maken die gebruikmaakt van preview-functies:  
1. Selecteer de toepassingssjabloon **Micro-fulfillmentcentrum**. Deze sjabloon bevat apparaatsjablonen voor alle apparaten die in de zelfstudie worden gebruikt. De sjabloon biedt ook een operatordashboard voor het bewaken van waarden binnen uw fulfillmentcentrum en de voorwaarden voor uw geautomatiseerde providers. 

    ![Schermopname van de pagina 'Uw IoT-app bouwen' in Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Kies desgewenst een beschrijvende **Toepassingsnaam**. De toepassingssjabloon is gebaseerd op het fictieve bedrijf Northwind Traders. 

    >[!NOTE]
    >Als u een beschrijvende toepassingsnaam gebruikt, moet u nog steeds een unieke waarde gebruiken voor de URL van de app.

1. Als u een Azure-abonnement hebt, voert u uw map, Azure-abonnement en locatie in. Als u geen abonnement hebt, kunt u een gratis proefversie van zeven dagen inschakelen en de vereiste contactgegevens aanvullen.  

    Zie de quickstart [Create an Azure IoT Central application](../core/quick-deploy-iot-central.md) (Een Azure IoT Central-toepassing maken) voor meer informatie over mappen en abonnementen.

1. Selecteer **Maken**.

    ![Schermopname van de pagina 'Nieuwe app' in Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>De app doorlopen 

Nadat de toepassingssjabloon is geïmplementeerd, ziet u het **dashboard voor het micro-fulfillmentcentrum van Northwind Traders**. Northwind Traders is een fictieve handelaar met een micro-fulfillmentcentrum dat wordt beheerd in deze Azure IoT Central-app. Op dit operatordashboard ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een set opdrachten, taken en acties die u kunt uitvoeren. Het dashboard is op een logische manier gesplitst in twee secties. Aan de linkerkant kunt u de omgevingsvoorwaarden in het fulfillmentcentrum bewaken en aan de rechterkant kunt u de status van een automatische orderpicker in de faciliteit bewaken.  

Vanuit het dashboard kunt u het volgende doen:
   * Telemetrie inzien van apparaten, zoals het aantal picks, het aantal verwerkte orders en eigenschappen zoals de status van het structuursysteem.  
   * Bekijk de plattegrond en de locatie van de geautomatiseerde orderpickers binnen het fulfillmentcentrum.
   * Activeer opdrachten, zoals het opnieuw instellen van het besturingssysteem, het bijwerken van de firmware van de provider en het opnieuw configureren van het netwerk.

     ![Schermopname van de bovenste helft van het micro-fulfillmentcentrum-dashboard van Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Bekijk een voorbeeld van het dashboard dat een operator kan gebruiken om voorwaarden binnen het fulfillmentcentrum te bewaken. 
   * Controleer de status van de nettoladingen die worden uitgevoerd op het gatewayapparaat binnen het fulfillmentcentrum.    

     ![Schermopname van de onderste helft van het micro-fulfillmentcentrum-dashboard van Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Apparaatsjabloon
Als u op het tabblad Apparaatsjablonen klikt, ziet u dat er twee verschillende typen apparaten zijn die deel uitmaken van de sjabloon: 
   * **Geautomatiseerde orderpicker**: Deze apparaatsjabloon vertegenwoordigt de definitie voor een werkende, geautomatiseerde orderpicker die is geïmplementeerd in de fulfillmentstructuur en die de juiste opslag- en ophaalbewerkingen uitvoert. Als u de sjabloon selecteert, ziet u dat de orderpicker apparaatgegevens verzendt, zoals de temperatuur, de positie op de as en eigenschappen zoals de status van de geautomatiseerde orderpicker. 
   * **Bewaking van de structuurstaat**: Deze apparaatsjabloon vertegenwoordigt een verzameling van apparaten waarmee u omgevingsvoorwaarden kunt bewaken, evenals het gatewayapparaat dat als host fungeert voor de verschillende randwerkbelastingen om uw fulfillmentcentrum te versterken. Het apparaat verzendt telemetriegegevens, zoals de temperatuur, het aantal picks en het aantal orders. Er wordt ook informatie verzonden over de status en gesteldheid van de rekenwerkbelastingen die in uw omgeving worden uitgevoerd. 

     ![Apparaatsjablonen van het micro-fulfillmentcentrum](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Als u het tabblad Apparaatgroepen selecteert, kunt u ook zien dat voor deze apparaatsjablonen automatisch apparaatgroepen worden gemaakt.

## <a name="rules"></a>Regels
Op het tabblad **Regels** ziet u een voorbeeldregel in de toepassingssjabloon om de temperatuurvoorwaarden voor de automatische orderpicker te bewaken. U kunt deze regel gebruiken om de operator te waarschuwen als een specifieke orderpicker in de faciliteit oververhit raakt en offline moet worden gehaald voor onderhoud. 

Gebruik de voorbeeldregel als inspiratie om regels te definiëren die geschikter zijn voor uw bedrijfsfuncties.

![Schermopname van het tabblad Regels](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet langer gaat gebruiken, verwijder dan de toepassingssjabloon. Ga naar **Beheer** > **App-instellingen** en selecteer **Verwijderen**.

![Schermopname van de pagina Toepassingsinstellingen van het micro-fulfillmentcentrum](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

> [!div class="nextstepaction"]
> [Oplossingsarchitectuur voor het micro-fulfillmentcentrum](./architecture-micro-fulfillment-center.md)
