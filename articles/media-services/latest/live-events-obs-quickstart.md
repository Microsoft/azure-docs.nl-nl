---
title: Een live stream maken met IB Studio
description: Ontdek hoe u een Azure Media Services-livestream maakt met behulp van de portal en OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956069"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Een Azure Media Services-livestream maken met OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze quickstart helpt u een Azure Media Services-livestream te maken met behulp van de Azure-portal en Open Broadcasting Studio (OBS). Er wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

In deze quickstart komen de volgende onderwerpen aan bod:

- Een on-premises encoder instellen met OBS.
- Een livestream instellen.
- Livestream-uitvoer instellen.
- Een standaardstreaming-eindpunt uitvoeren.
- Azure Media Player gebruiken om de livestream en on-demand uitvoer te bekijken.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Een on-premises encoder instellen met OBS

1. Download OBS voor uw besturingssysteem op de [Open Broadcaster Software-website](https://obsproject.com/) en installeer deze.
1. Start de toepassing en laat deze openstaan.

## <a name="run-the-default-streaming-endpoint"></a>Het standaardstreaming-eindpunt uitvoeren

1. Selecteer **Streaming-eindpunten** in de lijst Media Services.

   ![Het menu-item Streaming-eindpunten](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Als de status van het standaardstreaming-eindpunt ‘gestopt’ is, selecteert u het. Deze stap leidt u naar de pagina voor dat eindpunt.
1. Selecteer **Starten**.

   ![De knop Starten voor het streaming-eindpunt](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Een Azure Media Services-livestream instellen

1. Ga naar het Azure Media Services-account in de portal en selecteer **Live streamen** in de lijst **Media Services**.

   ![De koppeling Live streamen](media/live-events-obs-quickstart/select-live-streaming.png)
1. Selecteer **Livegebeurtenis toevoegen** om een nieuwe livestreamgebeurtenis te maken.

   ![Het pictogram Livegebeurtenis toevoegen](media/live-events-obs-quickstart/add-live-event.png)
1. Typ een naam voor uw nieuwe gebeurtenis, zoals *TestLivegebeurtenis*, in het vak **Naam van de livegebeurtenis**.

   ![Het vak Naam van de livegebeurtenis](media/live-events-obs-quickstart/live-event-name.png)
1. Typ een optionele beschrijving van de gebeurtenis in het vak **Beschrijving**.
1. Selecteer de optie **Passthrough: geen cloudcodering**.

   ![De optie Cloudcodering](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP**.
1. Zorg ervoor dat de optie **Nee** is geselecteerd voor **Livegebeurtenis starten**, om te voorkomen dat u wordt gefactureerd voor de livegebeurtenis voordat deze gereed is. (De facturering begint wanneer de livegebeurtenis is gestart.)

   ![De optie Livegebeurtenis starten](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecteer de knop **Controleren en maken** om de instellingen te controleren.
1. Selecteer de knop **Maken** om de livegebeurtenis te maken. U wordt dan teruggebracht naar de lijst met livegebeurtenissen.
1. Selecteer de koppeling naar de livegebeurtenis die u zojuist hebt gemaakt. Merk op dat uw gebeurtenis is gestopt.
1. Laat deze pagina openstaan in uw browser. We komen er later naar terug.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Een livestream instellen met OBS Studio

OBS begint met een standaardscherm waarop geen invoer is geselecteerd.

   ![OBS-standaardscherm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Een videobron toevoegen

1. Klik in het deelvenster **Bronnen** op het pictogram **Toevoegen** om een nieuw bronapparaat te selecteren. Het menu **Bronnen** wordt geopend.

1. Selecteer **Video-opnameapparaat** in het menu met bronapparaten. Het menu **Bron maken/selecteren** wordt geopend.

   ![Het OBS-menu Bronnen met een videoapparaat geselecteerd](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecteer het keuzerondje **Bestaande toevoegen** en klik op **OK**. Het menu **Eigenschappen voor Video-opnameapparaat** wordt geopend.

   ![Het OBS-menu Nieuwe videobron met ‘Bestaande toevoegen’ geselecteerd](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Selecteer in de vervolgkeuzelijst **Apparaat** het video-opnameapparaat dat u wilt gebruiken voor uw uitzending. Laat de rest van de instellingen voor nu even staan en klik op **OK**. De invoerbron wordt toegevoegd aan het deelvenster **Bronnen**, en de video-invoerweergave verschijnt in het gebied **Preview**.

   ![OBS-camera-instellingen](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Een audiobron toevoegen

1. Klik in het deelvenster **Bronnen** op het pictogram **Toevoegen** om een nieuw bronapparaat te selecteren. Het menu Bronapparaat wordt geopend.

1. Selecteer **Audio-opnameapparaat** in het menu met bronapparaten. Het menu **Bron maken/selecteren** wordt geopend.

   ![Het OBS-menu Bronnen met een audioapparaat geselecteerd](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecteer het keuzerondje **Bestaande toevoegen** en klik op **OK**. Het menu **Eigenschappen voor Audio-opnameapparaat** wordt geopend.

   ![OBS-audiobron met ‘Bestaande toevoegen’ geselecteerd ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Selecteer in de vervolgkeuzelijst **Apparaat** het audio-opnameapparaat dat u wilt gebruiken voor uw uitzending. Laat de rest van de instellingen voor nu even staan en klik op OK. Het audio-opnameapparaat wordt toegevoegd aan het audiomixervenster.

   ![OBS-vervolgkeuzelijst voor selectie van audioapparaat](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Streaming instellen in OBS

In de volgende procedure gaat u in uw browser terug naar Azure Media Services voor het kopiëren van de invoer-URL om in de uitvoerinstellingen in te voeren:

1. Selecteer **Starten** op de Azure Media Services-pagina van de portal om de livestreamgebeurtenis te starten. (De facturering begint nu.)

   ![Het pictogram Starten](media/live-events-obs-quickstart/start.png)
1. Stel de **RTMP**-wisselknop in op **RTMPS**.
1. Kopieer de URL in het vak **Invoer-URL** naar uw klembord.

   ![Invoer-URL](media/live-events-obs-quickstart/input-url.png)

1. Schakel over naar de OBS-toepassing.

1. Klik op de knop **Instellingen** in het deelvenster **Besturingselementen**. De Opties voor instellingen worden geopend.

   ![Het OBS-deelvenster Besturingselementen met ‘Instellingen’ geselecteerd](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecteer **Stream** in het menu **Instellingen**.

1. Selecteer in de vervolgkeuzelijst **Service** de optie Alles weergeven en selecteer vervolgens **Aangepast...** .

1. Plak in het veld **Server** de RTMPS-URL die u naar uw klembord had gekopieerd.

1. Typ iets in het veld **Streamsleutel**.  Het maakt niet zoveel uit wat u typt, zolang het maar een waarde bevat.

    ![OBS-streaminstellingen](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecteer **Uitvoer** in het menu **Instellingen**.

1. Typ *2* in het veld **Sleutelframe-interval**. De fragmentlengte wordt dan op 2 seconden ingesteld. Voor een lagere latentie stelt u de waarde in op 1 seconde.

1. OPTIONEEL: Stel **CPU-gebruik vooraf instellen** in op *Zeer snel* als u een computer gebruikt die weinig verwerkingskracht heeft. U kunt de kbps desgewenst op iets lagers instellen als er ongunstige netwerkomstandigheden zijn.

   ![OBS-uitvoerinstellingen](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Laat de rest van de instellingen ongewijzigd en klik op **OK**.

### <a name="start-streaming"></a>Streaming starten

1. Klik in het deelvenster **Besturingselementen** op **Streaming starten**.

    ![De OBS-knop Streaming starten](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Schakel in uw browser over naar het Azure Media Services-scherm Livegebeurtenis en klik op de koppeling **Speler opnieuw laden**. U zou uw stream nu in de Preview-speler moeten zien.

## <a name="set-up-outputs"></a>Uitvoer instellen

In dit deel kunt u uw uitvoer instellen en een opname van uw livestream opslaan.  

> [!NOTE]
> Om deze uitvoer te kunnen streamen, moet het streaming-eindpunt worden uitgevoerd. Zie de sectie [Het standaardstreaming-eindpunt uitvoeren](#run-the-default-streaming-endpoint) verderop.

1. Selecteer de koppeling **Uitvoer maken** onder de videoviewer **Uitvoer**.
1. Als u wilt, kunt u de naam van de uitvoer in het vak **Naam** wijzigen in iets gebruiksvriendelijkers, zodat de uitvoer later gemakkelijk terug te vinden is.

   ![Het vak Uitvoernaam](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de vakken voor nu even ongewijzigd.
1. Selecteer **Volgende** om een streaming-locator toe te voegen.
1. Wijzig de naam van de locator in iets gebruiksvriendelijkers, als u wilt.

   ![Het vak Locator-naam](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Laat al het andere op dit scherm voor nu even ongewijzigd.
1. Selecteer **Maken**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoeruitzending afspelen met Azure Media Player

1. Kopieer de streaming-URL onder de **Uitvoer**-videospeler.
1. Open de [Azure Media Player-demo](https://ampdemo.azureedge.net/azuremediaplayer.html) in een webbrowser.
1. Plak de streaming-URL in het vak **URL** van Azure Media Player.
1. Selecteer de knop **Speler bijwerken**.
1. Selecteer het pictogram **Afspelen** in de video om uw livestream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Wanneer u vindt dat u genoeg inhoud hebt gestreamd, stopt u de uitzending.

1. Selecteer **Stoppen** in de portal.

1. Selecteer in OBS de knop **Streaming stoppen** in het deelvenster **Besturingselementen**. Deze stap stopt de uitzending vanuit OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>De on-demand uitvoer afspelen met Azure Media Player

De uitvoer die u hebt gemaakt, is nu beschikbaar voor on-demand streamen zolang uw streaming-eindpunt wordt uitgevoerd.

1. Ga naar de lijst Media Services en selecteer **Assets**.
1. Zoek de gebeurtenisuitvoer die u eerder hebt gemaakt, en selecteer de koppeling naar de asset. De pagina met de assetuitvoer wordt geopend.
1. Kopieer de streaming-URL onder de videospeler voor de asset.
1. Ga in de browser terug naar Azure Media Player en plak de streaming-URL in het vak URL.
1. Selecteer **Speler bijwerken**.
1. Selecteer het pictogram **Afspelen** in de video om de on-demand asset te bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> Stop de services! Nadat u de stappen in deze quickstart hebt voltooid, moet u de livegebeurtenis en het streaming-eindpunt stoppen. Anders wordt u gefactureerd voor de tijd dat ze actief blijven. Zie stap 2 en 3 van de procedure [De uitzending stoppen](#stop-the-broadcast) om de livegebeurtenis te stoppen.

Het streaming-eindpunt stoppen:

1. Selecteer **Streaming-eindpunten** in de lijst Media Services.
2. Selecteer het standaardstreaming-eindpunt dat u eerder had gestart. Deze stap opent de pagina van het eindpunt.
3. Selecteer **Stoppen**.

> [!TIP]
> Als u de assets van deze gebeurtenis niet wilt bewaren, verwijder ze dan, zodat u niet wordt gefactureerd voor opslag.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Livegebeurtenissen en live-uitvoer in Media Services](./live-events-outputs-concept.md)
