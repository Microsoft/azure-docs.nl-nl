---
title: Zelfstudie over IoT Connected-logistiek | Microsoft Docs
description: Een zelfstudie over een verbonden toepassingssjabloon voor logistiek voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831617"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Zelfstudie: Een toepassingssjabloon voor verbonden logistiek implementeren en doorlopen

In deze zelfstudie leert u hoe u aan de slag gaat met de IoT Central-toepassingssjabloon voor *verbonden logistiek*. U leert hoe u de sjabloon implementeert en gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een app maken voor verbonden logistiek.
> * De belangrijkste functies in de app gebruiken.
> * Het dashboard gebruiken om de belangrijkste activiteiten van het logistiekapparaat weer te geven.
> * De apparaatsjabloon gebruiken
> * Regels volgen
> * Taken gebruiken

## <a name="prerequisites"></a>Vereisten

* Er zijn geen specifieke vereisten voor het implementeren van deze app.
* U kunt het gratis prijsplan gebruiken of een Azure-abonnement gebruiken.

## <a name="create-connected-logistics-application"></a>Een app maken voor verbonden logistiek

Voer de volgende stappen uit om de app te maken:

1. Ga naar de website voor het [bouwen van Azure IoT Central-oplossingen](https://aka.ms/iotcentral). Meld u aan met een persoonlijk account of werk- of schoolaccount van Microsoft. Selecteer **Bouwen** in de navigatiebalk aan de linkerkant en klik vervolgens op het tabblad **Detailhandel**:

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Sjabloon voor verbonden logistiek":::

1. Selecteer **App maken** onder **App voor verbonden logistiek**.

1. Met **App maken** wordt het formulier **Nieuwe app** geopend. Voer de volgende details in:


    * **Naam van de app**: u kunt de voorgestelde standaardnaam gebruiken of een beschrijvende naam invoeren voor uw app.
    * **URL**: u kunt de aanbevolen standaard-URL gebruiken of een beschrijvende en unieke URL opgeven die u goed kunt onthouden. Als u al een Azure-abonnement hebt, wordt vervolgens de standaardinstelling aanbevolen. U kunt beginnen met een gratis proefabonnement van 7 dagen en ervoor kiezen om dit op elk gewenst moment om te zetten naar een Standard-prijsplan voordat het gratis proefabonnement verloopt.
    * **Factureringsgegevens**: De map, het Azure-abonnement en de locatiegegevens zijn vereist voor het inrichten van de resources.
    * **Maken**: Selecteer Maken onderin de pagina om uw app te implementeren.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Toepassingssjabloon voor verbonden logistiek":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Factureringsinformatie over verbonden logistiek":::

## <a name="walk-through-the-application"></a>De app doorlopen

Hieronder ziet u de schermopname waarin wordt getoond hoe u de toepassingssjabloon voor verbonden logistiek selecteert.

> [!div class="mx-imgBorder"]
> ![Schermopname waarin wordt getoond hoe u de toepassingssjabloon voor verbonden logistiek selecteert](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

In de volgende secties vindt u een overzicht van de belangrijkste functies van de app.

### <a name="dashboard"></a>Dashboard

Na de implementatie van de toepassingssjabloon is uw standaarddashboard een portal die zich richt op operators van verbonden logistiek. Northwind Trader is een fictieve logistiekprovider die een vrachtvloot op zee en aan wal beheert. In dit dashboard ziet u twee verschillende gateways die telemetrie van zendingen bieden, samen met de bijbehorende opdrachten, taken en acties.

> [!div class="mx-imgBorder"]
> ![Schermopname waarin wordt getoond hoe u een app maakt met de toepassingssjabloon voor verbonden logistiek](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Schermopname met de factureringsopties wanneer u de app maakt](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Dit dashboard is vooraf geconfigureerd om de activiteiten van het kritieke logistiekapparaat weer te geven.

Met het dashboard kunnen twee verschillende beheerbewerkingen voor gatewayapparaten worden uitgevoerd:

* De logistiekroutes voor vrachtverzendingen en de locatiegegevens van zeeverzendingen bekijken.
* De status van de gateway en andere relevante informatie bekijken.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Dashboard voor verbonden logistiek":::

* U kunt het totale aantal gateways, actieve tags en onbekende tags volgen.
* U kunt bewerkingen voor apparaatbeheer uitvoeren, zoals: firmware bijwerken, sensoren uitschakelen en inschakelen, een sensordrempel bijwerken, telemetrie-intervallen bijwerken en servicecontracten bijwerken.
* Het accuverbruik van het apparaat weergeven.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Dashboardstatus voor verbonden logistiek":::

#### <a name="device-template"></a>Apparaatsjabloon

Selecteer **Apparaatsjablonen** om het model van de gatewaymogelijkheden weer te geven. Een mogelijkheidsprofiel is gestructureerd rond de interfaces van **Gatewaytelemetrie en -eigenschap** en **Gatewayopdrachten**.

**Gatewaytelemetrie en -eigenschap** - Deze interface definieert alle telemetrie met betrekking tot sensoren, locatie en apparaatgegevens. De interface definieert ook de dubbele eigenschapsmogelijkheden van het apparaat, zoals drempelwaarden voor de sensor en update-intervallen.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Interface van telemetrie en eigenschap":::

**Gateway-opdrachten** - Deze interface organiseert alle mogelijkheden van de gatewayopdracht:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Interface voor gatewayopdrachten":::

### <a name="rules"></a>Regels

Selecteer het tabblad **Regels** in deze toepassingssjabloon. Deze regels zijn geconfigureerd zodat ze e-mailmelding sturen naar de operators voor verdere onderzoeken:

**Waarschuwing voor gatewaydiefstal**: Deze regel wordt geactiveerd wanneer er tijdens de rit onverwacht licht door de sensoren wordt gedetecteerd. Operators moeten onmiddellijk worden gewaarschuwd om mogelijke diefstal te onderzoeken.

**Niet-reagerende gateway**: Deze regel wordt geactiveerd als de gateway voor langere tijd niet rapporteert aan de cloud. De gateway reageert mogelijk niet vanwege een laag accuniveau, een storing in de connectiviteit of een beschadiging aan het apparaat.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Regeldefinities":::

### <a name="jobs"></a>Taken

Selecteer het tabblad **Taken** om de taken in deze app weer te geven:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Uit te voeren taken":::

U kunt taken gebruiken om bewerkingen op de hele app uit te voeren. De taken in deze toepassing maken gebruik van apparaatopdrachten en dubbele mogelijkheden om taken uit te voeren, zoals het uitschakelen van specifieke sensoren in alle gateways of het wijzigen van de drempelwaarde voor de sensor, afhankelijk van de verzendmodus en route:

* Het is een standaardbewerking om schokkende sensoren uit te schakelen tijdens een zeeverzending of de temperatuurdrempel te verlagen tijdens koelketenvervoer.

* Met taken kunt u bewerkingen uitvoeren op het hele systeem, zoals het bijwerken van firmware op de gateways of het bijwerken van het servicecontract om op de hoogte te blijven van onderhoudsactiviteiten.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet meer wilt gebruiken, verwijdert u de app door **Beheer** > **App-instellingen** te bezoeken en te klikken op **Verwijderen**.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Sjabloon opschonen":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

> [!div class="nextstepaction"]
> [Concepten van verbonden logistiek](./architecture-connected-logistics.md)
