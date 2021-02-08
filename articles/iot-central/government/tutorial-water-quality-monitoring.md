---
title: 'Zelfstudie: Een app voor het bewaken van de waterkwaliteit maken in Azure IoT Central'
description: 'Zelfstudie: Lees hier hoe u met behulp van toepassingssjablonen van Azure IoT Central een app kunt maken om de waterkwaliteit te bewaken.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a928b1223b91faae6d4aca68b81cc16c827933b3
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820061"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Zelfstudie: Een toepassing voor het bewaken van de waterkwaliteit maken in Azure IoT Central

In deze zelfstudie wordt uitgelegd hoe u in Azure IoT Central een toepassing maakt voor het bewaken van de waterkwaliteit. U maakt de toepassing met behulp van de toepassingssjabloon **Bewaking van waterkwaliteit** van Azure IoT Central.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De sjabloon **Bewaking van waterkwaliteit** gebruiken om een toepassing te maken voor het bewaken van de waterkwaliteit.
> * Een dashboard voor gebruikers verkennen en aanpassen.
> * Een apparaatsjabloon voor de bewaking van waterkwaliteit verkennen.
> * Gesimuleerde apparaten verkennen.
> * Regels verkennen en configureren.
> * Taken configureren.
> * De huisstijl van een toepassing aanpassen met behulp van whitelabeling.

## <a name="prerequisites"></a>Vereisten

Het is handig als u een Azure-abonnement hebt voordat u aan deze zelfstudie begint. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Een toepassing voor het bewaken van de waterkwaliteit maken in Azure IoT Central

In dit gedeelte gebruikt u de sjabloon **Bewaking van waterkwaliteit** van Azure IoT Central om een toepassing te maken voor het bewaken van de waterkwaliteit.

1. Ga naar de [startpagina van Azure IoT Central](https://aka.ms/iotcentral).

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen tot dat abonnement. Meld u anders aan met een Microsoft-account:

    ![Aanmelden bij uw organisatieaccount](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Selecteer **Versie** aan de linkerkant van het venster van Azure IoT Central en selecteer het tabblad **Overheid**. Op het tabblad Overheid worden verschillende sjablonen voor overheidstoepassingen weergegeven.

    ![Sjablonen voor overheidstoepassingen](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Selecteer de toepassingssjabloon **Bewaking van waterkwaliteit**. Deze toepassingssjabloon bevat een sjabloon voor een apparaat voor het controleren van de waterkwaliteit, gesimuleerde apparaten, een dashboard voor gebruikers en vooraf geconfigureerde bewakingsregels.

1. Selecteer **App maken**. Het deelvenster **Nieuwe toepassing** wordt geopend met daarin de volgende elementen:

    * **Toepassingsnaam**: De toepassing heeft standaard de naam **Bewaking van waterkwaliteit** gevolgd door een unieke id bestaande uit een tekenreeks die door Azure IoT Central wordt gegenereerd. Als u wilt, kunt u een weergavenaam invoeren of de naam van de toepassing later wijzigen.
    * **URL**: U kunt elke gewenste URL invoeren of de URL-waarde later wijzigen.
    * Als u een Azure-abonnement hebt, voert u waarden in voor **Map**, **Azure-abonnement** en **Locatie**. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen.

    Zie de quickstart [Create an Azure IoT Central application](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (Een Azure IoT Central-toepassing maken) voor meer informatie over mappen en abonnementen.

1. Selecteer linksonder op de pagina de knop **Maken**.

    ![De pagina Nieuwe toepassing in Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![De pagina Factureringsgegevens in Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

U hebt nu een toepassing voor het bewaken van de waterkwaliteit gemaakt aan de hand van de sjabloon **Bewaking van waterkwaliteit** van Azure IoT Central.

De nieuwe toepassing wordt geleverd met de volgende vooraf geconfigureerde onderdelen:

* Dashboards voor gebruikers
* Sjablonen voor apparaten voor de bewaking van de waterkwaliteit
* Gesimuleerde apparaten voor de bewaking van de waterkwaliteit
* Regels en taken
* Een huisstijl waarin whitelabeling wordt toegepast

U kunt uw toepassing op elk gewenst moment wijzigen.

We gaan nu de toepassing verkennen en enkele aanpassingen doorvoeren.

## <a name="explore-and-customize-the-operator-dashboard"></a>Het dashboard voor gebruikers verkennen en aanpassen

Nadat u de toepassing hebt gemaakt, wordt het venster **Wide World water quality dashboard** geopend.

   ![Het dashboard voor het bewaken van de waterkwaliteit](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Als ontwikkelaar kunt u in het dashboard weergaven maken en aanpassen voor de gebruikers van de toepassing. We gaan het dashboard echter eerst nader bekijken voordat u het gaat aanpassen.

Alle gegevens die in het dashboard worden weergegeven, zijn gebaseerd op gegevens van gesimuleerde apparaten. Dit komt in de volgende sectie aan de orde.

Het dashboard bevat de volgende soorten tegels:

* **Tegel met afbeelding van Wide World Water Utility**: De eerste tegel in de linkerbovenhoek van het dashboard is een afbeelding waarin het fictieve nutsbedrijf met de naam Wide World wordt weergegeven. U kunt de tegel aanpassen met uw eigen afbeelding en u kunt de tegel ook verwijderen.

* **Tegels met gemiddelden**: Bovenaan het dashboard staan KPI-tegels zoals **Gemiddelde pH, afgelopen 30 minuten**. U kunt de KPI-tegels aanpassen en elke tegel instellen op een ander type en ander tijdsbereik.

* **Kaart van gebied voor bewaking van waterkwaliteit**: Azure IoT Central maakt gebruik van Azure Maps en u kunt rechtstreeks in uw toepassing instellen dat de locatie van het apparaat moet worden weergegeven. U kunt ook locatiegegevens vanuit uw toepassing toewijzen aan uw apparaat en vervolgens Azure Maps gebruiken om de gegevens op een kaart weer te geven. Beweeg de muisaanwijzer over de kaart en probeer de besturingselementen uit.

* **Heatmap van gemiddelde pH-verdeling**: U kunt verschillende visualisatiediagrammen selecteren om telemetrie van apparaten weer te geven op de manier die het meest geschikt is voor uw toepassing.

* **Lijndiagram met kritieke kwaliteitsindicatoren**: U kunt de telemetrie van apparaten visualiseren als een lijndiagram voor een bepaald tijdsbereik.  

* **Staafdiagram met de concentratie chemische stoffen**: U kunt de telemetrie van apparaten visualiseren in een staafdiagram.

* **Actieknop**: Het dashboard bevat een tegel voor acties die een gebruiker rechtstreeks vanuit het bewakingsdashboard kan initiëren. Het opnieuw instellen van de eigenschappen van een apparaat is een voorbeeld van een dergelijke actie.

* **Tegels met eigenschappenlijsten**: Het dashboard bevat verschillende tegels met eigenschappen die drempelwaarden vertegenwoordigen, evenals statusinformatie voor apparaten en onderhoudsinformatie.

### <a name="customize-the-dashboard"></a>Het dashboard aanpassen

Als ontwikkelaar kunt u weergaven van het dashboard aanpassen voor de gebruikers van de toepassing.

1. Selecteer **Bewerken** om het deelvenster **Wide World water quality dashboard** aan te passen. U kunt het dashboard aanpassen door opdrachten te selecteren in het menu **Bewerken**. Zodra het dashboard in de bewerkingsmodus wordt weergegeven, kunt u nieuwe tegels toevoegen of de bestaande tegels aanpassen.

    ![Het dashboard bewerken](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Selecteer **+ Nieuw** om een nieuw dashboard te maken en dit te configureren. U kunt meerdere dashboards hebben en u kunt navigeren tussen dashboards via het dashboardmenu.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Een apparaatsjabloon voor de bewaking van waterkwaliteit verkennen

Een apparaatsjabloon in Azure IoT Central definieert de mogelijkheden van een apparaat. Beschikbare mogelijkheden zijn telemetrie, eigenschappen en opdrachten. Als ontwikkelaar kunt u in Azure IoT Central apparaatsjablonen definiëren die de mogelijkheden vertegenwoordigen van de verbonden apparaten. U kunt ook gesimuleerde apparaten maken om uw apparaatsjabloon en toepassing te testen.

De toepassing voor de bewaking van de waterkwaliteit die u hebt gemaakt, bevat een apparaatsjabloon voor het bewaken van de waterkwaliteit.

De apparaatsjabloon weergeven:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster van uw toepassing in Azure IoT Central.
1. Selecteer **Water Quality Monitor** in de lijst met apparaatsjablonen. De apparaatsjabloon wordt geopend.

    ![De apparaatsjabloon](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Het apparaatsjabloon aanpassen

Oefen door de volgende instellingen voor de apparaatsjabloon aan te passen:

1. Selecteer **Aanpassen** in het menu van de apparaatsjabloon.
1. Ga naar het telemetrietype **Temperature**.
1. Wijzig de waarde van **Weergavenaam** in **Reported temperature**.
1. U kunt ook de maateenheid wijzigen of de waarden voor **Minimumwaarde** en **Maximumwaarde** instellen.
1. Selecteer **Opslaan**.

#### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen

1. Selecteer **Cloudeigenschap** in het menu van de apparaatsjabloon.
1. Als u een nieuwe cloudeigenschap wilt toevoegen, selecteert u **+ Cloudeigenschap toevoegen**. In Azure IoT Central kunt u een eigenschap toevoegen die relevant is voor een apparaat, maar die naar verwachting niet door het apparaat zal worden verzonden. Een voorbeeld van een dergelijke eigenschap is een waarschuwingsdrempel die specifiek is voor een installatiegebied, gegevens van een asset of onderhoudsinformatie.
1. Selecteer **Opslaan**.

### <a name="explore-views"></a>Weergaven verkennen

De apparaatsjabloon voor het bewaken van de waterkwaliteit bevat vooraf gedefinieerde weergaven. De weergaven bepalen hoe gebruikers de gegevens van het apparaat zien, maar ook hoe ze cloudeigenschappen kunnen instellen. Bekijk de weergaven en oefen door wat wijzigingen aan te brengen.

  ![Weergave van apparaatsjabloon](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Als u wijzigingen hebt aangebracht, vergeet dan niet om **Publiceren** te selecteren om de gewijzigde apparaatsjabloon te publiceren.

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken

1. Selecteer **+ Nieuw** en volg het proces voor het maken van een nieuwe apparaatsjabloon.
1. Maak een aangepaste apparaatsjabloon of kies een apparaatsjabloon uit de apparaatcatalogus van Azure IoT Central.

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

De toepassing voor het bewaken van de waterkwaliteit die u hebt gemaakt op basis van de toepassingssjabloon bevat twee gesimuleerde apparaten. Deze apparaten zijn gekoppeld aan de apparaatsjabloon voor de bewaking van de waterkwaliteit.

### <a name="view-the-devices"></a>De apparaten weergeven

1. Selecteer **Apparaten** in het meest linkse deelvenster van uw toepassing.

   ![Apparaten](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Selecteer één gesimuleerd apparaat.

    ![Eén apparaat selecteren](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Wijzig op het tabblad **Cloudeigenschappen** de waarde voor **Acidity (pH) threshold** van **8** in **9**.
1. Verken het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard**.

> [!NOTE]
> Alle tabbladen zijn geconfigureerd vanuit **apparaatsjabloonweergaven**.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

Selecteer **+ Nieuw** op het tabblad **Apparaten** om een nieuw apparaat toe te voegen.

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central kunt u regels maken om de telemetrie van apparaten automatisch te bewaken. Deze regels activeren een actie wanneer aan een van de voorwaarden wordt voldaan. Een voorbeeld van een actie is het verzenden van meldingen per e-mail. Andere mogelijkheden zijn een Power Automate-actie of een webhookactie om gegevens naar andere services te verzenden.

De toepassing voor het bewaken van de waterkwaliteit die u hebt gemaakt, heeft twee vooraf geconfigureerde regels.

### <a name="view-rules"></a>Regels weergeven

1. Selecteer **Regels** in het meest linkse deelvenster van uw toepassing.

   ![Regels](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Selecteer **High pH alert**, een van de vooraf geconfigureerde regels in de toepassing.

   ![De regel voor hoge pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   De regel **High pH alert** is geconfigureerd om te controleren of de zuurgraad (pH) hoger is dan 8.

Voeg vervolgens een e-mailactie toe aan de regel:

1. Selecteer **+ E-mailadres**.
1. Voer in het vak **Weergavenaam** **High pH alert** in.
1. Voer in het vak **Tot** het e-mailadres in dat is gekoppeld aan uw account van IoT Central.
1. Voer eventueel een opmerking in die u wilt opnemen in de tekst van het e-mailbericht.
1. Selecteer **Gereed** om de actie te voltooien.
1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren.

Binnen een paar minuten ontvangt u een e-mailbericht als aan de geconfigureerde voorwaarde is voldaan.

> [!NOTE]
> Telkens wanneer er aan een voorwaarde wordt voldaan, verzendt de toepassing een e-mail. Selecteer **Uitschakelen** voor een regel om geen automatische e-mails meer te ontvangen voor die regel.
  
Als u een nieuwe regel wilt maken, selecteert u **Regels** in het meest linkse deelvenster van uw toepassing en selecteert u vervolgens **+ Nieuw**.

## <a name="configure-jobs"></a>Taken configureren

In Azure IoT Central kunt u met behulp van taken updates van apparaat- of cloudeigenschappen activeren op meerdere apparaten tegelijk. U kunt taken ook gebruiken om apparaatopdrachten op meerdere apparaten te activeren. Azure IoT Central zorgt ervoor dat deze werkstroom voor u wordt geautomatiseerd.

1. Selecteer **Taken** in het meest linkse deelvenster van uw toepassing.
1. Selecteer **+ Nieuw** en configureer een of meer taken.

## <a name="customize-your-application"></a>Uw toepassing aanpassen

Als ontwikkelaar kunt u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

1. Selecteer **Beheer** > **Uw toepassing aanpassen**.
1. Selecteer **Wijzigen** onder **Impressumlogo** om de afbeelding te kiezen die u als het logo wilt uploaden.
1. Selecteer **Wijzigen** onder **Browserpictogram** om de afbeelding te kiezen die wordt weergegeven op browsertabbladen.
1. Onder de **Browserkleuren** kunt u de standaardwaarden vervangen door hexadecimale HTML-kleurcodes.
1. Selecteer **Instellingen** om de waarde van **Thema** te wijzigen.

   ![Uw toepassing aanpassen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>De afbeelding voor de toepassing bijwerken

1. Selecteer **Beheer** > **Uw toepassing**.

1. Gebruik de knop **Afbeelding selecteren** om een afbeelding te kiezen die u wilt uploaden als de toepassingsafbeelding.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de toepassing via de volgende stappen:

1. Open het tabblad **Beheer** in het meest linkse deelvenster van uw toepassing.
1. Selecteer **Uw toepassing** en selecteer de knop **Verwijderen** onderaan de pagina.

    ![Uw toepassing verwijderen](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over 

> [!div class="nextstepaction"]
> [Bewakingsconcepten voor waterkwaliteit](./concepts-waterqualitymonitoring-architecture.md).
