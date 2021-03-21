---
title: 'Zelfstudie: Een app maken met IoT Central om zonnepanelen te bewaken'
description: 'Zelfstudie: Ontdek hoe u met behulp van toepassingssjablonen van Azure IoT Central een zonnepanelen-app kunt maken.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831787"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Zelfstudie: Een toepassingssjabloon voor de app om zonnepanelen te bewaken maken en verkennen 

In deze zelfstudie gaat u een app maken om de werking van zonnepanelen bij te houden. U maakt ook een apparaatmodel dat gesimuleerde gegevens bevat. In deze zelfstudie leert u het volgende:


> [!div class="checklist"]
> * Een gratis zonnepaneel-app maken
> * De toepassing doorlopen
> * Resources opschonen


Als u geen abonnement hebt, kunt u een [Gratis proefaccount maken](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

Er zijn geen vereisten voor het voltooien van deze zelfstudie. Een Azure-abonnement wordt aanbevolen, maar is niet vereist.


## <a name="create-a-solar-panel-monitoring-app"></a>Een app maken om zonnepanelen te bewaken 

U kunt deze toepassing in drie eenvoudige stappen maken:

1. Ga naar [Azure IoT Central](https://apps.azureiotcentral.com). Als u een app wilt maken, selecteert u **Bouwen**. 

1. Selecteer het tabblad **Energie**. Selecteer **App maken** onder **Bewaking van zonnepanelen**. 

    > [!div class="mx-imgBorder"]
    > ![Schermopname van de bouwopties in Azure IoT Central.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Vul in het dialoogvenster **Nieuwe toepassing** de gevraagde gegevens in en selecteer vervolgens **Maken**:
    * **Toepassingsnaam**: Kies een naam voor uw Azure IoT Central-toepassing. 
    * **URL**: Kies een Azure IoT Central-URL. Het platform controleert zijn uniekheid.
    * **Prijsplan**: Als u al een Azure-abonnement hebt, wordt de standaardinstelling aanbevolen. Als u nog geen Azure-abonnement hebt, kunt u starten met een gratis proefversie.
    * **Factureringsgegevens**: De toepassing zelf is gratis. De informatie over de map, het Azure-abonnement en de locatiegegevens zijn vereist voor het inrichten van de resources voor uw app.
        ![Schermopname van 'Nieuwe toepassing'.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Schermopname van factureringsgegevens.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Toepassing en gesimuleerde gegevens controleren

U kunt uw nieuwe toepassing voor het zonnepaneel op elk gewenst moment wijzigen. Laten we voor nu controleren of de app is geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Als u wilt controleren of de app is gemaakt en de gegevens zijn gesimuleerd, gaat u naar het **dashboard**. Als u de tegels ziet met enkele gegevens, is de implementatie van uw app gelukt. Het kost de gegevenssimulatie enkele minuten om de gegevens te genereren. 

## <a name="application-walk-through"></a>Doornemen van toepassing
Nadat u de toepassingssjabloon succesvol hebt geïmplementeerd, kunt u de app verder verkennen. U ziet dat het een voorbeeld bevat van een apparaat met slimme meter, een apparaatmodel en een dashboard.

Adatum is een fictief energiebedrijf dat zonnepanelen bewaakt en beheert. In het dashboard van de app ziet u de eigenschappen van het zonnepaneel, gegevens en voorbeeldopdrachten. Met dit dashboard kunt u, of kan uw ondersteuningsteam, de volgende activiteiten proactief uitvoeren voordat er problemen extra ondersteuning vereisen:
* Bekijk de meest recente gegevens van het paneel en bekijk de installatielocatie op de kaart.
* Controleer de status van het paneel en de verbindingsstatus.
* Bekijk de trends voor het opwekken van energie en van temperatuur om afwijkende patronen vast te stellen.
* Houd de totale hoeveelheid van opgewekte energie bij voor planning en facturering.
* Activeer een deelvenster en werk de versie van de firmware zo nodig bij. In de sjabloon tonen de opdrachtknoppen mogelijke functionaliteiten, maar er worden geen echte opdrachten verzonden.

> [!div class="mx-imgBorder"]
> ![Schermopname van dashboardsjabloon voor zonnepaneelbewaking.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Apparaten
De app wordt geleverd met een voorbeeld van een zonnepaneel. Selecteer **Apparaten** om apparaatdetails te zien.

> [!div class="mx-imgBorder"]
> ![Schermopname van apparaatsjablonen voor zonnepaneelbewaking.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Selecteer het voorbeeldapparaat **SP0123456789**. Op de tab **Eigenschappen bijwerken** kunt u de schrijfbare eigenschappen van het apparaat bijwerken. De bijgewerkte waarden kunt u visualiseren op het dashboard. 

> [!div class="mx-imgBorder"]
> ![Schermopname van het tabblad 'Eigenschappen bijwerken' binnen het sjabloonbeheer voor zonnepaneelbewaking.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad **Apparaatsjablonen** om het apparaatmodel van het zonnepaneel weer te geven. Het model heeft een vooraf gedefinieerde interface voor gegevens, eigenschappen, opdrachten en weergaven.

> [!div class="mx-imgBorder"]
> ![Schermopname van apparaatsjabloon voor zonnepaneelbewaking.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet te blijven gebruiken, verwijdert u de toepassing met de volgende stappen:

1. Selecteer **Administratie** in het linkerdeelvenster.
1. Selecteer **Toepassingsinstellingen** > **Verwijderen**. 

    > [!div class="mx-imgBorder"]
    > ![Schermopname van sjabloonbeheer voor zonnepaneelbewaking.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Volgende stappen
 
> [!div class="nextstepaction"]
> [Azure IoT Central: architectuur van de zonnepaneel-app](./concept-iot-central-solar-panel-app.md)

