---
title: Een live stream maken met IB Studio
description: Ontdek hoe u een Azure Media Services-livestream maakt met behulp van de portal en OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: 0e425cddea1adaec8bfb8f0055b55bb0c45fb168
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123624"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Een Azure Media Services-livestream maken met OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze Snelstartgids helpt u bij het maken van een Media Services live-gebeurtenis met behulp van de Azure Portal en de uitzending met behulp van open Broadcasting Studio (IB). Er wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

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

   ![Menu-item voor streaming-eind punten.](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Als de status van het standaardstreaming-eindpunt ‘gestopt’ is, selecteert u het. Deze stap leidt u naar de pagina voor dat eindpunt.
1. Selecteer **Starten**.

   ![Start knop voor het streaming-eind punt.](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Een Azure Media Services-livestream instellen

1. Ga naar het Azure Media Services-account in de portal en selecteer **Live streamen** in de lijst **Media Services**.

   ![Koppeling live streamen.](media/live-events-obs-quickstart/select-live-streaming.png)
1. Selecteer **Livegebeurtenis toevoegen** om een nieuwe livestreamgebeurtenis te maken.

   ![Pictogram live-gebeurtenis toevoegen.](media/live-events-obs-quickstart/add-live-event.png)
1. Typ een naam voor uw nieuwe gebeurtenis, zoals *TestLivegebeurtenis*, in het vak **Naam van de livegebeurtenis**.

   ![Vak voor de live-gebeurtenis naam.](media/live-events-obs-quickstart/live-event-name.png)
1. Typ een optionele beschrijving van de gebeurtenis in het vak **Beschrijving**.
1. Selecteer de optie **Passthrough: geen cloudcodering**.

   ![Optie voor Cloud versleuteling.](media/live-events-obs-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP**.
1. Zorg ervoor dat de optie **Nee** is geselecteerd voor **Livegebeurtenis starten**, om te voorkomen dat u wordt gefactureerd voor de livegebeurtenis voordat deze gereed is. (De facturering begint wanneer de livegebeurtenis is gestart.)

   ![De optie Live-gebeurtenis starten.](media/live-events-obs-quickstart/start-live-event-no.png)
1. Selecteer de knop **Controleren en maken** om de instellingen te controleren.
1. Selecteer de knop **Maken** om de livegebeurtenis te maken. U wordt dan teruggebracht naar de lijst met livegebeurtenissen.
1. Selecteer de koppeling naar de live-gebeurtenis die u hebt gemaakt. Merk op dat uw gebeurtenis is gestopt.
1. Laat deze pagina openstaan in uw browser. We komen er later naar terug.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Een livestream instellen met OBS Studio

OBS begint met een standaardscherm waarop geen invoer is geselecteerd.

   ![OBS-standaardscherm](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Een videobron toevoegen

1. Selecteer in het deel venster **bronnen** het pictogram **toevoegen** om een nieuw bron apparaat te selecteren. Het menu **Bronnen** wordt geopend.

1. Selecteer **Video-opnameapparaat** in het menu met bronapparaten. Het menu **Bron maken/selecteren** wordt geopend.

   ![Menu bronnen IB met geselecteerd video apparaat.](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Selecteer het keuze rondje **bestaande toevoegen** en selecteer vervolgens **OK**. Het menu **Eigenschappen voor Video-opnameapparaat** wordt geopend.

   ![IB nieuwe video bron menu met de optie bestaande toevoegen geselecteerd.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Selecteer in de vervolgkeuzelijst **Apparaat** het video-opnameapparaat dat u wilt gebruiken voor uw uitzending. Laat de overige instellingen voor Taan ongewijzigd en selecteer **OK**. De invoerbron wordt toegevoegd aan het deelvenster **Bronnen**, en de video-invoerweergave verschijnt in het gebied **Preview**.

   ![OBS-camera-instellingen](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Een audiobron toevoegen

1. Selecteer in het deel venster **bronnen** het pictogram **toevoegen** om een nieuw bron apparaat te selecteren. Het menu Bronapparaat wordt geopend.

1. Selecteer **Audio-opnameapparaat** in het menu met bronapparaten. Het menu **Bron maken/selecteren** wordt geopend.

   ![Het menu IB bronnen met een audio apparaat geselecteerd.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Selecteer het keuze rondje **bestaande toevoegen** en selecteer vervolgens **OK**. Het menu **Eigenschappen voor Audio-opnameapparaat** wordt geopend.

   ![IB-audio bron met bestaande toevoegen geselecteerd.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Selecteer in de vervolgkeuzelijst **Apparaat** het audio-opnameapparaat dat u wilt gebruiken voor uw uitzending. Laat de overige instellingen voor Taan ongewijzigd en selecteer OK. Het audio-opnameapparaat wordt toegevoegd aan het audiomixervenster.

   ![OBS-vervolgkeuzelijst voor selectie van audioapparaat](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>Instellingen voor streaming en geavanceerde code ring instellen in IB

In de volgende procedure gaat u in uw browser terug naar Azure Media Services voor het kopiëren van de invoer-URL om in de uitvoerinstellingen in te voeren:

1. Selecteer **Starten** op de Azure Media Services-pagina van de portal om de livestreamgebeurtenis te starten. (De facturering begint nu.)

   ![Pictogram starten.](media/live-events-obs-quickstart/start.png)
1. Stel de **RTMP**-wisselknop in op **RTMPS**.
1. Kopieer de URL in het vak **Invoer-URL** naar uw klembord.

   ![Invoer-URL.](media/live-events-obs-quickstart/input-url.png)

1. Schakel over naar de OBS-toepassing.

1. Selecteer de knop **instellingen** in het deel venster **besturings elementen** . De Opties voor instellingen worden geopend.

   ![Deel venster IB-besturings elementen met geselecteerde instellingen.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Selecteer **Stream** in het menu **Instellingen**.

1. Selecteer in de vervolgkeuzelijst **Service** de optie Alles weergeven en selecteer vervolgens **Aangepast...** .

1. Plak in het veld **Server** de RTMPS-URL die u naar uw klembord had gekopieerd.

1. Typ iets in het veld **Streamsleutel**.  Het maakt niet zoveel uit wat u typt, zolang het maar een waarde bevat.

    ![Instellingen voor IB.](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Selecteer **Uitvoer** in het menu **Instellingen**.

1. Selecteer de vervolg keuzelijst **uitvoer modus** boven aan de pagina en kies **Geavanceerd** voor toegang tot alle beschik bare coderings instellingen.

1. Selecteer het tabblad **streaming** om het coderings programma in te stellen.

1. Selecteer het juiste coderings programma voor uw systeem.  Als uw hardware GPU-versnelling ondersteunt, kiest u uit NVIDIA **NVENC** H. 264 of Intel **QuickSync** H. 264. Als uw systeem geen ondersteunde GPU heeft, selecteert u de optie **X264** software encoder.

#### <a name="x264-encoder-settings"></a>X264 encoder-instellingen

1. Als u de optie **X264** encoding hebt geselecteerd, selecteert u het vak **uitvoer opnieuw schalen** . Selecteer 1920 als u een Premium live-gebeurtenis in Media Services of 1280x720 gebruikt als u een standaard-live-evenement (720P) gebruikt.  Als u een Pass-through live-gebeurtenis gebruikt, kunt u een wille keurige beschik bare oplossing kiezen.

1. Stel de **bitsnelheid** in op elke wille keurige waarde tussen 1500 kbps en 4000 kbps. U wordt aangeraden 2500 kbps te gebruiken als u gebruikmaakt van een standaard gebeurtenis voor het coderen van live op 720P. Als u een 1080P Premium live-evenement gebruikt, wordt 4000 kbps aanbevolen. U kunt de bitsnelheid aanpassen op basis van de beschik bare CPU-mogelijkheden en band breedte op uw netwerk om de gewenste kwaliteits instelling te krijgen.

1. Typ *2* in het veld **Sleutelframe-interval**. Met de waarde stelt u het interval van het sleutel frame in op 2 seconden. Hiermee bepaalt u de uiteindelijke grootte van de fragmenten die worden geleverd via HLS of streepje van Media Services. Stel de sleutel frame-interval nooit in op een waarde van meer dan 4 seconden.  Als u een hoge latentie ziet bij het uitzenden van de uitzending, moet u de gebruikers van uw toepassing altijd controleren of laten weten dat deze waarde altijd 2 seconden moet worden ingesteld. Bij een poging om live-levering met een lagere latentie te krijgen, kunt u ervoor kiezen deze waarde in te stellen op zo laag 1 seconde.

1. Optioneel: Stel de voor instelling voor CPU-gebruik in op **veryfast** en voer enkele experimenten uit om te zien of uw lokale CPU de combi natie van bitrate en voor instelling met voldoende overhead kan verwerken. Probeer instellingen te vermijden die resulteren in een gemiddelde CPU van meer dan 80% om problemen te voor komen tijdens live streamen. Als u de kwaliteit wilt verbeteren, kunt u met **snellere** en **snelle** instellingen testen totdat u de beperkingen voor de CPU bereikt.

   ![Instellingen voor IB X264 encoder](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Laat de overige instellingen ongewijzigd en selecteer **OK**.

#### <a name="nvidia-nvenc-encoder-settings"></a>Instellingen voor nvidia NVENC encoder

1. Als u de coderings optie **NVENC** GPU hebt geselecteerd, schakelt u het selectie vakje scale-out **aanpassen** in en selecteert u 1920 als u een Premium live-gebeurtenis in Media Services gebruikt, of 1280x720 als u gebruikmaakt van een Standard (720P) live-gebeurtenis. Als u een Pass-through live-gebeurtenis gebruikt, kunt u een wille keurige beschik bare oplossing kiezen.

1. Stel de **frequentie controle** in op CBR voor constante bitsnelheid voor bitrate.

1. Stel de **bitsnelheid** overal in tussen 1500 kbps en 4000 kbps. U wordt aangeraden 2500 kbps te gebruiken als u gebruikmaakt van een standaard gebeurtenis voor het coderen van live op 720P. Als u een 1080P Premium live-evenement gebruikt, wordt 4000 kbps aanbevolen. U kunt ervoor kiezen om dit aan te passen op basis van de beschik bare CPU-mogelijkheden en band breedte op uw netwerk om de gewenste instelling voor de kwaliteit te krijgen.

1. Stel het **interval** voor het keyframe in op 2 seconden zoals hierboven vermeld onder de opties voor X264. Niet meer dan 4 seconden, omdat dit de latentie van uw live uitzending aanzienlijk kan beïnvloeden.

1. Stel de **voor instelling** in op lage latentie, Low-Latency prestaties of Low-Latency kwaliteit, afhankelijk van de CPU-snelheid op uw lokale computer. Experimenteer met deze instellingen om het beste evenwicht te krijgen tussen kwaliteit en CPU-gebruik op uw eigen hardware.

1. Stel het **profiel** in op ' Main ' of ' high ' als u een krachtigere hardwareconfiguratie gebruikt.

1. Zorg ervoor **dat het selectie vakje niet ingeschakeld blijft** . Als u een zeer krachtige machine hebt, kunt u dit controleren.

1. Zorg ervoor dat de **visuele afstemming van psycho** niet is ingeschakeld. Als u een zeer krachtige machine hebt, kunt u dit controleren.

1. Stel de **GPU** in op 0 om automatisch te bepalen welke gpu's moeten worden toegewezen. Desgewenst kunt u het GPU-gebruik beperken.

1. Het **maximum aantal B-frames** instellen op 2

   ![IB NVidia NVidia NVENC GPU encoder-instellingen.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Instellingen voor Intel QuickSync encoder

1. Als u de optie Intel **QuickSync** GPU encoding hebt geselecteerd, schakelt u het selectie vakje voor het opnieuw schalen van de **uitvoer** in en selecteert u 1920 als u een Premium live-gebeurtenis in Media Services gebruikt, of 1280x720 als u gebruikmaakt van een Standard (720P) live-gebeurtenis. Als u een Pass-through live-gebeurtenis gebruikt, kunt u een wille keurige beschik bare oplossing kiezen.

1. Stel het **doel gebruik** in op Gebalanceerd of pas deze zo nodig aan op basis van uw CPU en GPU gecombineerd laden. Pas zo nodig aan en Experimenteer een maximum van 80% voor het CPU-gebruik, gemiddeld met de kwaliteit die uw hardware kan produceren. Als u zich op een meer beperkte hardware bevindt, kunt u testen met snel of op zeer snelle weg als u prestatie problemen ondervindt.

1. Stel het **profiel** in op ' Main ' of ' high ' als u een krachtigere hardwareconfiguratie gebruikt.

1. Stel het **interval** voor het keyframe in op 2 seconden zoals hierboven vermeld onder de opties voor X264. Niet meer dan 4 seconden, omdat dit de latentie van uw live uitzending aanzienlijk kan beïnvloeden.

1. Stel de **frequentie controle** in op CBR voor constante bitsnelheid voor bitrate.

1. Stel de **bitsnelheid** overal in tussen 1500 en 4000 kbps.  U wordt aangeraden 2500 kbps te gebruiken als u gebruikmaakt van een standaard gebeurtenis voor het coderen van live op 720P. Als u een 1080P Premium live-evenement gebruikt, wordt 4000 kbps aanbevolen. U kunt ervoor kiezen om dit aan te passen op basis van de beschik bare CPU-mogelijkheden en band breedte op uw netwerk om de gewenste instelling voor de kwaliteit te krijgen.

1. Stel de **latentie** in op laag.

1. Stel de **B-frames** in op 2.

1. Zorg ervoor dat de **uitbrei dingen van de onderhevige video** uitgeschakeld zijn.

   ![IB Intel QuickSync GPU encoder-instellingen.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Audio-instellingen instellen

In de volgende procedure past u de instellingen voor audio codering aan.

1. Selecteer het tabblad uitvoer->audio in instellingen.

1. Stel de audio- **bitsnelheid** van track 1 in op 128 kbps.

   ![IB voor audio-bitsnelheid.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Selecteer het tabblad Audio in instellingen.

1. Stel de **sample frequentie** in op 44,1 kHz.

   ![Instellingen voor IB-audio sample frequentie.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Streaming starten

1. Klik in het deelvenster **Besturingselementen** op **Streaming starten**.

    ![IB de knop streaming starten.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Schakel in uw browser over naar het Azure Media Services-scherm Livegebeurtenis en klik op de koppeling **Speler opnieuw laden**. U zou uw stream nu in de Preview-speler moeten zien.

## <a name="set-up-outputs"></a>Uitvoer instellen

In dit deel kunt u uw uitvoer instellen en een opname van uw livestream opslaan.  

> [!NOTE]
> Om deze uitvoer te kunnen streamen, moet het streaming-eindpunt worden uitgevoerd. Zie de sectie [Het standaardstreaming-eindpunt uitvoeren](#run-the-default-streaming-endpoint) verderop.

1. Selecteer de koppeling **Uitvoer maken** onder de videoviewer **Uitvoer**.
1. Als u wilt, kunt u de naam van de uitvoer in het vak **Naam** wijzigen in iets gebruiksvriendelijkers, zodat de uitvoer later gemakkelijk terug te vinden is.

   ![Vak uitvoer naam.](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de vakken voor nu even ongewijzigd.
1. Selecteer **Volgende** om een streaming-locator toe te voegen.
1. Wijzig de naam van de locator in iets gebruiksvriendelijkers, als u wilt.

   ![Vak Naam van Locator.](media/live-events-wirecast-quickstart/live-event-locator.png)
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
> [Livegebeurtenissen en live-uitvoer in Media Services](./live-event-outputs-concept.md)
