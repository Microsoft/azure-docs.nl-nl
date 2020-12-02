---
title: 'Zelfstudie: Een app voor verbonden afvalbeheer maken met Azure IoT Central'
description: 'Zelfstudie: Leer hoe u een toepassing voor verbonden afvalbeheer maakt met Azure IoT Central-toepassingssjablonen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0a7f5ec0231612a2cee2a9435144c886527c228e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014472"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Zelfstudie: Een toepassing voor verbonden afvalbeheer maken in Azure IoT Central



Deze zelfstudie begeleidt u bij het maken van een Azure IoT Central-toepassing voor verbonden afvalbeheer met behulp van de toepassingssjabloon **Verbonden afvalbeheer** van IoT Central. 

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]

> * Het Azure IoT Central-sjabloon **Verbonden afvalbeheer** gebruiken om uw toepassing Verbonden afvalbeheer te maken
> * Het dashboard van de operator verkennen en aanpassen 
> * Het apparaatsjabloon voor een verbonden prullenbak verkennen
> * Gesimuleerde apparaten verkennen
> * Regels verkennen en configureren
> * Taken configureren
> * De huisstijl van uw toepassing aanpassen met behulp van whitelabeling

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:
-  Een Azure-abonnement wordt aanbevolen. U kunt eventueel een gratis proefversie van zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Een app voor verbonden afvalbeheer maken in Azure IoT Central

In deze sectie gaan u het Azure IoT Central-sjabloon **Verbonden afvalbeheer** gebruiken om uw toepassing Verbonden afvalbeheer te maken in IoT Central.

Als u een nieuwe app voor verbonden afvalbeheer wilt maken in Azure IoT Central, gaat u als volgt te werk:  

1. Ga naar de [startpagina van Azure IoT Central](https://aka.ms/iotcentral).

    Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om dit te openen. U kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klik op **Bouwen** in het linkerdeelvenster en selecteer het tabblad **Overheid**. Op de overheidspagina worden verschillende sjablonen voor overheidstoepassingen weergegeven.

    ![Sjablonen voor overheids-apps ontwikkelen](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Selecteer de toepassingssjabloon **Verbonden afvalverwerking**. Deze sjabloon bevat voorbeelden zoals een apparaatsjabloon voor een verbonden prullenbak, een gesimuleerd apparaat, een operatordashboard en vooraf geconfigureerde bewakingsregels.    

2. Klik op **App maken**. Hiermee wordt het aanmaakformulier **Nieuwe toepassing** geopend, waarin de volgende velden zijn te vinden:
    * **Toepassingsnaam**. De toepassing gebruikt standaard *Verbonden afvalbeheer* gevolgd door een unieke id-tekenreeks die door IoT Central wordt gegenereerd. Kies desgewenst een beschrijvende toepassingsnaam. U kunt de toepassingsnaam ook later nog wijzigen.
    * **URL-** : u kunt als u dat wilt hier de door u gewenste URL kiezen. U kunt de URL later ook nog wijzigen. 
    * Als u een Azure-abonnement hebt, voert u uw *directory, Azure-abonnement en regio in*. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

5. Klik op de knop **Maken** onder aan de pagina. 

    ![Azure IoT Central-pagina Toepassing Verbonden afvalbeheer maken](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central: verbonden factureringsgegevens maken](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. U hebt nu een Verbonden afvalbeheer-app gemaakt met behulp van de Azure IoT Central-sjabloon **Verbonden afvalbeheer**. 

Gefeliciteerd! Op uw zojuist gemaakte toepassing is het volgende vooraf geconfigureerd:
* Voorbeelden van operatordashboards
* Voorbeelden van vooraf gedefinieerde apparaatsjablonen voor verbonden prullenbakken
* Apparaten voor gesimuleerde gekoppelde prullenbakken
* Vooraf geconfigureerde regels en taken
* Voorbeeld van een huisstijl waarbij whitelabeling is gebruikt 

Het is uw toepassing en u kunt deze op elk gewenst moment wijzigen. We gaan nu de toepassing verkennen en enkele aanpassingen aanbrengen.  

## <a name="explore-and-customize-operator-dashboard"></a>Het dashboard van de operator verkennen en aanpassen 
Nadat u de toepassing hebt gemaakt, komt u nu terecht in het **Wide World Waste-dashboard voor verbonden afvalbeheer**.

   ![Dashboard Verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als ontwikkelaar kunt u weergaven in het operatordashboard maken en aanpassen. Voordat u met aanpassen gaat beginnen, moet u eerst even kennismaken met het dashboard. 

> [!NOTE]
> Alle gegevens die in het dashboard worden weergegeven, zijn gebaseerd op gesimuleerde apparaatgegevens. Dit komt in de volgende sectie aan de orde. 

Het dashboard bestaat uit verschillende soorten tegels:

* ***Tegel met een afbeelding van het Wide World Waste-hulpprogramma**: de eerste tegel in het dashboard is een tegel met een afbeelding van een fictief hulpprogramma voor afvalbeheer met de naam 'Wide World Waste'. U kunt de tegel aanpassen en uw afbeelding gebruiken of deze verwijderen. 

_***Tegel met afbeelding van prullenbak** _: u kunt tegels met afbeeldingen en inhoud gebruiken om het apparaat dat wordt bewaakt, samen met een beschrijvende tekst, visueel weer te geven. 

_***KPI-tegel met vulniveau** _: de tegel geeft een waarde weer die door een _vulniveausensor_* in een prullenbak wordt gerapporteerd. *Vulniveausensoren* en andere sensoren zoals een *geurmeter* of *gewichtssensor* in een prullenbak kunnen op afstand worden bewaakt. Een operator kan actie ondernemen, zoals de vuilniswagen erop uitsturen om het vuil op te halen. 

*  ***Omgevingskaart voor afvalbewaking**: de kaart maakt gebruik van Azure Maps, die u rechtstreeks in Azure IoT Central kunt configureren. Op de tegel met de kaart wordt de locatie van het apparaat weergegeven. Beweeg de muisaanwijzer over de kaart en probeer de besturingselementen op de kaart uit, zoals inzoomen, uitzoomen of uitbreiden.

     ![Dashboardkaart Verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


_***Staafdiagram dat vulniveau, geur, gewicht aangeeft**: u kunt de telemetriegegevens van een of meer apparaten in een staafdiagram visualiseren. U kunt het staafdiagram ook uitbreiden.  

  ![Staafdiagram op dashboard Verbonden afvalbeheer](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Inhoudstegel van Field Services**: het dashboard bevat koppelingen die u helpen vanuit de Azure IoT Centra-toepassing met Dynamics 365 Field Services te integreren. U kunt bijvoorbeeld Field Services gebruiken om tickets te maken voor het uit laten rijden van vuilnisauto's. 


### <a name="customize-dashboard"></a>Dashboard aanpassen 

Als ontwikkelaar kunt u weergaven in het operatordashboard aanpassen. U kunt het volgende doen:
1. Klik op **Bewerken** om het **dashboard Verbonden afvalbeheer Wide World Waste** aan te passen. U kunt het dashboard aanpassen door op het menu **Bewerken** te klikken. Zodra het dashboard in de **bewerkingsmodus** is , kunt u nieuwe tegels toevoegen of configureren 

    ![Dashboard bewerken](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. U kunt ook op **+ Nieuw** klikken om een nieuw dashboard te maken en helemaal opnieuw te configureren. U kunt meerdere dashboards hebben en u kunt van het ene naar het andere dashboard navigeren via het dashboardmenu. 

## <a name="explore-connected-waste-bin-device-template"></a>Het apparaatsjabloon voor een verbonden prullenbak verkennen

Via een apparaatsjabloon in Azure IoT Central worden de mogelijkheden van een apparaat gedefinieerd, dat kunnen telemetrische gegevens, eigenschappen of opdrachten zijn. Als ontwikkelaar kunt u apparaatprofielen definiëren die de mogelijkheden vertegenwoordigen van de apparaten die u wilt verbinden. 
 

De **Verbonden afvalbeheer**-toepassing wordt geleverd met een voorbeeld van een apparaatsjabloon voor een verbonden prullenbak.

De apparaatsjabloon weergeven:

1. Klik op **Apparaatsjablonen** in het linkerdeelvenster van uw toepassing in IoT Central. 

    ![Schermopname van de lijst met apparaatinstellingen in de toepassing](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. In de lijst met apparaatsjablonen ziet u **Gekoppelde prullenbak**. Open deze door op de naam ervan te klikken.

3. Maak u vertrouwd met de mogelijkheden die de apparaatsjabloon heeft te bieden. U kunt zien dat hier sensoren zoals voor het *vulniveau*, een *geurmeter*, voor het *gewicht*, de *locatie* en andere zijn gedefinieerd.

   ![Schermopname met de details van de aangesloten apparaatsjabloon voor prullenbakken](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>De apparaatsjabloon aanpassen

Pas het volgende aan:
1. Ga naar **aanpassen** vanuit het menu van de apparaatsjabloon
2. Zoek naar het type telemetriegegevens `Odor meter`
3. Werk de **weergavenaam** bij van `Odor meter` naar `Odor level`
4. U kunt ook de maateenheid bijwerken of de *minimumwaarde* en *maximumwaarde* instellen
5. **Sla** alle eventuele wijzigingen op 

### <a name="add-a-cloud-property"></a>Een cloudeigenschap toevoegen 

1. Ga naar **Cloudeigenschap** vanuit het menu van de apparaatsjabloon
2. Voeg een nieuwe cloudeigenschap toe door op **+ Een cloudeigenschap toevoegen** te klikken. In IoT Central kunt u een eigenschap toevoegen die relevant is voor het apparaat, maar naar verwachting niet door een apparaat wordt verzonden. Een voorbeeld hiervan is als een cloudeigenschap een drempelwaarde voor een waarschuwing is die specifiek voor een installatie, voor bepaalde gegevens over assets of voor onderhoudsinformatie en andere informatie geldt. 
3. **Sla** alle eventuele wijzigingen op 
 
### <a name="views"></a>Weergaven 
* Het apparaatsjabloon voor verbonden prullenbakken bevat vooraf gedefinieerde weergaven. Bekijk de weergaven en u kunt updates maken. De weergaven bepalen hoe operators de gegevens van het apparaat zien, maar ook hoe ze cloudeigenschappen kunnen invoeren. 

  ![Apparaatsjabloonweergaven](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publiceren 

* Als u wijzigingen hebt aangebracht, zorg er dan voor dat u de apparaatsjabloon **Publiceert**. 

### <a name="create-a-new-device-template"></a>Een nieuwe apparaatsjabloon maken 

* Selecteer **+ Nieuw** en volg het proces voor het maken van een apparaatsjabloon. U kunt zelf een volledig nieuwe apparaatsjabloon maken of een apparaatsjabloon uit de Azure-apparaatcatalogus kiezen. 

## <a name="explore-simulated-devices"></a>Gesimuleerde apparaten verkennen

In IoT Central kunt u gesimuleerde apparaten maken om uw apparaatsjabloon en toepassing te testen. 

Voor de **Verbonden afvalbeheer**-toepassing zijn twee gesimuleerde apparaten toegewezen aan de apparaatsjabloon voor een verbonden prullenbak. 

### <a name="to-view-the-devices"></a>De apparaten weergeven:

1. Navigeer naar **Apparaat** vanuit het linkerdeelvenster van IoT Central. 

   ![Apparaten](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Selecteer en klik op het apparaat met de verbonden prullenbak.  

     ![Apparaat 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Ga naar het tabblad **Cloudeigenschappen** en werk de waarde `Bin full alert threshold` bij van `95` naar `100`. 
* Verken het tabblad **Apparaateigenschappen** en het tabblad **Apparaatdashboard**. 

> [!NOTE]
> Houd er rekening mee dat alle tabbladen zijn geconfigureerd vanuit de **apparaatsjabloonweergaven**.

### <a name="add-new-devices"></a>Nieuwe apparaten toevoegen

* U kunt nieuwe apparaten toevoegen door te klikken op **+ Nieuw** op het tabblad **Apparaten**. 

## <a name="explore-and-configure-rules"></a>Regels verkennen en configureren

In Azure IoT Central kunt u regels maken om de telemetrische gegevens van apparaten automatisch te bewaken en acties te activeren die worden ondernomen wanneer aan een of meer voorwaarden wordt voldaan. De acties kunnen onder andere het verzenden van e-mail zijn, het activeren van een Power Automate-actie of een webhookactie voor het verzenden van gegevens naar andere services.

De toepassing **Verbonden afvalbeheer** bevat vier voorbeeldregels.

### <a name="to-view-rules"></a>U kunt de regels als volgt weergeven:
1. Navigeer naar **Regels** vanuit het linkerdeelvenster van IoT Central

   ![Regels](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Selecteer de **Prullenbak vol-waarschuwing**

     ![Prullenbak vol-waarschuwing](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. De `Bin full alert` controleert als de **voorwaarde** `Fill level is greater than or equal to Bin full alert threshold` is.

    De `Bin full alert threshold` is een *cloudeigenschap* die is gedefinieerd in de apparaatsjabloon `Connected waste bin`. 

Nu gaan we een e-mailactie maken.

### <a name="create-an-email-action"></a>Een e-mailactie maken
Een e-mailactie configureren in de actielijst van de regel, gaat als volgt:
1. Selecteer **+ E-mail**. 
2. Voer *waarschuwing bij hoge pH-waarde* in als beschrijvende **weergavenaam** voor de actie.
3. Voer het e-mailadres in dat is gekoppeld aan uw IoT Central account bij **Naar**. 
4. Voer eventueel een opmerking in die u wilt toevoegen in de tekst van het e-mailbericht.
5. Klik op **Gereed** om de actie te voltooien.
6. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

U ontvangt een e-mail wanneer aan de geconfigureerde **voorwaarde** wordt voldaan.

> [!NOTE]
> Telkens wanneer er aan een voorwaarde wordt voldaan, verzendt de toepassing een e-mail. **Schakel** de regel uit om het ontvangen van e-mail via de automatische regel te stoppen. 
  
Een nieuwe regel maken: 
1. Selecteer **+ Nieuw** bij **Regels** in het linkerdeelvenster.

## <a name="configure-jobs"></a>Taken configureren

In IoT Central kunt u met taken het bijwerken van apparaat- of cloudeigenschappen op meerdere apparaten activeren. Naast eigenschappen kunt u taken ook gebruiken om apparaatopdrachten op meerdere apparaten te activeren. IoT Central zorgt dat deze werkstroom wordt geautomatiseerd. 

1. Ga in het linkerdeelvenster naar **Taken**. 
2. Klik op **+ Nieuw** en configureer een of meer taken. 


## <a name="customize-your-application"></a>Uw toepassing aanpassen 

Als ontwikkelaar kunt u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen.

### <a name="to-change-the-application-theme"></a>Het toepassingsthema wijzigen:

1. Ga naar **Beheer > Uw toepassing aanpassen**.
3. Gebruik de knop **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **toepassingslogo**.
4. Gebruik de knop **Wijzigen** om een afbeelding voor het **pictogram van de browser** te kiezen dat op de tabbladen van de browser wordt weergegeven.
5. U kunt de **standaardkleuren van de browser** ook vervangen door hexadecimale HTML-kleurcodes toe te voegen.

   ![Azure IoT Central: uw-toepassing aanpassen](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. U kunt ook afbeeldingen van toepassingen wijzigen door naar de knop **Beheer > Toepassingsinstellingen** en **Afbeelding selecteren** te gaan om een afbeelding te kiezen die u als afbeelding voor de toepassing wilt uploaden.
7. Ten slotte kunt u ook het **thema** wijzigen door te klikken op **Instellingen** in het Impressum van de toepassing.

  
## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de toepassing via de volgende stappen:

1. Open het tabblad Beheer in het linkerdeelvenster van uw IoT Central-toepassing.
2. Selecteer Uw toepassing en klik onder aan de pagina op de knop Verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over
 
> [!div class="nextstepaction"]
> [Verbonden afvalbeheerconcepten](./concepts-connectedwastemanagement-architecture.md)
