---
title: 'Zelfstudie: Een app voor bewaking van waterverbruik maken met Azure IoT Central'
description: 'Zelfstudie: Lees hier hoe u met behulp van toepassingssjablonen van Azure IoT Central een app kunt maken om waterverbruik te bewaken.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587193"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Zelfstudie: Een app voor bewaking van waterverbruik maken met Azure IoT Central

In deze zelfstudie wordt uitgelegd hoe u een Azure IoT Central-bewakingstoepassing voor waterverbruik maakt met behulp van de toepassingssjabloon Azure IoT Central-bewaking waterverbruik.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de Azure IoT Central-bewakingssjabloon waterverbruik om uw toepassing voor waterverbruik-bewaking te maken.
> * Het dashboard van de operator verkennen en aanpassen.
> * Sjablonen voor apparaten verkennen.
> * Gesimuleerde apparaten verkennen.
> * Regels verkennen en configureren.
> * Taken configureren.
> * De huisstijl van uw toepassing aanpassen met behulp van whitelabeling.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Een app voor bewaking van waterverbruik maken met Azure IoT Central

In deze sectie gebruikt u de Azure IoT Central-sjabloon voor de bewaking van waterverbruik om uw toepassing voor bewaking van waterverbruik in Azure IoT Central te maken.

Voor het maken van een nieuwe Azure IoT Central-toepassing voor het bewaken van waterverbruik:

1. Ga naar de website [Startpagina van Azure IoT Central](https://aka.ms/iotcentral).

    Meld u aan met de referenties die u gebruikt om toegang te krijgen tot uw Azure-abonnement. Meld u anders aan met behulp van een Microsoft-account.

    ![Voer uw organisatieaccount in](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Klik op **Bouwen** in het linkerdeelvenster en selecteer het tabblad **Overheid**. Op de **Overheids**-pagina worden verschillende sjablonen voor overheidstoepassingen weergegeven.

   ![Sjablonen voor overheids-apps bouwen](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassingssjabloon **Bewaking waterverbruik**.
Deze sjabloon bevat een voorbeeld van een sjabloon voor waterverbruik, een gesimuleerd apparaat, een operator-dashboard en vooraf geconfigureerde bewakingsregels.

1. Klik op **App maken**. Hiermee wordt het aanmaakformulier **Nieuwe toepassing** geopend, waarin de volgende velden zijn te vinden:
    * **Toepassingsnaam**: De toepassing maakt standaard gebruik van *Waterconsumptie bewaken* gevolgd door een unieke ID-tekenreeks die door Azure IoT Central wordt gegenereerd. Kies desgewenst een beschrijvende toepassingsnaam. U kunt de toepassingsnaam ook later nog wijzigen.
    * **URL**: Azure IoT Central genereert automatisch een URL op basis van de naam van de toepassing. U kunt ervoor kiezen om de URL naar uw eigen smaak bij te werken. U kunt de URL later ook nog wijzigen.
    * Als u een Azure-abonnement hebt, voert u de gegevens van uw **directory**, **Azure-abonnement** en **regio** in. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen.

    Zie de [Snelstartgids over het maken van een toepassing](../core/quick-deploy-iot-central.md) voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken** onderaan de pagina.

    ![Pagina Nieuwe toepassing Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Pagina met factureringsgegevens voor Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

U hebt nu een app voor bewaking van het waterverbruik gemaakt met behulp van de Azure IoT Central-sjabloon voor bewaking van waterverbruik.

De toepassing bewaking waterconsumptie wordt geleverd met vooraf geconfigureerd:

* Voorbeelden van operatordashboards.
* Voorbeeld van vooraf gedefinieerde waterstroom-en klep-apparaatsjablonen.
* Gesimuleerde waterstroom-en slimme klep-apparaten.
* Regels en taken.
* Voorbeeld van een huisstijl waarbij whitelabeling is gebruikt.

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het dashboard van de operator verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het voorbeeld **Dashboard voor waterverbruik van Wide World** geopend.

   ![Bewakingsdashboard waterverbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Als ontwikkelaar kunt u weergaven in het operatordashboard maken en aanpassen. We gaan het dashboard verkennen voordat u het gaat aanpassen.

> [!NOTE]
> Alle gegevens die in het dashboard worden weergegeven, zijn gebaseerd op gesimuleerde apparaatgegevens. Dit komt in de volgende sectie aan de orde.
  
Het dashboard bestaat uit verschillende soorten tegels:

* **Wide World Water Utility-afbeeldingstegel**: De eerste tegel in het dashboard is een afbeeldingstegel van het fictieve water-hulpprogramma voor Wide World Water. U kunt de tegel aanpassen en uw afbeelding gebruiken of deze verwijderen.
* **KPI-tegel voor gemiddelde waterstroom**: De KPI-tegel is zo geconfigureerd dat deze als voorbeeld *het gemiddelde in de afgelopen 30 minuten* weergeeft. U kunt de KPI-tegel aanpassen en instellen op een ander type en tijdsbereik.
* **Tegels voor apparaatopdrachten**: Deze tegels omvatten de tegels **Klep sluiten**, **Klep openen** en **Kleppositie instellen**. Als u de opdrachten selecteert, gaat u naar de opdrachtpagina voor het gesimuleerde apparaat. In Azure IoT Central is een *opdracht* van het typee *apparaatondersteuning*. We verkennen dit concept later in het gedeelte "Apparaatsjabloon" van deze zelfstudie.
* **Gebiedskaart waterdistributie**: De kaart maakt gebruik van Azure Maps, dat u rechtstreeks in Azure IoT Central kunt configureren. Op de tegel met de kaart wordt de locatie van het apparaat weergegeven. Beweeg de muisaanwijzer over de kaart en probeer de besturingselementen op de kaart uit, zoals *Inzoomen*, *Uitzoomen* of *Uitbreiden*.

    ![Dashboardkaart voor bewaking van waterverbruik](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Lijndiagram gemiddelde waterstroom** en **Diagram omgevingsconditie**: U kunt een of meer apparaat-telemetrieën visualiseren die zijn getekend als een lijndiagram over een gewenst tijdsbereik.
* **Gemiddelde heatmap-grafiek van klepdruk**: U kunt kiezen welk type apparaat-telemetriegegevens voor heatmap-visualisatie moet worden weergegeven in een tijdsbereik met een kleurindex.
* **Tegel inhoud waarschuwingsdrempels opnieuw instellen**: U kunt call-to-action-inhoudstegels opnemen en een koppeling insluiten naar een actiepagina. In dit geval gaat u met de drempelwaarde voor opnieuw instellen naar de **Taken** van de toepassing, waar u updates kunt uitvoeren op apparaateigenschappen. We verkennen deze optie later in de sectie "Taken configureren" van deze zelfstudie.
* **Eigenschapstegels**: In het dashboard worden de tegels van **Operationele klepgegevens**, **Flow-waarschuwingsdrempels** en **Onderhoudsgegevens** weergegeven.

### <a name="customize-the-dashboard"></a>Het dashboard aanpassen

Als ontwikkelaar kunt u weergaven in het operatordashboard aanpassen.

1. Selecteer **Bewerken** om het **dashboard Wide World-waterverbruik aan te passen**. U kunt het dashboard aanpassen door op het menu **Bewerken** te klikken. Zodra het dashboard in de **Bewerkingsmodus** is, kunt u nieuwe tegels toevoegen of configureren.

     ![Dashboard instellen](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dashboard te maken en dit helemaal opnieuw te configureren. U kunt meerdere dashboards hebben en u kunt van het ene naar het andere dashboard navigeren via het dashboardmenu.

## <a name="explore-the-device-template"></a>De apparaatsjabloon verkennen

Via een apparaatsjabloon in Azure IoT Central worden de mogelijkheden van een apparaat gedefinieerd, dat kunnen telemetrische gegevens, eigenschappen of opdrachten zijn. Als ontwikkelaar kunt u in Azure IoT Central een of meer apparaatprofielen definiëren die de mogelijkheden vertegenwoordigen van de apparaten die u wilt verbinden.

De toepassing voor bewaking van waterverbruik wordt geleverd met twee referentie-apparaatsjablonen die de apparaten *stroommeter* en *slimme klep* vertegenwoordigen.

De apparaatsjabloon weergeven:

1. Klik op **Apparaatsjablonen** in het linkerdeelvenster van uw toepassing in Azure IoT Central. In de lijst **Apparaatsjablonen** ziet u twee apparaatsjablonen, **Slimme klep** en **Stroommeter**.

   ![Apparaatsjabloon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Selecteer het apparaatsjabloon **Stroommeter** en leer de mogelijkheden van het apparaat kennen.

     ![Apparaatsjabloon Stroommeter](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Het apparaatsjabloon aanpassen

De sjabloon voor het apparaat aanpassen:

1. Ga naar **Aanpassen** in het menu **Apparaatsjablonen**.
1. Zoek naar het type telemetriegegevens `Temperature`.
1. Werk de **Weergavenaam** bij van `Temperature` naar `Reported temperature`.
1. U kunt ook de maateenheid bijwerken of de **Minimumwaarde** en **Maximumwaarde** instellen.
1. Selecteer **Opslaan** om de wijzigingen op te slaan.

### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen

1. Ga naar **Cloudeigenschappen** in het menu **Apparaatbeheer**.
1. Voeg een nieuwe cloudeigenschap toe door op **+ Cloudeigenschap toevoegen** te klikken.
    In Azure IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat. Een voorbeeld hiervan is als een cloudeigenschap een drempelwaarde voor een waarschuwing is die specifiek voor een installatiegebied, voor bepaalde gegevens over activa of voor onderhoudsinformatie en andere informatie geldt.
1. Selecteer **Opslaan** om de wijzigingen op te slaan.

### <a name="views"></a>Weergaven

De sjabloon voor het bewakingsapparaat voor waterverbruik bevat vooraf gedefinieerde weergaven. Bekijk de weergaven en u kunt updates maken. De weergaven bepalen hoe operators de gegevens van het apparaat zien, maar ook hoe ze cloudeigenschappen kunnen invoeren.

  ![Apparaatsjabloonweergaven](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publiceren

Als u wijzigingen hebt aangebracht, zorg er dan voor dat u de apparaatsjabloon **Publiceert**.

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

Selecteer **+ Nieuw** en volg het proces voor het maken van een nieuwe apparaatsjabloon.
U kunt zelf een volledig nieuwe apparaatsjabloon maken of een apparaatsjabloon uit de Azure-apparaatcatalogus kiezen.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In Azure IoT Central kunt u gesimuleerde apparaten maken om uw apparaatsjabloon en toepassing te testen. De bewakingstoepassing voor waterverbruik heeft twee gesimuleerde apparaten die zijn toegewezen aan de apparaatsjablonen **Stroommeter** en **Slimme klep**.

### <a name="view-the-devices"></a>De apparaten weergeven

1. Selecteer **Apparaten** > **Alle apparaten** in het linkerdeelvenster.

   ![Deelvenster Alle apparaten](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Selecteer **Slimme klep 1**.

    ![Slimme klep 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Op het tabblad **Opdrachten**, ziet u de drie opdrachten voor apparaten (**Klep sluiten**, **Klep openen** en **Kleppositie instellen**) die zijn gedefinieerd in de apparaatsjabloon **Slimme klep**.

1. Verken het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard**.

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd vanuit de apparaatsjabloonweergaven.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Voeg nieuwe apparaten toe door **+ Nieuw** te selecteren op het tabblad **Apparaten**.

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central kunt u regels maken om de telemetrische gegevens van apparaten automatisch te bewaken en acties te activeren die worden ondernomen wanneer aan een of meer voorwaarden wordt voldaan. De acties kunnen onder andere het verzenden van e-mail zijn, het activeren van een Microsoft Power Automate-actie of een webhookactie voor het verzenden van gegevens naar andere services.

De toepassing voor het controleren van waterverbruik die u hebt gemaakt heeft drie vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weergeven

1. Selecteer **Regels** in het linkerdeelvenster.

   ![Het deelvenster Regels](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Selecteer **Hoge pH-waarschuwing**, een van de vooraf geconfigureerde regels in de toepassing.

     ![Hoge pH-waarschuwing](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    De `High flow alert`-regel is geconfigureerd om te controleren op de voorwaarde `Acidity (pH)` is `greater than` de `Max flow threshold`. De maximale stroomdrempel is een cloudeigenschap die is gedefinieerd in de apparaatsjabloon **Intelligente klep**. De waarde van `Max flow threshold` wordt ingesteld per apparaatinstantie.

Nu gaan we een e-mailactie maken.

Een actie toevoegen aan de regel:

1. Selecteer **+ E-mail**.
1. Voer **Waarschuwing bij hoge pH-waarde** in als beschrijvende **Weergavenaam** voor de actie.
1. Voer het e-mailadres in dat is gekoppeld aan uw IoT Central-account bij **Naar**.
1. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mailbericht.
1. Selecteer **Gereed** om de actie te voltooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u een e-mailbericht nadat aan de geconfigureerde voorwaarde is voldaan.

> [!NOTE]
> Telkens wanneer er aan een voorwaarde wordt voldaan, verzendt de toepassing een e-mail. Selecteer de regel **Uitschakelen** om het ontvangen van e-mail via de automatische regel te stoppen.
  
Een nieuwe regel maken:

* Selecteer **+ Nieuw** bij **Regels** in het linkerdeelvenster.

## <a name="configure-jobs"></a>Taken configureren

In IoT Central kunt u met taken het bijwerken van apparaat- of cloudeigenschappen op meerdere apparaten activeren. Naast eigenschappen kunt u taken ook gebruiken om apparaatopdrachten op meerdere apparaten te activeren. Azure IoT Central zorgt dat deze werkstroom wordt geautomatiseerd.

1. Selecteer **Taken** in het linkerdeelvenster.
1. Klik op **+ Nieuw** en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als ontwikkelaar kunt u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

1. Selecteer **Beheer** > **Uw toepassing aanpassen**.
1. Gebruik de knop **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **Toepassingslogo**.
1. Gebruik de knop **Wijzigen** om een afbeelding voor het **Pictogram van de browser** te kiezen dat op de tabbladen van de browser wordt weergegeven.
1. U kunt de **Standaardkleuren van de browser** ook vervangen door hexadecimale HTML-kleurcodes toe te voegen.

   ![Selecties voor het toepassingslogo, browserpictogram en browserkleuren](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. U kunt ook afbeeldingen van toepassingen wijzigen door **Beheer** > **Toepassingsinstellingen** te selecteren. Gebruik de knop **Afbeelding selecteren** om een afbeelding te kiezen die u wilt uploaden als de toepassingsafbeelding.
1. Ten slotte kunt u ook het **Thema** wijzigen door het pictogram **Instellingen** te selecteren in de rechterbovenhoek van de toepassing.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u deze.

1. Selecteer **Beheer** in het linkerdeelvenster van uw Azure IoT Central-toepassing.
1. Selecteer **Toepassingsinstellingen** en selecteer vervolgens **Verwijderen** onderaan de pagina.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over 

> [!div class="nextstepaction"]
> [Bewakingsconcepten voor waterverbruik](./concepts-waterconsumptionmonitoring-architecture.md).
