---
title: Zelfstudie van digitaal distributiecentrum voor IoT | Microsoft Docs
description: Een zelfstudie van een toepassingssjabloon voor een digitaal distributiecentrum voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 40b0d7eeb77f165c50c5470e1b5869e240d36534
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702688"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Zelfstudie: Een toepassingssjabloon voor een digitaal distributiecentrum implementeren en doorlopen

In deze zelfstudie leert u hoe u aan de slag gaat door een toepassingssjabloon voor een **digitaal distributiecentrum** voor IoT Central te implementeren. U leert hoe u de sjabloon implementeert, wat er kant-en-klaar in is opgenomen, en wat u hierna kunt doen.

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Een toepassing voor een digitaal distributiecentrum maken 
> * De toepassing doorlopen 

## <a name="prerequisites"></a>Vereisten
* Er zijn geen specifieke vereisten voor het implementeren van deze app
* Een Azure-abonnement wordt aanbevolen, maar u kunt het ook zonder abonnement proberen

## <a name="create-digital-distribution-center-application-template"></a>Een toepassingssjabloon voor een digitaal distributiecentrum maken

U kunt een toepassing maken met behulp van de volgende stappen

1. Navigeer naar de website Azure IoT Central-toepassingsbeheer. Selecteer **Bouwen** in de navigatiebalk aan de linkerkant en klik vervolgens op het tabblad **Detailhandel**.

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Een toepassingssjabloon voor een digitaal distributiecentrum":::
1. Selecteer het tabblad **Detailhandel** en selecteer **App maken** onder de toepassing **Digital Distribution Center** (Digitaal distributiecentrum)

1. Met **App maken** wordt het formulier Nieuwe toepassing geopend en worden de aangevraagde gegevens ingevuld zoals hieronder wordt weergegeven.
   **Naam van de app**: u kunt de voorgestelde standaardnaam gebruiken of een beschrijvende naam invoeren voor uw app.
   **URL**: u kunt de aanbevolen standaard-URL gebruiken of een beschrijvende en unieke URL opgeven die u goed kunt onthouden. Als u al een Azure-abonnement hebt, wordt vervolgens de standaardinstelling aanbevolen. U kunt beginnen met een gratis proefabonnement van 7 dagen en ervoor kiezen om dit op elk gewenst moment om te zetten naar een Standard-prijsplan voordat het gratis proefabonnement verloopt.
   **Factureringsgegevens**: De map, het Azure-abonnement en de locatiegegevens zijn vereist voor het inrichten van de resources.
   **Maken**: Selecteer Maken onderin de pagina om uw app te implementeren.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Schermopname waarin wordt getoond hoe u een app maakt met de toepassingssjabloon voor het digitale distributiecentrum":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Schermopname met de factureringsopties wanneer u de app maakt":::

## <a name="walk-through-the-application-dashboard"></a>Het toepassingsdashboard doorlopen 

Nadat u de app-sjabloon hebt geïmplementeerd, is het standaarddashboard een portal voor operators van een distributiecentrum. Northwind Traders is een fictieve distributiecentrumoplossingsprovider die lopendebandsystemen beheert. 

In dit dashboard ziet u één gateway en één camera die fungeert als een IoT-apparaat. De gateway levert telemetrie over pakketten zoals geldig, ongeldig, onbekend en grootte, samen met gekoppelde eigenschappen van de apparaatdubbel. Alle downstreamopdrachten worden uitgevoerd op IoT-apparaten, zoals een camera. Dit dashboard is vooraf geconfigureerd om de activiteiten van het kritieke distributiecentrumapparaat weer te geven.

Het dashboard is logisch ingedeeld om de mogelijkheden voor apparaatbeheer van de Azure IoT-gateway en het IoT-apparaat weer te geven.  
   * U kunt gatewayopdracht- en besturingstaken uitvoeren
   * Alle camera's beheren die deel uitmaken van de oplossing. 

> [!div class="mx-imgBorder"]
> ![Schermopname van het dashboard van het digitale distributiecentrum](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Apparaatsjabloon

Klik op het tabblad Apparaatinstellingen om het mogelijkheidsmodel van de gateway weer te geven. Een mogelijkheidsmodel is gestructureerd rond twee verschillende interfaces: **Camera** en **Gateway voor digitale distributie**

> [!div class="mx-imgBorder"]
> ![Schermopname van de apparaatsjabloon voor de gateway voor digitale distributie in de toepassing](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Camera** - Deze interface organiseert alle mogelijkheden voor camera-specifieke opdrachten 

> [!div class="mx-imgBorder"]
> ![Schermopname van de camera-interface in apparaatsjabloon voor de gateway voor digitale distributie](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway** - In deze interface worden alle telemetriegegevens van de camera, in de cloud gedefinieerde eigenschappen van de apparaatdubbel en gatewaygegevens weergegeven.

> [!div class="mx-imgBorder"]
> ![Schermopname van de interface van de gateway voor digitale distributie in de apparaatsjabloon voor de gateway voor digitale distributie](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Gatewayopdrachten
Deze interface organiseert alle mogelijkheden van gatewayopdrachten

> [!div class="mx-imgBorder"]
> ![Schermopname van de interface van gatewayopdrachten in de apparaatsjabloon voor de gateway voor digitale distributie](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regels
Selecteer het tabblad Regels om twee verschillende regels weer te geven die voorkomen in deze toepassingssjabloon. Deze regels zijn geconfigureerd voor het verzenden van e-mailmeldingen naar de operators voor verdere onderzoeken.

 **Waarschuwing voor te veel ongeldige pakketten**: deze regel wordt geactiveerd wanneer de camera een groot aantal ongeldige pakketten in het lopendebandsysteem detecteert.
 
**Groot pakket**: deze regel wordt geactiveerd als de camera een erg groot pakket detecteert waarvan de kwaliteit niet kan worden geïnspecteerd. 

> [!div class="mx-imgBorder"]
> ![Schermopname van de lijst met regels in de toepassing voor het digitale distributiecentrum](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad Taken om vijf verschillende taken weer te geven die voorkomen in deze toepassingssjabloon: U kunt de taakfunctie gebruiken voor het uitvoeren van bewerkingen voor de hele oplossing. Hierbij wordt gebruik gemaakt van de mogelijkheden van apparaatopdrachten en dubbels om taken in het digitale distributiecentrum uit te voeren, zoals:
   * het kalibreren van de voordat de pakketdetectie wordt gestart 
   * het periodiek bijwerken van de firmware van de camera
   * het wijzigen van het telemetrie-interval om het uploaden van gegevens te beheren

> [!div class="mx-imgBorder"]
> ![Schermopname van de lijst met taken in de toepassing voor het digitale distributiecentrum](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze app niet meer wilt gebruiken, verwijdert u de app door **Beheer** > **App-instellingen** te bezoeken en te klikken op **Verwijderen**.

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien hoe u de toepassing verwijdert wanneer u deze niet meer nodig hebt](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de oplossings architectuur van Digital Distribution Center:

> [!div class="nextstepaction"]
> [concept van digitaal distributiecentrum](./architecture-digital-distribution-center.md)
