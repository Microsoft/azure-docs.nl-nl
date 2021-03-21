---
title: 'Zelfstudie: een in-store analytics-toepassing maken in Azure IoT Central'
description: In deze zelfstudie leert u hoe u een in-store analytics-toepassing voor de detailhandel maakt in IoT Central. U maakt de toepassing, past deze aan en voegt sensorapparaten toe.
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
ms.openlocfilehash: caa61170504c3ee4b7162cc06db4df16d1232395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014363"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Zelfstudie: een in-store analytics-toepassing maken in Azure IoT Central

In de zelfstudie leren bouwers van oplossingen hoe ze een in-store analytics-toepassing kunnen maken in Azure IoT Central. De voorbeeldtoepassing is voor een winkel. Het is een oplossing voor de algemene bedrijfsbehoefte van het bewaken van en het inspelen op aanwezigheids- en omgevingsomstandigheden.

De voorbeeldtoepassing die u bouwt, maakt gebruik van drie echte apparaten: een Rigado Cascade 500-gateway en twee RuuviTag-sensoren. In de zelfstudie ziet u ook hoe u de gesimuleerde aanwezigheidssensor die is opgenomen in de toepassingssjabloon gebruikt voor testdoeleinden. De Rigado C500-gateway fungeert als de communicatiehub in uw toepassing. De gateway communiceert met sensoren in uw winkel en beheert de verbindingen van de sensoren met de cloud. De RuuviTag is een omgevingssensor die telemetrie biedt, waaronder temperatuur, luchtvochtigheid en druk. De gesimuleerde aanwezigheidssensor biedt een manier om beweging en aanwezigheid te traceren in de kassagebieden van een winkel. 

Deze zelfstudie bevat instructies voor het verbinden van de Rigado-gateway en RuuviTag-sensoren met uw toepassing. Als u een andere gateway en sensoren hebt, kunt u nog steeds de stappen volgen om uw toepassing te bouwen. In de zelfstudie ziet u ook hoe u gesimuleerde RuuviTag-sensoren maakt. Met de gesimuleerde sensoren kunt u de toepassing toch bouwen als u niet over echte apparaten beschikt. 

Het ontwikkelen van de oplossing voor het bewaken van de kassagebieden en omgeving bestaat uit drie delen:

* De toepassing maken en apparaten verbinden om omstandigheden te controleren
* Het dashboard aanpassen zodat gebruikers apparaten kunnen bewaken en beheren
* Gegevensexport configureren om winkelmanagers in staat te stellen analyses uit te voeren en inzichten te visualiseren

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De sjabloon **In-Store Analytics: betaling** van Azure IoT Central gebruiken om een toepassing voor een winkel te maken
> * De toepassingsinstellingen aanpassen
> * IoT-apparaatsjablonen maken en aanpassen
> * Apparaten verbinden met uw toepassing
> * Regels en acties toevoegen om omstandigheden te controleren

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudiereeks hebt u het volgende nodig:
* Een Azure-abonnement wordt aanbevolen. U kunt eventueel een gratis proefversie van zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).
* Toegang tot een gatewayapparaat en twee omgevingssensoren (u kunt eventueel gesimuleerde apparaten gebruiken zoals wordt beschreven in de zelfstudie)
* Apparaatsjablonen voor de apparaten die u gebruikt (er zijn sjablonen beschikbaar voor alle apparaten die in de zelfstudie worden gebruikt)

## <a name="create-an-application"></a>Een app maken
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de rest van de zelfstudiereeks om een volledige oplossing te ontwikkelen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Navigeer naar de website [Azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral).

1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties van dit abonnement. U kunt zich ook aanmelden met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **Detailhandel**.  De detailhandelspagina toont verschillende toepassingssjablonen voor detailhandels.

Een nieuwe in-store analytics-toepassing voor afrekenen maken:  

1. Selecteer de toepassingssjabloon **In-Store Analytics: betaling**. Deze sjabloon bevat apparaatsjablonen voor alle apparaten die in de zelfstudie worden gebruikt, behalve voor de RuuviTag-sensoren. De sjabloon biedt ook een gebruikersdashboard voor het bewaken van de afreken- en omgevingsomstandigheden, en de apparaatstatus. 

1. Kies desgewenst een beschrijvende **Toepassingsnaam**. Deze toepassing is gebaseerd op een fictieve winkel met de naam Contoso. In de zelfstudie wordt *Contoso checkout* gebruikt als de **toepassingsnaam**. De toepassingssjabloon is gebaseerd op het fictieve bedrijf Northwind. In deze zelfstudie gebruikt u Contoso om te leren hoe u de toepassing aanpast.

    > [!NOTE]
    > Als u een beschrijvende **toepassingsnaam** gebruikt, moet u nog steeds een unieke waarde gebruiken voor **URL**.

1. Als u een Azure-abonnement hebt, voert u uw *map, Azure-abonnement en locatie* in. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen.  

    Zie de [snelstart over het maken van een toepassing](../core/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken**.

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Factureringsgegevens voor maken van toepassing in Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Toepassingsinstellingen aanpassen

Als ontwikkelaar kunt u verschillende instellingen wijzigen om de gebruikerservaring in uw toepassing aan te passen. In dit gedeelte selecteert u een vooraf gedefinieerd toepassingsthema. U leert ook hoe u een aangepast thema maakt en hoe u de afbeelding van de toepassing bijwerkt. Met een aangepast thema kunt u de kleuren van de toepassingsbrowser instellen, evenals het browserpictogram en het toepassingslogo die worden weergegeven in het impressum.

Een vooraf gedefinieerd toepassingsthema selecteren:

1. Selecteer **Instellingen** in het impressum.

    ![Instellingen voor IoT Central-toepassing](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Selecteer een nieuw thema in de vervolgkeuzelijst **Thema**.

3. Selecteer **Opslaan**.

In plaats van een vooraf gedefinieerd thema te gebruiken, kunt u ook een aangepast thema maken. Als u een aantal voorbeeldafbeeldingen wilt gebruiken om de toepassing aan te passen en de zelfstudie te volt ooien, downloadt u de [Contoso-voorbeeldafbeeldingen](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Een aangepast thema maken:

1. Vouw het linkerdeelvenster uit als dat nog niet is gebeurd.

    ![Linkerdeelvenster van Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Selecteer **Beheer > Uw toepassing aanpassen**.

1. Gebruik de knop **Wijzigen** om een afbeelding te kiezen die u wilt uploaden als het **impressumlogo**. Geef desgewenst een waarde op voor **Alternatieve tekst van logo**. 

1. Gebruik de knop **Wijzigen** om een afbeelding voor het **browserpictogram** te kiezen dat op de tabbladen van de browser wordt weergegeven.

1. U kunt de **browserkleuren** eventueel vervangen door hexadecimale HTML-kleurcodes toe te voegen. Typ in het vak **Header** de waarde *#008575*.  Typ in het vak **Accent** de waarde *#A1F3EA*. 

1. Selecteer **Opslaan**. 

    ![Aangepast logo van Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Als u de wijzigingen hebt opgeslagen, worden de browserkleuren, het logo in het impressum en het browserpictogram bijgewerkt. 

    ![Bijgewerkte instellingen voor IoT Central-toepassing](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

De afbeelding voor de toepassing bijwerken:

1. Selecteer **Beheer > Toepassingsinstellingen**.

1. Gebruik de knop **Afbeelding selecteren** om een afbeelding te kiezen die u wilt uploaden als de toepassingsafbeelding. Deze afbeelding wordt weergegeven op de tegel van de toepassing op de pagina **Mijn apps** van de beheersite van IoT Central.

1. Selecteer **Opslaan**.

1. Ga eventueel naar de pagina **Mijn apps** van de [beheersite van IoT Central](https://aka.ms/iotcentral). Op de tegel van de toepassing wordt de bijgewerkte afbeelding van de toepassing weergegeven.

    ![Afbeelding van toepassing aanpassen in Azure IoT Central](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Apparaatsjablonen maken
Als ontwerper kunt u apparaatsjablonen maken waarmee u en de gebruikers van de toepassing apparaten kunnen configureren en beheren. U kunt een sjabloon maken door een aangepaste sjabloon te maken, een bestaand sjabloonbestand importeren of door een sjabloon te importeren uit de apparaatcatalogus van Azure IoT. Nadat u een apparaatsjabloon hebt gemaakt en aangepast, kunt u deze gebruiken om echte apparaten te verbinden met uw toepassing. U kunt een apparaatsjabloon ook gebruiken om gesimuleerde apparaten te genereren voor testdoeleinden.

De toepassingssjabloon **In-Store Analytics: betaling** bevat apparaatsjablonen voor verschillende apparaten.  Er zijn apparaatsjablonen voor twee van de drie apparaten die u in de toepassing gebruikt. De apparaatsjabloon voor de RuuviTag-sensoren is niet opgenomen in de toepassingssjabloon **In-Store Analytics: betaling**. In dit gedeelte voegt u een apparaatsjabloon voor de RuuviTag-sensoren toe aan uw toepassing.

Een sjabloon voor RuuviTag-apparaten toevoegen aan de toepassing:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster.

1. Selecteer **+ Nieuw** om een nieuwe apparaatsjabloon te maken.

1. Zoek en selecteer de apparaatsjabloon voor de **RuuviTag**-sensor in de apparaatcatalogus van Azure IoT. 

1. Selecteer **Volgende: Aanpassen**.

    ![Schermopname waarin de knop Volgende: aanpassen is gemarkeerd.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Selecteer **Maken**. De toepassing voegt de RuuviTag-apparaatsjabloon toe.

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster. De pagina bevat alle apparaatsjablonen die in de toepassingssjabloon zijn opgenomen, plus de RuuviTag-apparaatsjabloon die u zojuist hebt toegevoegd.

    ![Apparaatsjabloon van Azure IoT Central voor RuuviTag-sensor](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Apparaatsjablonen aanpassen
U kunt de apparaatsjablonen in uw toepassing op drie manieren aanpassen. Zo kunt u de ingebouwde interfaces van apparaten aanpassen door de mogelijkheden van het apparaat te wijzigen. In het geval van een temperatuursensor kunt u bijvoorbeeld gegevens wijzigen zoals de weergavenaam van de temperatuurinterface, het gegevenstype, de maateenheden. en de minimale en maximale werkingsbereiken. 

Een andere manier is het aanpassen van uw apparaatsjablonen door cloudeigenschappen toe te voegen. Cloudeigenschappen maken geen deel uit van de ingebouwde mogelijkheden van een apparaat. Cloudeigenschappen zijn aangepaste gegevens die door uw Azure IoT Central-toepassing worden gemaakt, opgeslagen en gekoppeld aan uw apparaten. Een voorbeeld van een cloudeigenschap is een berekende waarde of metagegevens zoals een locatie die u wilt koppelen aan een set apparaten. 

De derde en laatste manier is het aanpassen van apparaatsjablonen door aangepaste weergaven te maken. Weergaven zijn een hulpmiddel voor gebruikers om telemetrie en metagegevens voor uw apparaten te visualiseren, zoals metrische gegevens van apparaten en hun status.

Hier gaat u de eerste twee methoden gebruiken om de apparaatsjabloon voor de RuuviTag-sensoren aan te passen. Informatie over het maken van weergaven voor uw sensoren vindt u in de zelfstudie [Add a simulated device to your IoT Central application](../core/quick-create-simulated-device.md) (Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing).

De ingebouwde interfaces van de sjabloon voor RuuviTag-apparaten aanpassen:

1. Selecteer **Apparaatsjablonen** in het linkerdeelvenster. 

1. Selecteer de sjabloon voor RuuviTag-sensoren. 

1. Verberg het linkernavigatiegedeelte. Hier ziet u een samenvatting van de sjabloon met de kenmerken van het apparaat.

    ![Samenvatting van apparaatsjabloon van Azure IoT Central voor RuuviTag-sensoren](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Selecteer **Aanpassen** in het menu van de sjabloon voor RuuviTag-apparaten. 

1. Scrol in de lijst met mogelijkheden en zoek het type telemetrie `humidity` . Dit is het rij-item met de bewerkbare **weergavenaam** *humidity*.

In de volgende stappen past u het type telemetrie `humidity` voor de RuuviTag-sensoren aan. Desgewenst kunt u ook enkele van de andere telemetrietypen aanpassen.

Breng de volgende wijzigingen aan voor het type telemetrie `humidity`:

1. Selecteer het besturingselement **Uitvouwen** om de schemadetails voor de rij uit te vouwen.

1. Wijzig de **weergavenaam** van *humidity* in een aangepaste waarde, zoals *Relative humidity*.

1. Wijzig de waarde voor **Semantisch type** van *Geen* in *Humidity*.  Stel eventueel schemawaarden in voor het type telemetrie Humidity in de uitgevouwen schemaweergave. Met behulp van schema-instellingen kunt u gedetailleerde validatievereisten definiëren voor de gegevens die door de sensoren worden gemeten. U kunt bijvoorbeeld minimum- en maximumwaarden instellen voor het werkingsbereik voor een bepaalde interface.

1. Selecteer **Opslaan** om uw wijzigingen op te slaan.

    ![Schermopname van het scherm Aanpassen, met de knop Opslaan gemarkeerd.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Een cloudeigenschap toevoegen aan een apparaatsjabloon in uw toepassing:

1. Selecteer **Cloudeigenschappen** in het menu van de sjabloon voor RuuviTag-apparaten.

1. Selecteer **Een cloudeigenschap toevoegen**. 

Geef de volgende waarden op om een aangepaste eigenschap te maken voor het opslaan van de locatie van elk apparaat:

1. Voer de waarde *Location* in voor **Weergavenaam**. Deze waarde wordt automatisch gekopieerd naar het veld **Naam** en vormt een beschrijvende naam voor de eigenschap. U kunt de gekopieerde waarde gebruiken of deze wijzigen.

1. Selecteer *String* in de vervolgkeuzelijst **Schema**. Met dit type kunt u een locatienaam koppelen aan elk apparaat op basis van de sjabloon. U kunt zo bijvoorbeeld een gebied in een winkel koppelen aan elk apparaat. U kunt desgewenst de waarde voor **Semantisch type** van de eigenschap instellen op *Location*. Hierdoor **schema** automatisch ingesteld op *Geopoint*. U kunt dan GPS-coördinaten koppelen aan een apparaat. 

1. Stel **Minimumlengte** in op *2*. 

1. Stel **Spaties knippen** in op **Aan**.

1. Selecteer **Opslaan** om de aangepaste cloudeigenschap op te slaan.

    ![Aanpassing van apparaatsjabloon van Azure IoT Central voor RuuviTag-sensoren](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Selecteer **Publiceren**. 

    Als u een apparaatsjabloon publiceert, wordt deze zichtbaar voor gebruikers van de toepassing. Nadat u een sjabloon hebt gepubliceerd, kunt u deze gebruiken om gesimuleerde apparaten te genereren voor testdoeleinden of om echte apparaten te verbinden met uw toepassing. Als er al apparaten zijn verbonden met uw toepassing, heeft het publiceren van een aangepaste sjabloon tot gevolg dat de wijzigingen naar de apparaten worden gepusht.

## <a name="add-devices"></a>Apparaten toevoegen
Nadat u apparaatsjablonen hebt gemaakt en aangepast, is het tijd om apparaten toe te voegen. 

Voor deze zelfstudie gebruikt u de volgende set echte en gesimuleerde apparaten om de toepassing te bouwen:
- Een echte Rigado C500-gateway
- Twee echte RuuviTag-sensoren
- Een gesimuleerde aanwezigheidssensor (**Occupancy**). De gesimuleerde sensor is opgenomen in de toepassingssjabloon, dus u hoeft die niet te maken. 

> [!NOTE]
> Als u geen echte apparaten hebt, kunt u deze zelfstudie voltooien door gesimuleerde RuuviTag-sensoren te maken. De volgende instructies bevatten stappen voor het maken van een gesimuleerde RuuviTag. U hoeft geen gesimuleerde gateway te maken.

Volg de stappen in de volgende twee artikelen om een echte Rigado-gateway en echte RuuviTag-sensoren te verbinden. Als u daarmee klaar bent, keert u terug naar deze zelfstudie. Omdat u al apparaatsjablonen hebt gemaakt in deze zelfstudie, hoeft u deze niet opnieuw te maken in de volgende reeks instructies.

- Als u een Rigado-gateway wilt verbinden, raadpleegt u [Connect a Rigado Cascade 500 gateway device to your Azure IoT Central application](../core/howto-connect-rigado-cascade-500.md) (Een Rigado Cascade 500-gateway verbinden met uw Azure IoT Central-toepassing).
- Als u een RuuviTag-sensoren wilt verbinden, raadpleegt u [Connect a RuuviTag sensor to your Azure IoT Central application](../core/howto-connect-ruuvi.md) (Een RuuviTag-sensor verbinden met uw Azure IoT Central-toepassing). U kunt deze instructies ook gebruiken om twee gesimuleerde sensoren te maken, indien nodig.

## <a name="add-rules-and-actions"></a>Regels en acties toevoegen
Als onderdeel van het gebruik van sensoren in uw Azure IoT Central-toepassing om omstandigheden te bewaken, kunt u regels maken om acties uit te voeren wanneer aan bepaalde voorwaarden wordt voldaan. Een regel wordt gekoppeld aan een apparaatsjabloon en een of meer apparaten en bevat voorwaarden waaraan moet worden voldaan op basis van telemetrie van apparaten of gebeurtenissen. Een regel heeft ook een of meer gekoppelde acties. Voorbeelden van acties zijn het verzenden van meldingen per e-mail en het activeren van een webhookactie voor het verzenden van gegevens naar andere services. De toepassingssjabloon **In-Store Analytics: betaling** bevat enkele vooraf gedefinieerde regels voor de apparaten in de toepassing.

In dit gedeelte maakt u een nieuwe regel waarmee het maximale relatieve luchtvochtigheidsniveau wordt gecontroleerd op basis van de telemetrie van de RuuviTag-sensor. U voegt een actie toe aan de regel, zodat bij overschrijding van de limiet een e-mail wordt verzonden met de toepassing. 

Een regel maken: 

1. Vouw het linkerdeelvenster uit.

1. Selecteer **Regels**.

1. Selecteer **+ Nieuw**.

1. Voer *Humidity level* in als de naam van de regel. 

1. Kies de sjabloon voor RuuviTag-sensoren bij **Apparaatsjabloon**. De regel die u definieert, wordt toegepast op alle sensoren die zijn gebaseerd op die sjabloon. U kunt eventueel ook een filter maken waarmee de regel alleen wordt toegepast op een gedefinieerde subset van de sensoren. 

1. Kies `Relative humidity` bij **Telemetrie**. Dit is de apparaatmogelijkheid die u in een vorige stap hebt aangepast.

1. Kies `Is greater than` bij **Operator**. 

1. Voer bij **Waarde** een bovenlimiet in voor het luchtvochtigheidsniveau voor uw omgeving. Geef bijvoorbeeld *65* op. U hebt een voorwaarde ingesteld voor de regel die zich voordoet wanneer de relatieve vochtigheid in een echte of gesimuleerde RuuviTag-sensor deze waarde overschrijdt. Mogelijk moet u de waarde naar boven of beneden bijstellen, afhankelijk van het normale luchtvochtigheidsbereik in uw omgeving.  

   ![Regelvoorwaarden toevoegen in Azure IoT Central](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Een actie toevoegen aan de regel:

1. Selecteer **+ E-mailadres**. 

1. Voer *High humidity notification* in voor **Weergavenaam**. 

1. Voer in **Tot** het e-mailadres in dat is gekoppeld aan uw account. Als u een ander e-mailadres gebruikt, moet het adres zijn gekoppeld aan een gebruiker die aan de toepassing is toegevoegd. De gebruiker moet zich ook ten minste één keer aan- en afmelden.

1. Voer eventueel een opmerking in die u als bericht wilt toevoegen aan het e-mailbericht.

1. Selecteer **Gereed** om de actie te voltooien.

   ![Acties toevoegen aan regels in Azure IoT Central](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Selecteer **Opslaan** om de nieuwe regel op te slaan en te activeren. 

    Binnen een paar minuten moet het opgegeven e-mailaccount e-mails gaan ontvangen. De toepassing verstuurt een e-mail telkens wanneer een sensor aangeeft dat het vochtigheidsniveau hoger is dan de waarde in uw voorwaarde.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* De sjabloon **In-Store Analytics: betaling** van Azure IoT Central gebruiken om een toepassing voor een winkel te maken
* De toepassingsinstellingen aanpassen
* IoT-apparaatsjablonen maken en aanpassen
* Apparaten verbinden met uw toepassing
* Regels en acties toevoegen om omstandigheden te controleren

Nu u in Azure IoT Central een toepassing voor het bewaken van omstandigheden hebt gemaakt, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Het dashboard van de operator aanpassen](./tutorial-in-store-analytics-customize-dashboard.md)
