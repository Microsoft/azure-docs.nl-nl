---
title: Een Azure Media Services-livestream maken
description: Ontdek hoe u een Azure Media Services-livestream maakt met behulp van de portal en Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89265266"
---
# <a name="create-an-azure-media-services-live-stream"></a>Een Azure Media Services-livestream maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze quickstart helpt u een Azure Media Services-livestream te maken met behulp van de Azure-portal en Telestream Wirecast. Er wordt ervan uitgegaan dat u een Azure-abonnement hebt en een Media Services-account hebt gemaakt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [Microsoft Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

In deze quickstart komen de volgende onderwerpen aan bod:

- Een on-premises coderingsprogramma instellen met een gratis proefversie van Telestream Wirecast.
- Een livestream instellen.
- Livestream-uitvoer instellen.
- Een standaardstreaming-eindpunt uitvoeren.
- Azure Media Player gebruiken om de livestream en on-demand uitvoer te bekijken.

Om het eenvoudig te houden, gebruiken we een coderingsvoorinstelling voor Azure Media Services in Wirecast, passthrough cloudcodering en RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Een on-premises encoder instellen met Wirecast

1. Download en installeer Wirecast voor uw besturingssysteem op de [Telestream-website](https://www.telestream.net).
1. Start de toepassing en gebruik uw favoriete e-mailadres om het product te registreren. Houd de toepassing open.
1. Verifieer uw e-mail adres in het e-mailbericht dat u ontvangt. Vervolgens wordt de gratis proefversie van de toepassing gestart.
1. Aanbevolen: Bekijk de video-zelfstudie in het openingsscherm van de toepassing.

## <a name="set-up-an-azure-media-services-live-stream"></a>Een Azure Media Services-livestream instellen

1. Ga naar het Azure Media Services-account in de portal en selecteer **Live streamen** in de lijst **Media Services**.

   ![De koppeling Live streamen](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Selecteer **Livegebeurtenis toevoegen** om een nieuwe livestreamgebeurtenis te maken.

   ![Het pictogram Livegebeurtenis toevoegen](media/live-events-wirecast-quickstart/add-live-event.png)
1. Typ een naam voor uw nieuwe gebeurtenis, zoals *TestLivegebeurtenis*, in het vak **Naam van de livegebeurtenis**.

   ![Het vak Naam van de livegebeurtenis](media/live-events-wirecast-quickstart/live-event-name.png)
1. Typ een optionele beschrijving van de gebeurtenis in het vak **Beschrijving**.
1. Selecteer de optie **Passthrough: geen cloudcodering**.

   ![De optie Cloudcodering](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Selecteer de optie **RTMP**.
1. Zorg ervoor dat de optie **Nee** is geselecteerd voor **Livegebeurtenis starten**, om te voorkomen dat u wordt gefactureerd voor de livegebeurtenis voordat deze gereed is. (De facturering begint wanneer de livegebeurtenis is gestart.)

   ![De optie Livegebeurtenis starten](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Selecteer de knop **Controleren en maken** om de instellingen te controleren.
1. Selecteer de knop **Maken** om de livegebeurtenis te maken. U wordt dan teruggebracht naar de lijst met livegebeurtenissen.
1. Selecteer de koppeling naar de livegebeurtenis die u zojuist hebt gemaakt. Merk op dat uw gebeurtenis is gestopt.
1. Laat deze pagina openstaan in uw browser. We komen er later naar terug.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Een livestream instellen met Wirecast Studio

1. Selecteer in de Wirecast-toepassing **Create Empty Document** (Leeg document maken) in het hoofdmenu, en selecteer vervolgens **Continue** (Doorgaan).

   ![Startscherm van Wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Wijs de eerste laag in het gebied **Wirecast layers** (Wirecast-lagen) aan.  Selecteer het pictogram **Add** (Toevoegen) dat wordt weergegeven, en selecteer de video-invoer die u wilt streamen.

   ![Pictogram Toevoegen van Wirecast](media/live-events-wirecast-quickstart/add-icon.png)

   Het dialoogvenster **Master Layer 1** (Hoofdlaag 1) wordt geopend.
1. Selecteer **Video Capture** (Video opnemen) in het menu en selecteer vervolgens de camera die u wilt gebruiken.

   ![Preview-gebied voor video-opname](media/live-events-wirecast-quickstart/video-shot-selection.png)

   De weergave van de camera wordt weergegeven in het preview-gebied.
1. Wijs de tweede laag in het gebied **Wirecast layers** (Wirecast-lagen) aan. Selecteer het pictogram **Add** (Toevoegen) dat wordt weergegeven, en selecteer de audio-invoer die u wilt streamen. Het dialoogvenster **Master Layer 2** (Hoofdlaag 2) wordt geopend.
1. Selecteer **Audio Capture** (Audio opnemen) in het menu en selecteer vervolgens de audio-invoer die u wilt gebruiken.

   ![Invoer voor audio-opname](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Selecteer **Output settings** (Uitvoerinstellingen) in het hoofdmenu. Het dialoogvenster **Select an Output Destination** (Uitvoerbestemming selecteren) wordt weergegeven.
1. Selecteer **Azure Media Services** in de vervolgkeuzelijst **Destination** (Bestemming). De uitvoerinstelling voor Azure Media Services vult *de meeste* uitvoerinstellingen automatisch in.

   ![Uitvoerinstellingenscherm van Wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


In de volgende procedure gaat u in uw browser terug naar Azure Media Services voor het kopiëren van de invoer-URL om in de uitvoerinstellingen in te voeren:

1. Selecteer **Starten** op de Azure Media Services-pagina van de portal om de livestreamgebeurtenis te starten. (De facturering begint nu.)

   ![Het pictogram Starten](media/live-events-wirecast-quickstart/start.png)
2. Stel de wisselknop **Secure/Not secure** (Beveiligd/niet beveiligd) in op **Not secure**. Met deze stap stelt u het protocol in op RTMP in plaats van RTMPS.
3. Kopieer de URL in het vak **Invoer-URL** naar uw klembord.

   ![Invoer-URL](media/live-events-wirecast-quickstart/input-url.png)
4. Ga naar de Wirecast-toepassing en plak de **Invoer-URL** in het vak **Address** (Adres) in de uitvoerinstellingen.

   ![Invoer-URL voor Wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Selecteer **OK**.

## <a name="set-up-outputs"></a>Uitvoer instellen

In dit deel kunt u uw uitvoer instellen en een opname van uw livestream opslaan.  

> [!NOTE]
> Om deze uitvoer te kunnen streamen, moet het streaming-eindpunt worden uitgevoerd. Zie de sectie [Het standaardstreaming-eindpunt uitvoeren](#run-the-default-streaming-endpoint) verderop.

1. Selecteer de koppeling **Uitvoer maken** onder de videoviewer **Uitvoer**.
1. Als u wilt, kunt u de naam van de uitvoer in het vak **Naam** wijzigen in iets gebruikersvriendelijkers, zodat de uitvoer later gemakkelijk terug te vinden is.
   
   ![Het vak Uitvoernaam](media/live-events-wirecast-quickstart/output-name.png)
1. Laat de rest van de vakken voor nu even ongewijzigd.
1. Selecteer **Volgende** om een streaming-locator toe te voegen.
1. Wijzig de naam van de locator in iets gebruikersvriendelijkers, als u wilt.
   
   ![Het vak Locator-naam](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Laat al het andere op dit scherm voor nu even ongewijzigd.
1. Selecteer **Maken**.

## <a name="start-the-broadcast"></a>De uitzending starten

1. Selecteer in Wirecast **Output** (Uitvoer) > **Start / Stop Broadcasting** (Uitzending starten/stoppen) > **Start Azure Media Services: Azure Media Services** in het hoofdmenu.

   ![Menu-items voor het starten van de uitzending](media/live-events-wirecast-quickstart/start-broadcast.png)

   Wanneer de stroom is verzonden naar de live-gebeurtenis, wordt het venster **Live** in Wirecast weer gegeven in de videospeler op de pagina met live-gebeurtenissen in Azure Media Services.

1. Selecteer de knop **Go** onder het voorbeeldvenster om de video en audio die u voor de Wirecast-lagen hebt geselecteerd, uit te zenden.

   ![De knop Go van Wirecast](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Als er een fout optreedt, kunt u de speler opnieuw laden door de koppeling **Reload PLayer** (Speler opnieuw laden) boven de speler te selecteren.

## <a name="run-the-default-streaming-endpoint"></a>Het standaardstreaming-eindpunt uitvoeren

1. Selecteer **Streaming-eindpunten** in de lijst Media Services.

   ![Het menu-item Streaming-eindpunten](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Als de status van het standaardstreaming-eindpunt ‘gestopt’ is, selecteert u het. Deze stap leidt u naar de pagina voor dat eindpunt.
1. Selecteer **Starten**.
   
   ![De knop Starten voor het streaming-eindpunt](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>De uitvoeruitzending afspelen met Azure Media Player

1. Kopieer de streaming-URL onder de **Uitvoer**-videospeler.
1. Open de [Azure Media Player-demo](https://ampdemo.azureedge.net/azuremediaplayer.html) in een webbrowser.
1. Plak de streaming-URL in het vak **URL** van Azure Media Player.
1. Selecteer de knop **Speler bijwerken**.
1. Selecteer het pictogram **Afspelen** in de video om uw livestream te bekijken.

## <a name="stop-the-broadcast"></a>De uitzending stoppen

Wanneer u vindt dat u genoeg inhoud hebt gestreamd, stopt u de uitzending.

1. Selecteer de knop **Broadcast** (Uitzenden) in Wirecast. Deze stap stopt de uitzending vanuit Wirecast.
1. Selecteer **Stoppen** in de portal. Vervolgens wordt er een waarschuwing weergegeven dat de livestream wordt gestopt, maar de uitvoer nu een asset op aanvraag wordt.
1. Selecteer **Stop** in het waarschuwingsbericht. In Azure Media Player wordt nu een fout weergegeven, omdat de livestream niet meer beschikbaar is.

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
