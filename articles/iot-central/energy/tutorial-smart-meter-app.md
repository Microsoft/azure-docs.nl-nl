---
title: 'Zelfstudie: Een app voor analyse van slimme meters maken met IoT Central'
description: 'Zelfstudie: Lees hier hoe u met behulp van toepassingssjablonen van Azure IoT Central een app kunt maken om slimme meters te bewaken.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a95f1befcdc9b59293eb97330ba7ceae3034a74b
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516766"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Zelfstudie: Een app-sjabloon maken en leren kennen om de werking van slimme meters te bewaken 

In deze zelfstudie gaat u een app maken om de werking van slimme meters te bewaken. U maakt ook een apparaatmodel dat gesimuleerde gegevens bevat. In deze zelfstudie leert u:

> [!div class="checklist"]
> * De app voor slimme meters gratis maken
> * Doornemen van toepassing
> * Resources opschonen


Als u geen abonnement hebt, kunt u een [gratis proefaccount](https://azure.microsoft.com/free) maken.

## <a name="prerequisites"></a>Vereisten
- Geen
- Een Azure-abonnement wordt aanbevolen, maar is niet vereist voor de zelfstudie

## <a name="create-a-smart-meter-monitoring-app"></a>Een app maken om slimme meters te bewaken 

U kunt deze toepassing in drie eenvoudige stappen maken:

1. Open de [startpagina van Azure IoT Central](https://apps.azureiotcentral.com) en klik op **Bouwen** om een nieuwe toepassing te maken. 
1. Selecteer het tabblad **Energie** en klik op **App maken** onder de toepassingstegel **Bewaken van slimme meters**.

    > [!div class="mx-imgBorder"]
    > ![App maken](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. Met **App maken** wordt het formulier **Nieuwe toepassing** geopend. Vul de gevraagde gegevens in, zoals wordt weergegeven in de afbeelding hieronder:
    * **Toepassingsnaam**: Kies een naam voor uw IoT Central-toepassing. 
    * **URL**: Kies een IoT Central-URL, het platform controleert de uniekheid van de URL.
    * **Gratis**: Als u al een Azure-abonnement hebt, wordt de standaardinstelling aanbevolen. Als u nog geen Azure-abonnement hebt, maakt u een gratis proefaccount.
    * **Factureringsgegevens**: De toepassing zelf is gratis. De informatie over de map, het Azure-abonnement en de locatie is vereist voor het inrichten van de resources voor uw app.
    * Klik onder aan de pagina op de knop **Maken** en de app wordt binnen een minuut of zo gemaakt.

        ![Formulier voor een nieuwe toepassing](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Factureringsgegevens in formulier voor nieuwe toepassing](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Toepassing en gesimuleerde gegevens controleren

De zojuist gemaakte app voor het bewaken van de werking van slimme meters is uw eigendom en u kunt deze app dus op elk gewenst moment wijzigen. Laten we controleren of de app is geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Als u wilt controleren of de app is gemaakt en de gegevens zijn gesimuleerd, gaat u naar het **dashboard**. Als u de tegels ziet met enkele gegevens, is de implementatie van uw app gelukt. De gegevenssimulatie duurt meestal 1-2 minuten. 

## <a name="application-walk-through"></a>Doornemen van toepassing
Nadat u de app-sjabloon hebt geïmplementeerd, beschikt u over en voorbeeld van een slimme meter, een apparaatmodel en een dashboard. 

Adatum is een fictief energiebedrijf dat slimme meters bewaakt en beheert. In het dashboard van de slimme meter ziet u de eigenschappen van slimme meter, gegevens en voorbeeldopdrachten. Hiermee kunnen operators en ondersteuningsteams proactief de volgende activiteiten uitvoeren voordat het ondersteuningsincidenten worden: 
* De meest recente gegevens van de meter en de installatielocatie op de kaart bekijken
* Proactief de status van het meternetwerk en de verbindingsstatus controleren 
* Minimale en maximale spanningswaarden bewaken voor de netwerkstatus 
* De trends voor energie, vermogen en spanning bekijken om afwijkende patronen vast te stellen 
* Het totale energieverbruik volgen voor planning en facturering
* Instructies geven en bedienen, zoals het opnieuw aansluiten van de meter en het bijwerken van de firmwareversie. In de sjabloon tonen de opdrachtknoppen mogelijke functionaliteiten maar worden er geen echte opdrachten verzonden. 

> [!div class="mx-imgBorder"]
> ![Dashboard om slimme meters te bewaken](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Apparaten
De app wordt geleverd met een voorbeeld van een slimme meter. U kunt de details van het paneel bekijken door te klikken op het tabblad **Apparaten**.

> [!div class="mx-imgBorder"]
> ![Slimme meters](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Klik op de koppeling van het voorbeeldapparaat **SM0123456789** om de details van het apparaat weer te geven. Op de pagina **Eigenschappen bijwerken** kunt u de aanpasbare eigenschappen van het apparaat bijwerken en de bijgewerkte waarden visualiseren op het dashboard.

> [!div class="mx-imgBorder"]
> ![Eigenschappen van slimme meters](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad **Apparaatsjablonen** om het apparaatmodel van de slimme meter weer te geven. Het model heeft een vooraf gedefinieerde interface voor Data, Property, Commands en Views.

> [!div class="mx-imgBorder"]
> ![Apparaatsjablonen voor slimme meters](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet te blijven gebruiken, verwijdert u de toepassing met de volgende stappen:

1. Open het tabblad Beheer aan de linkerkant.
1. Selecteer Uw toepassing en klik onder aan de pagina op de knop Verwijderen. 

    > [!div class="mx-imgBorder"]
    > ![Toepassing verwijderen](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Volgende stappen

Lees meer over de architectuur van de app voor slimme meters in 
> [!div class="nextstepaction"]
> [het conceptartikel](./concept-iot-central-smart-meter-app.md)
* Maak gratis toepassingssjablonen voor slimme meters: [slimme meter-app](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Zie [Overzicht van IoT Central](../index.yml) voor meer informatie over IoT Central
