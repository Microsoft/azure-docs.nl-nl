---
title: Zelfstudie - Het operatordashboard van Azure IoT Central aanpassen
description: In deze zelfstudie leert u hoe u het operatordashboard kunt aanpassen in een IoT Central-toepassing en hoe u apparaten beheert.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 5165f7d6e16e0d136dbf8d4507090c78372185cb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348047"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Zelfstudie:  Het operatordashboard aanpassen en apparaten beheren in Azure IoT Central


In deze zelfstudie leert u hoe u het operatordashboard kunt aanpassen in uw Azure IoT Central In-Store Analytics-toepassing. Toepassingsoperators kunnen het aangepaste dashboard gebruiken om de toepassing uit te voeren en de gekoppelde apparaten te beheren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De naam van het dashboard wijzigen
> * Afbeeldingstegels op het dashboard aanpassen
> * Tegels schikken om de lay-out te wijzigen
> * Telemetrie-tegels toevoegen aan weergavevoorwaarden
> * Eigenschapstegels toevoegen om de details van het apparaat weer te geven
> * Opdrachttegels toevoegen om opdrachten uit te voeren

## <a name="prerequisites"></a>Vereisten

De builder moet de zelfstudie voltooien om de Azure IoT Central In-Store Analytics-toepassing te maken en apparaten toe te voegen:

* [Een In-Store Analytics toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (vereist)

## <a name="change-the-dashboard-name"></a>De naam van het dashboard wijzigen
Als u het operatordashboard wilt aanpassen, moet u het standaarddashboard in uw toepassing bewerken. U kunt ook extra nieuwe dashboards maken. De eerste stap bij het aanpassen van het dashboard in uw toepassing is het wijzigen van de naam.

1. Navigeer naar de website [Azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral).

1. Open de toepassing voor het controleren van de voorwaarde die u hebt gemaakt in de zelfstudie [Een In-Store Analytics toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md).

1. Selecteer **Bewerken** op de werkbalk van het dashboard. In de bewerkingsmodus kunt u het uiterlijk, de indeling en de inhoud van het dashboard aanpassen.

    ![Dashboard van Azure IoT Central bewerken](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. U kunt het linkerdeelvenster eventueel verbergen. Het linkerdeelvenster verbergen biedt een groter werkgebied voor het bewerken van het dashboard.

1. Voer een beschrijvende naam in voor uw dashboard in **Dashboardnaam.** In deze zelfstudie wordt gebruikgemaakt van een fictief bedrijf met de naam Contoso en de naam van het voorbeelddashboard is *Contoso dashboard*. 

1. Selecteer **Opslaan**. Uw wijzigingen worden opgeslagen in het dashboard en de bewerkingsmodus is uitgeschakeld.

    ![Dashboard naam van Azure IoT Central wijzigen](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Afbeeldingstegels op het dashboard aanpassen
Een Azure IoT Central-toepassingsdashboard bestaat uit een of meer tegels. Een tegel is een rechthoekige container voor het weergeven van inhoud op een dash board. U kunt verschillende typen inhoud koppelen aan tegels en u kunt tegels slepen, neerzetten en het formaat ervan wijzigen om een dashboardindeling aan te passen. Er zijn verschillende soorten tegels voor het weergeven van inhoud. Afbeeldingstegels bevatten afbeeldingen en u kunt een URL toevoegen waarmee gebruikers kunnen klikken op de installatiekopie. In labeltegels wordt tekst zonder opmaak weergegeven. Markdown-tegels bevatten opgemaakte inhoud en stellen u in staat om een afbeelding, een URL, een titel en een markdown-code te definiëren die als HTML wordt weergegeven. Op tegels voor telemetrie, eigenschappen of opdrachten worden apparaatspecifieke gegevens weergegeven. 

In deze sectie leert u hoe u afbeeldingstegels kunt aanpassen in het dashboard.

De tegel afbeelding aanpassen waarin een merkafbeelding op het dashboard wordt weergegeven:

1. Selecteer **Bewerken** op de werkbalk van het dashboard. 

1. Selecteer **Configureren** op de afbeeldingstegel die de merkafbeelding van Norhwind weergeeft. 

    ![Merkafbeelding van Azure IoT Central bewerken](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Wijzig de **Titel**. De titel verschijnt wanneer een gebruiker met de muis over de afbeelding beweegt.

1. Selecteer **Afbeelding**. Er wordt een dialoogvenster geopend en u kunt een aangepaste afbeelding uploaden. 

1. Geef desgewenst een URL op voor de afbeelding.

1. Selecter **Configuratie bijwerken**. Met de knop **Configuratie bijwerken** worden wijzigingen in het dashboard opgeslagen en blijft de bewerkingsmodus ingeschakeld.

    ![Merkafbeelding van Azure IoT Central opslaan](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Selecteer eventueel **Configureren** op de tegel met de titel **Documentatie** en geef een URL op voor de ondersteuningsinhoud. 

De afbeeldingstegel aanpassen waarin een kaart van de sensorzones in de winkel wordt weergegeven:

1. Selecteer **Configureren** op de afbeeldingstegel waarop de standaard opslagzone-toewijzing wordt weergegeven. 

1. Selecteer **Afbeelding** en gebruik het dialoogvenster voor het uploaden van een aangepaste afbeelding van een winkelzone-toewijzing. 

1. Selecter **Configuratie bijwerken**.

    ![Winkelkaart van Azure IoT Central opslaan](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    In het voorbeeld Contoso winkeltoewijzingen worden vier zones weergegeven: twee kassazones, een zone voor kleding en persoonlijke verzorging en een zone voor boodschappen en delicatessen. In deze zelfstudie koppelt u sensoren aan deze zones om telemetrie te bieden.

    ![Azure IoT Central-winkelzones](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Selecteer **Opslaan**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Tegels schikken om de lay-out te wijzigen
Een belangrijke stap bij het aanpassen van een dashboard is het rangschikken van de tegels om een nuttige weergave te maken. Toepassingsoperatoren gebruiken het dashboard om telemetrie van apparaten te visualiseren, apparaten te beheren en voorwaarden in een winkel te bewaken. Azure IoT Central vereenvoudigt de Application Builder-taak voor het maken van een dashboard. Met de bewerkingsmodus van het dashboard kunt u snel tegels toevoegen, verplaatsen, vergroten of verkleinen en verwijderen. Met de toepassingssjabloon **In-Store Analytics-kassa** wordt ook de taak voor het maken van een dashboard vereenvoudigd. Het biedt een werkende dashboardindeling, met verbonden sensoren en tegels die het aantal uitchecklijnen en omgevingsvoorwaarden weergeven.

In deze sectie rangschikt u het dashboard in de toepassingssjabloon **In-Store Analytics-kassa** om een aangepaste indeling te maken.

Tegels verwijderen die u niet in uw toepassing wilt gebruiken:

1. Selecteer **Bewerken** op de werkbalk van het dashboard. 

1. Selecteer **X Verwijderen** om de volgende tegels te verwijderen: **Terug naar alle zones**, **Ga naar het winkeldashboard**, **Wachttijd** en alle drie de tegels die zijn gekoppeld aan **Kassa 3**. Het Contoso winkeldashboard gebruikt deze tegels niet. 

    ![Tegels van Azure IoT Central verwijderen](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Scroll om de resterende dashboardtegels in beeld te brengen.

1. Selecteer **X Verwijderen** om de volgende tegels te verwijderen: **Opwarmen kassazone**, **Afkoelen kassazone**, **Instellingen voor de bezettingssensor**, **Instellingen voor de thermostaat-sensor** en **Omgevingsomstandigheden**. 

   ![Resterende tegels van Azure IoT Central verwijderen](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Selecteer **Opslaan**. Als u ongebruikte tegels verwijdert, maakt u ruimte vrij op de pagina bewerken en vereenvoudigt u de dashboardweergave voor operators.

1. Bekijk uw wijzigingen in het dashboard.

   ![Azure IoT Central na het verwijderen van tegels](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Nadat u ongebruikte tegels hebt verwijderd, rangschikt u de overige tegels opnieuw om een geordende indeling te maken. De nieuwe indeling bevat ruimte voor tegels die u in een latere stap toevoegt.

De overige tegels opnieuw indelen:

1. Selecteer **Bewerken**.

1. Selecteer de tegel **Bezettings-firmware** en sleep deze naar rechts van de batterijtegel **Bezetting**.

1. Selecteer de tegel **Thermostaat-firmware** en sleep deze naar rechts van de batterijtegel **Thermostaat**.

1. Selecteer **Opslaan**.

1. Bekijk uw wijzigingen in de indeling. 

    ![Batterijtegels voor Azure IoT Central-firmware](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Telemetrie-tegels toevoegen aan weergavevoorwaarden
Nadat u de indeling van het dashboard hebt aangepast, kunt u tegels toevoegen om telemetrie weer te geven. Als u een telemetrie-tegel wilt maken, selecteert u een apparaatsjabloon en apparaatinstantie en selecteert u apparaatspecifieke telemetrie om weer te geven op de tegel. De toepassingssjabloon **In-Store Analytics-kassa** bevat verschillende telemetrie-tegels in het dashboard. De vier tegels in de twee kassazones tonen telemetrie van de gesimuleerde bezettingssensor. De tegel **Personenverkeer** bevat aantallen in de twee kassazones. 

In deze sectie voegt u twee nieuwe telemetrie-tegels toe voor het weergeven van de telemetrie van de omgeving van de RuuviTag-sensoren die u hebt toegevoegd in de zelfstudie [Een in-Store Analytics-toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md). 

Tegels toevoegen voor het weergeven van omgevingsgegevens van de RuuviTag-sensoren:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst **Apparaatsjabloon**. 

1. Selecteer een **Apparaatinstantie** van een van de twee RuuviTag-sensoren. Selecteer in de voorbeeld Contoso-winkel `Zone 1 Ruuvi` om een telemetrie-tegel voor Zone 1 te maken. 

1. Selecteer `Relative humidity` en `temperature` in de lijst **Telemetrie**. Dit zijn de telemetriegegevens die voor elke zone op de tegel worden weergegeven.

1. Selecteer **Combineren**. 

    ![RuuviTag-tegel 1 toevoegen in Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Er wordt een nieuwe tegel weergegeven om de gecombineerde luchtvochtigheid en de temperatuurtelemetrie voor de geselecteerde sensor weer te geven. 

1. Selecteer **Configureren** op de nieuwe tegel voor de RuuviTag-sensor. 

1. Wijzig de **Titel** in *Zone 1 omgeving*. 

1. Selecter **Configuratie bijwerken**.

1. Herhaal de vorige stappen om een tegel te maken voor de tweede sensorinstantie. Stel de **Titel** in op *Zone 2 omgeving* en selecteer vervolgens **Update configureren.**

1. Sleep de tegel met de titel **Zone 2 omgeving** onder de tegel **Thermostaat-firmware**. 

1. Sleep de tegel met de titel **Zone 1 omgeving** onder de tegel **Personenverkeer**. 

1. Selecteer **Opslaan**. In het dashboard wordt de zone-telemetrie in de twee nieuwe tegels weergegeven.

    ![Alle RuuviTag-tegels toevoegen in Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Als u de tegel **Personenverkeer** wilt bewerken om telemetrie weer te geven voor slechts twee kassaszones:

1. Selecteer **Bewerken**. 

1. Selecteer **Configureren** op de tegel **Personenverkeer**.

1. In **Telemetrie** selecteert u **aantal 1**, **aantal 2** en **aantal 3**. 

1. Selecter **Configuratie bijwerken**. De bestaande configuratie op de tegel wordt gewist. 

1. Selecteer weer **Configureren** op de tegel **Personenverkeer**.

1. In **Telemetrie** selecteert u **aantal 1** en **aantal 2**. 

1. Selecter **Configuratie bijwerken**. 

1. Selecteer **Opslaan**.  Het bijgewerkte dashboard geeft alleen aantallen weer voor uw twee kassazones, die zijn gebaseerd op de gesimuleerde bezettingssensor.

    ![Azure IoT Central personenverkeer twee banen](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Eigenschapstegels toevoegen om de details van het apparaat weer te geven
Toepassingsoperators gebruiken het dashboard om apparaten te beheren en de status van de monitor te controleren. Voeg een tegel toe voor elke RuuviTag om operators in staat te stellen de softwareversie te bekijken. 

Een eigenschappentegel toevoegen voor elke RuuviTag:

1. Selecteer **Bewerken**.

1. Selecteer `RuuviTag` in de lijst **Apparaatsjabloon**. 

1. Selecteer een **Apparaatinstantie** van een van de twee RuuviTag-sensoren. Selecteer in de voorbeeld Contoso-winkel `Zone 1 Ruuvi` om een telemetrie-tegel voor Zone 1 te maken. 

1. Selecteer **Eigenschappen > Softwareversie**.

1. Selecteer **Combineren**. 

1. Selecteer **Configureren** op de zojuist gemaakte tegel met de titel **Softwareversie**. 

1. Wijzig de **Titel** in *Ruuvi 1-softwareversie*.

1. Selecter **Configuratie bijwerken**. 

1. Sleep de tegel met de titel **Ruuv 1-softwareversie** onder de tegel **Zone 1 omgeving**.

1. Herhaal de vorige stappen om een tegel voor de eigenschappen van een softwareversie voor de tweede RuuviTag te maken. 

1. Selecteer **Opslaan**.  

    ![Eigenschappentegels van Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Opdrachttegels toevoegen om opdrachten uit te voeren
Toepassingsoperators gebruiken het dashboard ook om apparaten te beheren en de status van de monitor te controleren. U kunt opdrachttegels toevoegen aan het dashboard waarmee vooraf gedefinieerde opdrachten op een apparaat worden uitgevoerd. In deze sectie voegt u een opdrachttegel toe om operators in te schakelen om de Rigado-Gateway opnieuw op te starten. 

Een opdrachttegel toevoegen om de gateway opnieuw op te starten:

1. Selecteer **Bewerken**. 

1. Selecteer `C500` in de lijst **Apparaatsjabloon**. Het is de sjabloon voor de Rigado C500-gateway. 

1. Selecteer de instantie van de gateway in **Apparaatinstantie**.

1. Selecteer **Opdracht > Opnieuw opstarten** en sleep deze naar het dashboard naast de winkelkaart. 

1. Selecteer **Opslaan**. 

1. Bekijk uw voltooide Contoso-dashboard. 

    ![Aanpassing van Azure IoT Central dashboard-aanpassing voltooien](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Selecteer desgewenst de tegel **Opnieuw opstarten** om de opdracht Opnieuw opstarten op de gateway uit te voeren.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

* De naam van het dashboard wijzigen
* Afbeeldingstegels op het dashboard aanpassen
* Tegels schikken om de lay-out te wijzigen
* Telemetrie-tegels toevoegen aan weergavevoorwaarden
* Eigenschapstegels toevoegen om de details van het apparaat weer te geven
* Opdrachttegels toevoegen om opdrachten uit te voeren

Nu u het dashboard hebt aangepast in uw Azure IoT Central In-Store Analytics-toepassing, volgt u de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Gegevens exporteren en inzichten virtualiseren](./tutorial-in-store-analytics-export-data-visualize-insights.md)
