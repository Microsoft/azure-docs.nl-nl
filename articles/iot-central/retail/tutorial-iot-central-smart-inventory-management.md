---
title: Zelfstudie van slim voorraadbeheer voor IoT | Microsoft Docs
description: Een zelfstudie van een toepassingssjabloon voor slim voorraadbeheer voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 4bade4cfe5bf82aefcc967d86362e27605a005f3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832178"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Zelfstudie: Een toepassingssjabloon voor slim voorraadbeheer implementeren en doorlopen

In deze zelfstudie leert u hoe u aan de slag gaat door een toepassingssjabloon voor **slim voorraadbeheer** voor IoT Central te implementeren. U leert hoe u de sjabloon implementeert, wat er kant-en-klaar in is opgenomen, en wat u hierna kunt doen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> een Smart Inventory Management-toepassing maken door de toepassing te laten lopen 

## <a name="prerequisites"></a>Vereisten

* Er zijn geen specifieke vereisten voor het implementeren van deze app
* Een Azure-abonnement wordt aanbevolen, maar u kunt het ook zonder abonnement proberen

## <a name="create-smart-inventory-management-application-template"></a>Sjabloon voor het maken van een toepassing voor slim voorraadbeheer

U kunt een toepassing maken met behulp van de volgende stappen

1. Navigeer naar de website Azure IoT Central-toepassingsbeheer. Selecteer **Bouwen** in de navigatiebalk aan de linkerkant en klik vervolgens op het tabblad **Detailhandel**.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Schermopname waarin wordt getoond hoe u de toepassingssjabloon voor slim voorraadbeheer":::

2. Selecteer het tabblad **Detailhandel** en selecteer **App maken** onder **slim voorraadbeheer**

3. Met **App maken** wordt het formulier Nieuwe toepassing geopend en worden de aangevraagde gegevens ingevuld zoals hieronder wordt weergegeven.
    **Naam van de app**: u kunt de voorgestelde standaardnaam gebruiken of een beschrijvende naam invoeren voor uw app.
    **URL**: u kunt de aanbevolen standaard-URL gebruiken of een beschrijvende en unieke URL opgeven die u goed kunt onthouden. Als u al een Azure-abonnement hebt, wordt vervolgens de standaardinstelling aanbevolen. U kunt beginnen met een gratis proefabonnement van 7 dagen en ervoor kiezen om dit op elk gewenst moment om te zetten naar een Standard-prijsplan voordat het gratis proefabonnement verloopt.
    **Factureringsgegevens**: De map, het Azure-abonnement en de locatiegegevens zijn vereist voor het inrichten van de resources.
    **Maken**: Selecteer Maken onderin de pagina om uw app te implementeren.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Schermopname waarin wordt getoond hoe u een app maakt op basis van de toepassingssjabloon voor slim voorraadbeheer":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Schermopname met de factureringsopties wanneer u de app maakt":::

## <a name="walk-through-the-application"></a>De toepassing doorlopen 

### <a name="dashboard"></a>Dashboard 

Nadat u de app-sjabloon hebt geïmplementeerd, is het standaarddashboard een portal voor operators van slim voorraadbeheer. Northwind Traders is een fictieve provider van slim voorraadbeheer die een warehouse beheert met behulp van BLE (Bluetooth Low Energy) en een winkel met RFID (Radio-Frequency Identification). In dit dashboard ziet u twee verschillende gateways die telemetrische gegevens over voorraadbeheer leveren, samen met de bijbehorende opdrachten, taken en acties die u kunt uitvoeren. Dit dashboard is vooraf geconfigureerd om de activiteiten van het kritieke slim-voorraadbeheerapparaat weer te geven.
Het dashboard is logisch onderverdeel in twee verschillende gatewayapparaatbeheerbewerkingen: 
   * Het warehouse is geïmplementeerd met een vaste BLE-gateway en BLE-tags op pallets om de voorraad in een grotere faciliteit te kunnen bijhouden en volgen
   * De winkel is geïmplementeerd met een vaste RFID-gateway en RFID-tags op afzonderlijke artikelen om de voorraad in een winkel te kunnen bijhouden en volgen
   * De locatie, status en gerelateerde details van de gateway weergeven 

> [!div class="mx-imgBorder"]
> ![Schermopname van de bovenste helft van het dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * U kunt het totale aantal gateways, actieve tags en onbekende tags eenvoudig volgen.
   * U kunt bewerkingen voor apparaatbeheer uitvoeren, zoals: firmware bijwerken, sensoren uitschakelen en inschakelen, een sensordrempel bijwerken, telemetrie-intervallen bijwerken en servicecontracten bijwerken
   * Gatewayapparaten kunnen voorraadbeheer op aanvraag uitvoeren met een volledige of incrementele scan.

> [!div class="mx-imgBorder"]
> ![Schermopname van de onderste helft van het dashboard voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad Apparaatinstellingen om het mogelijkheidsmodel van de gateway weer te geven. Een mogelijkheidsprofiel is gestructureerd rond twee verschillende interfaces: **Gatewaytelemetrie en -eigenschap** en **Gatewayopdrachten**

**Gatewaytelemetrie en -eigenschap**: deze interface vertegenwoordigt alle telemetrie die betrekking heeft op de sensoren, locaties, apparaatgegevens en de mogelijkheid voor eigenschappen van apparaatdubbels, zoals gatewaydrempels en update-intervallen.

> [!div class="mx-imgBorder"]
> ![Schermopname van de apparaatsjabloon voor de gateway voor voorraadbeheer in de toepassing](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway-opdrachten** - Deze interface organiseert alle mogelijkheden van de gatewayopdracht

> [!div class="mx-imgBorder"]
> ![Schermopname van de interface van gatewayopdrachten in de apparaatsjabloon voor de gateway voor voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regels
Selecteer het tabblad Regels om twee verschillende regels weer te geven die voorkomen in deze toepassingssjabloon. Deze regels zijn geconfigureerd voor het verzenden van e-mailmeldingen naar de operators voor verdere onderzoeken.

**Gateway offline**: Deze regel wordt geactiveerd als de gateway voor langere tijd niet rapporteert aan de cloud. De gateway reageert mogelijk niet vanwege een laag accuniveau, een storing in de connectiviteit of een beschadiging aan het apparaat.

**Onbekende tags**: Het is van essentieel belang dat alle RFID- en BLE-tags die zijn gekoppeld aan assets, kan worden gevolgd. Als de gateway te veel onbekende tags detecteert, is dit een indicatie van synchronisatieproblemen met tagbrontoepassingen.

> [!div class="mx-imgBorder"]
> ![Schermopname van de lijst met regels in de toepassing voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Taken
Selecteer het tabblad Taken om vijf verschillende taken weer te geven die voorkomen in deze toepassingssjabloon: U kunt taken gebruiken voor het uitvoeren van bewerkingen voor de hele oplossing. Hierbij wordt gebruik gemaakt van de mogelijkheden van apparaatopdrachten en dubbels om voorraadbeheertaken uit te voeren, zoals:
   * lezers uitschakelen op alle gateways
   * de telemetriedrempelwaarde wijzigen tussen 
   * voorraadbeheerscans op aanvraag uitvoeren voor de hele oplossing.

> [!div class="mx-imgBorder"]
> ![Schermopname van de lijst met taken in de toepassing voor slim voorraadbeheer](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet meer wilt gebruiken, verwijdert u de app door **Beheer** > **App-instellingen** te bezoeken en te klikken op **Verwijderen**.

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien hoe u de toepassing verwijdert wanneer u deze niet meer nodig hebt](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Smart Inventory Management:

> [!div class="nextstepaction"]
> [Concept van slim voorraadbeheer](./architecture-smart-inventory-management.md)
