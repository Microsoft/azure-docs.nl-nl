---
title: 'Zelfstudie: Een app voor verbonden afvalbeheer maken met Azure IoT Central'
description: 'Zelf studie: meer informatie over het bouwen van een verbonden afval beheer toepassing met behulp van Azure IoT Central-toepassings sjablonen'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727563"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Zelfstudie: Een verbonden app voor afvalbeheer maken

In deze zelfstudie leert u hoe u Azure IoT Central kunt gebruiken om een toepassing voor verbonden afvalbeheer te maken. 

U leert met name het volgende: 

> [!div class="checklist"]
> * De Azure IoT Central-sjabloon *Verbonden afvalbeheer* gebruiken om de app te maken.
> * Het dashboard van de operator verkennen en aanpassen. 
> * De apparaatsjabloon voor een verbonden afvalbak verkennen.
> * Gesimuleerde apparaten verkennen.
> * Regels verkennen en configureren.
> * Taken configureren.
> * De huisstijl van de toepassing aanpassen.

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement wordt aanbevolen. U kunt ook een gratis proefversie voor zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>De app in Azure IoT Central maken

In deze sectie gebruikt u de sjabloon voor verbonden afvalbeheer om de app in Azure IoT Central te maken. U doet dit als volgt:

1. Ga naar [Azure IoT Central](https://aka.ms/iotcentral).

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen tot dat abonnement. Of meld u aan met behulp van een Microsoft-account.

    ![Schermopname van aanmelding bij Microsoft.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Selecteer **Build** in het linkerdeelvenster. Selecteer vervolgens het tabblad **Overheid**. Op de overheidspagina worden verschillende sjablonen voor overheidstoepassingen weergegeven.

    ![Schermopname van de pagina Build in Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecteer de toepassingssjabloon **Verbonden afvalbeheer**. Deze sjabloon bevat een voorbeeld van een apparaatsjabloon voor een verbonden afvalbak, een gesimuleerd apparaat, een operator-dashboard en vooraf geconfigureerde bewakingsregels.    

1. Selecteer **App maken**, waarmee het dialoogvenster **Nieuwe toepassing** wordt geopend. Vul de gegevens voor de volgende velden in:
    * **Toepassingsnaam**. De toepassing maakt standaard gebruik van **Verbonden afvalbeheer**, gevolgd door een unieke id-tekenreeks die door Azure IoT Central wordt gegenereerd. Kies desgewenst een beschrijvende toepassingsnaam. U kunt de toepassingsnaam ook later nog wijzigen.
    * **URL**. U kunt ook de door u gewenste URL kiezen. U kunt de URL later wijzigen. 
    * **Prijsstelling**. Als u een Azure-abonnement hebt, voert u uw directory, Azure-abonnement en regio in de juiste velden van het dialoogvenster **Factureringsgegevens** in. Als u geen abonnement hebt, selecteert u **Gratis** om een proefversie voor zeven dagen in te schakelen en vult u de vereiste contactgegevens in.  

    Zie [Quickstart: een Azure IoT Central-toepassing maken ](../core/quick-deploy-iot-central.md)voor meer informatie over directory’s en abonnementen.

1. Selecteer **Maken** onder aan de pagina. 

    ![Schermopname van het dialoogvenster Nieuwe toepassing maken in Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Schermopname van het dialoogvenster Factureringsgegevens in Azure IoT Central.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Op uw zojuist gemaakte toepassing is het volgende vooraf geconfigureerd:
* Voorbeelden van operatordashboards.
* Voorbeelden van vooraf gedefinieerde apparaatsjablonen voor verbonden afvalbakken.
* Apparaten voor gesimuleerde, verbonden afvalbakken.
* Regels en taken.
* Voorbeeld van een huisstijl. 

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Het dashboard voor gebruikers verkennen en aanpassen 

Bekijk het **dashboard voor afvalbeheer van Wide World**, dat u te zien krijgt als u de app hebt gemaakt.

   ![Schermopnamen van het dashboard voor afvalbeheer van Wide World.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als ontwikkelaar kunt u weergaven in het operatordashboard maken en aanpassen. Eerst maakt u kennis met het dashboard. 

>[!NOTE]
>Alle gegevens die in het dashboard worden weergegeven, zijn gebaseerd op gegevens van gesimuleerde apparaten. Dit komt in de volgende sectie aan de orde. 

Het dashboard bestaat uit verschillende tegels:

* **Tegel met afbeelding van het afvalverwerkende bedrijf Wide World Waste**: De eerste tegel in het dashboard is een afbeeldingstegel van een fictief afvalverwerkend bedrijf, Wide World Waste. U kunt de tegel aanpassen en uw afbeelding gebruiken. U kunt de tegel ook verwijderen. 

* **Tegel met afbeelding van afvalbak**: U kunt tegels met afbeeldingen en inhoud gebruiken om het apparaat dat wordt bewaakt, samen met een beschrijving, visueel weer te geven. 

* **KPI-tegel voor vulniveau**: Op de tegel wordt een waarde weergegeven die door een *vulniveausensor* in een afvalbak wordt vermeld. De sensor voor het vulniveau en andere sensoren, zoals een *geurmeter* of die voor het *gewicht* in een afvalbak, kunnen op afstand worden uitgelezen. Een operator kan actie ondernemen, zoals een vuilniswagen op weg sturen. 

* **Kaart van gebied voor overzicht over afval**: Deze tegel maakt gebruik van Azure Maps, die u rechtstreeks in Azure IoT Central kunt configureren. Op de tegel met de kaart wordt de locatie van het apparaat weergegeven. Beweeg de muisaanwijzer over de kaart en probeer de besturingselementen op de kaart uit, zoals inzoomen, uitzoomen of uitbreiden.

     ![Schermopname van de kaart op het dashboard van de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Vulling, geur, staafdiagram voor gewichtsniveau**: U kunt telemetriegegevens van een of meer soorten apparaten in een staafdiagram visualiseren. U kunt het staafdiagram ook uitbreiden.  

  ![Schermopname van het staafdiagram op het dashboard van de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services**: Het dashboard bevat een koppeling waarmee u Dynamics 365 Field Services vanuit de Azure IoT Central-toepassing kunt integreren. U kunt bijvoorbeeld Field Services gebruiken om tickets te maken voor het uit laten rijden van vuilnisauto's. 


### <a name="customize-the-dashboard"></a>Het dashboard aanpassen 

U kunt het dashboard aanpassen door op het menu **Bewerken** te klikken. Vervolgens kunt u nieuwe tegels toevoegen of bestaande tegels configureren. Het dashboard zoals het eruitziet in de bewerkingsmodus: 

![Schermopname van het dashboard van de sjabloon voor verbonden afvalbeheer in de bewerkingsmodus.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

U kunt ook **+ Nieuw** selecteren om een nieuw dashboard te maken en helemaal opnieuw te configureren. U kunt meerdere dashboards hebben en u kunt tussen dashboards schakelen via het dashboardmenu. 

## <a name="explore-the-device-template"></a>De apparaatsjabloon verkennen

Via een apparaatsjabloon in Azure IoT Central worden de mogelijkheden van een apparaat gedefinieerd. Dat kunnen telemetrische gegevens, eigenschappen of opdrachten zijn. Als ontwikkelaar kunt u apparaatsjablonen definiëren die de mogelijkheden weergeven van de apparaten die u wilt verbinden. 

De toepassing voor verbonden afvalbeheer wordt geleverd met een voorbeeld van een apparaatsjabloon voor een verbonden afvalbak.

De apparaatsjabloon weergeven:

1. In Azure IoT Central selecteert u **Apparaatsjablonen** in het linkerdeelvenster van de app. 

    ![Schermopname van de lijst met apparaatsjablonen in de app.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. In de lijst **Apparaatsjablonen** selecteert u **Verbonden afvalbak**.

1. Bekijk de mogelijkheden van de apparaatsjabloon. U ziet dat er sensoren zijn gedefinieerd, zoals voor het **vulniveau**, een **geurmeter**, voor het **gewicht** en de **locatie**.

   ![Schermopname met de details van de aangesloten apparaatsjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Het apparaatsjabloon aanpassen

Pas het volgende aan:
1. Selecteer **Aanpassen** in het menu van de apparaatsjabloon.
1. Zoek het telemetrietype **Geurmeter**.
1. Werk de **weergavenaam** van de **geurmeter** bij naar het **geurniveau**.
1. Werk de maateenheid bij of stel de **minimumwaarde** en **maximumwaarde** in.
1. Selecteer **Opslaan**. 

### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen 

U doet dit als volgt:
1. Selecteer **Cloudeigenschap** in het menu van de apparaatsjabloon.
1. Selecteer **+ Cloudeigenschap toevoegen**. In Azure IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat, maar die naar verwachting niet door een apparaat wordt verzonden. Bijvoorbeeld: een cloudeigenschap kan een drempelwaarde voor een waarschuwing zijn die specifiek is voor een installatiegebied, voor bepaalde gegevens over activa of voor onderhoudsgegevens. 
1. Selecteer **Opslaan**. 
 
### <a name="views"></a>Weergaven 
De apparaatsjabloon voor verbonden afvalbakken bevat vooraf gedefinieerde weergaven. Bekijk de weergaven en werk deze zo nodig bij. De weergaven bepalen hoe gebruikers de gegevens van het apparaat zien en hoe ze cloudeigenschappen invoeren. 

  ![Schermopname van de sjabloonweergaven van de apparaatsjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 

Publiceer de apparaatsjabloon indien u wijzigingen hebt aangebracht. 

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 

Selecteer **+ Nieuw** en volg de stappen als u een nieuwe apparaatsjabloon wilt maken. U kunt zelf een volledig nieuwe apparaatsjabloon maken of een apparaatsjabloon uit de Azure-apparaatcatalogus kiezen. 

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In Azure IoT Central kunt u gesimuleerde apparaten maken om uw apparaatsjabloon en toepassing te testen. 

Voor de toepassing voor verbonden afvalbeheer zijn twee gesimuleerde apparaten gekoppeld aan de apparaatsjabloon voor een verbonden afvalbak. 

### <a name="view-the-devices"></a>De apparaten weergeven

1. Selecteer **Apparaat** in het linkerdeelvenster van Azure IoT Central. 

   ![Schermopname van de apparaten van de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Selecteer het apparaat **Verbonden afvalbak**.  

     ![Schermopname van de apparaateigenschappen van de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Ga naar het tabblad **Cloudeigenschappen**. Werk de **drempelwaarde voor de waarschuwing bij een volle afvalbak** bij van **95** naar **100**. 

Bekijk de tabbladen **Apparaateigenschappen** en **Apparaatdashboard**. 

> [!NOTE]
> Alle tabbladen zijn geconfigureerd op basis van de weergaven op de apparaatsjabloon.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

U kunt nieuwe apparaten toevoegen door op het tabblad **Apparaten** **+ Nieuw** te selecteren. 

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central kunt u regels maken om de telemetrische gegevens van apparaten automatisch te bewaken en acties te activeren als aan een of meer voorwaarden wordt voldaan. De acties kunnen onder andere het verzenden van e-mailmeldingen zijn, het activeren van een actie in Power Automate of een webhookactie voor het verzenden van gegevens naar andere services.

De toepassing Verbonden afvalbeheer bevat vier voorbeeldregels.

### <a name="view-rules"></a>Regels weergeven
1. Selecteer **Regels** in het linkerdeelvenster van Azure IoT Central.

   ![Schermopname van de regels in de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Selecteer **Waarschuwing bij volle afvalbak**.

     ![Schermopname van de waarschuwing bij volle afvalbak.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. De **waarschuwing bij volle afvalbak** controleert op de volgende voorwaarde: **Het vulniveau is groter dan of gelijk aan de drempelwaarde voor Waarschuwing bij volle afvalbak**.

    De drempelwaarde voor **Waarschuwing bij volle afvalbak** is een cloudeigenschap die is gedefinieerd in de apparaatsjabloon voor de verbonden afvalbak. 

Nu gaan we een e-mailactie maken.

### <a name="create-an-email-action"></a>Een e-mailactie maken

In lijst **Acties** van de regel kunt u een e-mailactie configureren:
1. Selecteer **+ E-mail**. 
1. Bij **Weergavenaam** voert u **Waarschuwing voor hoge pH** in.
1. Bij **Aan** voert u het e-mailadres in dat is gekoppeld aan uw Azure IoT Central-account. 
1. Voer eventueel een opmerking in die u wilt opnemen in de tekst van het e-mailbericht.
1. Selecteer **Gereed** > **Opslaan**. 

U ontvangt voortaan een e-mail wanneer aan de geconfigureerde voorwaarde wordt voldaan.

>[!NOTE]
>Telkens wanneer er aan een voorwaarde wordt voldaan, verzendt de toepassing een e-mail. Schakel de regel uit om het ontvangen van e-mail via de automatische regel te stoppen. 
  
Als u een nieuwe regel wilt maken, selecteert u in het linkerdeelvenster van **Regels** de optie **+ Nieuw**.

## <a name="configure-jobs"></a>Taken configureren

In Azure IoT Central kunt u met taken het bijwerken van apparaat- of cloudeigenschappen op meerdere apparaten activeren. U kunt taken ook gebruiken om apparaatopdrachten op meerdere apparaten te activeren. Azure IoT Central zorgt ervoor dat deze werkstroom voor u wordt geautomatiseerd. 

1. In het linkerdeelvenster van Azure IoT Central selecteert u **Taken**. 
1. Selecteer **+ Nieuw** en configureer een of meer taken. 

## <a name="customize-your-application"></a>Uw toepassing aanpassen 

Als ontwikkelaar kunt u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

### <a name="change-the-application-theme"></a>Het toepassingsthema wijzigen

U doet dit als volgt:
1. Ga naar **Beheer** > **Uw toepassing aanpassen**.
1. Selecteer **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **logo voor de app**.
1. Selecteer **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **browserpictogram** (een afbeelding die wordt weergegeven op browsertabbladen).
1. U kunt de standaardkleuren van de browser ook vervangen door hexadecimale HTML-kleurcodes toe te voegen. Gebruik hiervoor de velden **Header** en **Accent**.

   ![Schermopname van Uw toepassing aanpassen in de sjabloon voor verbonden afvalbeheer.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. U kunt ook de afbeeldingen voor de toepassing wijzigen. Selecteer **Beheer** > **Toepassingsinstellingen** > **Afbeelding selecteren** om een afbeelding te kiezen die u als de afbeelding van de toepassing wilt uploaden.
1. Ten slotte kunt u ook het thema wijzigen door boven aan de toepassing **Instellingen** te selecteren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de toepassing via de volgende stappen:

1. Selecteer **Beheer** in het linkerdeelvenster van uw Azure IoT Central-toepassing.
1. Selecteer **Toepassingsinstellingen** > **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbonden afvalbeheerconcepten](./concepts-connectedwastemanagement-architecture.md)
