---
title: 'Zelfstudie: Een app maken met IoT Central om de werking van zonnepanelen bij te houden'
description: 'Zelfstudie: Lees hier hoe u met behulp van toepassingssjablonen van Azure IoT Central een app kunt maken om de werking van zonnepanelen bij te houden.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d006bae8290c1f6ea564f9985727f4d6ff70bb04
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516745"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Zelfstudie: Een app maken met IoT Central om de werking van zonnepanelen bij te houden 

In deze zelfstudie gaat u een app maken om de werking van zonnepanelen bij te houden. U maakt ook een apparaatmodel dat gesimuleerde gegevens bevat. In deze zelfstudie leert u:


> [!div class="checklist"]
> * Gratis een app maken om de werking van zonnepanelen bij te houden
> * Doornemen van toepassing
> * Resources opschonen


Als u geen abonnement hebt, kunt u een [gratis proefaccount](https://azure.microsoft.com/free) maken.

## <a name="prerequisites"></a>Vereisten
* Geen
* Een Azure-abonnement wordt aanbevolen, maar is niet vereist voor de zelfstudie


## <a name="create-a-solar-panel-monitoring-app"></a>Een app maken om zonnepanelen te bewaken 

U kunt deze toepassing in drie eenvoudige stappen maken:

1. Open de [startpagina van Azure IoT Central](https://apps.azureiotcentral.com) en klik op **Bouwen** om een nieuwe toepassing te maken. 

1. Selecteer het tabblad **Energie** en klik op **App maken** onder de toepassingstegel **Bewaking van zonnepanelen**. 

    > [!div class="mx-imgBorder"]
    > ![App maken](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Met **App maken** wordt het formulier **Nieuwe toepassing** geopend. Vul de gevraagde gegevens in, zoals wordt weergegeven in de afbeelding hieronder:
    * **Toepassingsnaam**: Kies een naam voor uw IoT Central-toepassing. 
    * **URL**: Kies een IoT Central-URL, het platform controleert de uniekheid van de URL.
    * **Gratis**: Als u al een Azure-abonnement hebt, wordt de standaardinstelling aanbevolen. Als u nog geen Azure-abonnement hebt, maakt u een gratis proefaccount.
    * **Factureringsgegevens**: De toepassing zelf is gratis. De informatie over de map, het Azure-abonnement en de locatie is vereist voor het inrichten van de resources voor uw app.
    * Klik onder aan de pagina op de knop **Maken** en de app wordt binnen een minuut of zo gemaakt.
        ![Formulier voor nieuwe toepassing](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Factureringsgegevens in formulier voor nieuwe toepassing](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Toepassing en gesimuleerde gegevens controleren

De zojuist gemaakte app voor het bijhouden van de werking van zonnepanelen is uw eigendom en u kunt deze app dus op elk gewenst moment wijzigen. Laten we controleren of de app is geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Als u wilt controleren of de app is gemaakt en de gegevens zijn gesimuleerd, gaat u naar het **dashboard**. Als u de tegels ziet met enkele gegevens, is de implementatie van uw app gelukt. De gegevenssimulatie duurt meestal 1-2 minuten. 

## <a name="application-walk-through"></a>Doornemen van toepassing
Nadat u de app-sjabloon hebt geïmplementeerd, ziet u voorbeeldgegevens van een slimme meter, het model apparaat en het dashboard.

Adatum is een fictief energiebedrijf dat zonnepanelen bewaakt en beheert. In het dashboard van de app ziet u de eigenschappen van het zonnepaneel, gegevens en voorbeeldopdrachten. Hiermee kunnen operators en ondersteuningsteams proactief de volgende activiteiten uitvoeren voordat het ondersteuningsincidenten worden:
* De meest recente gegevens van het paneel en de installatielocatie op de kaart bekijken
* Proactief de status van het paneel en de verbindingsstatus controleren
* De trends voor het genereren van energie en van temperatuur bekijken om afwijkende patronen vast te stellen
* De totaal gegenereerde hoeveelheid energie bijhouden voor planning en facturering
* Bewerkingen starten en beheren zoals paneel activeren en firmwareversie bijwerken. In de sjabloon tonen de opdrachtknoppen mogelijke functionaliteiten maar worden er geen echte opdrachten verzonden.

> [!div class="mx-imgBorder"]
> ![Dashboard voor bewaking van zonnepanelen](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Apparaten
De app wordt geleverd met een voorbeeld van een zonnepaneel. U kunt de details van het paneel bekijken door te klikken op het tabblad **Apparaten**.

> [!div class="mx-imgBorder"]
> ![Bijgeleverd zonnepaneel](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klik op de koppeling van het voorbeeldapparaat **SP0123456789** om de details van het apparaat weer te geven. Op de pagina **Eigenschappen bijwerken** kunt u de aanpasbare eigenschappen van het apparaat bijwerken en de bijgewerkte waarden visualiseren in het dashboard. 

> [!div class="mx-imgBorder"]
> ![Eigenschappen van zonnepaneel](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad **Apparaatsjablonen** om het apparaatmodel van het zonnepaneel weer te geven. Het model heeft een vooraf gedefinieerde interface voor Data, Property, Commands en Views.

> [!div class="mx-imgBorder"]
> ![Apparaatsjabloon voor zonnepaneel](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet te blijven gebruiken, verwijdert u de toepassing met de volgende stappen:

1. Open het tabblad Beheer aan de linkerkant.
1. Selecteer Uw toepassing en klik onder aan de pagina op de knop Verwijderen. 

    > [!div class="mx-imgBorder"]
    > ![Toepassing verwijderen](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Volgende stappen
* Lees meer over de architectuur van uw zonnepaneel-app 
> [!div class="nextstepaction"]
> [het conceptartikel](./concept-iot-central-solar-panel-app.md)
* Maak gratis sjablonen voor de app voor zonnepanelen: [app voor bewaking van zonnepaneel](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Zie [Overzicht van IoT Central](../index.yml) voor meer informatie over IoT Central
