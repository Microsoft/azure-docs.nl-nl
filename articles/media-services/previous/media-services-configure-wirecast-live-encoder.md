---
title: De Telestream Wirecast Encoder configureren voor het verzenden van een live stream met één bitsnelheid | Microsoft Docs
description: 'In dit onderwerp wordt uitgelegd hoe u de Wirecast Live Encoder configureert om een enkele bitrate stroom te verzenden naar AMS-kanalen die zijn ingeschakeld voor Live encoding. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016828"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Het Wirecast-coderings programma gebruiken om een live stream met één bitsnelheid te verzenden

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

In dit artikel wordt beschreven hoe u de Wirecast van de [Telestream](https://www.telestream.net/wirecast/overview.htm) configureert om een enkele bitrate stroom te verzenden naar AMS-kanalen die zijn ingeschakeld voor Live encoding. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelf studie ziet u hoe u Azure Media Services (AMS) beheert met Azure Media Services Explorer (AMSE). Dit hulp programma wordt alleen uitgevoerd op Windows-PC'S. Als u gebruikmaakt van Mac of Linux, gebruikt u de Azure Portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [Program ma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van RTMP-protocollen. Gebruik Wirecast-versie 13.0.2 of hoger als gevolg van de TLS 1,2-vereiste.

## <a name="prerequisites"></a>Vereisten
* [Een Azure Media Services-account maken](media-services-portal-create-account.md)
* Zorg ervoor dat er een streaming-eind punt wordt uitgevoerd. Zie [streaming-eind punten beheren in een Media Services-account](media-services-portal-manage-streaming-endpoints.md) voor meer informatie
* Installeer de meest recente versie van het hulp programma [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Start het hulp programma en maak verbinding met uw AMS-account.

## <a name="tips"></a>Tips
* Gebruik indien mogelijk een vaste internetverbinding.
* Als goede vuistregel voor het bepalen van de bandbreedtevereisten kunt u de streaming-bitrates verdubbelen. Hoewel dit geen verplichte vereiste is, helpt het bij het beperken van de impact van de netwerk congestie.
* Wanneer u software encoders gebruikt, moet u overbodige Program ma's sluiten.

## <a name="create-a-channel"></a>Een kanaal maken
1. In het hulp programma AMSE gaat u naar het tabblad **Live** en klikt u met de rechter muisknop binnen het kanaal gebied. Selecteer **kanaal maken...** in het menu.

    ![Scherm afbeelding toont het maken van een kanaal dat is geselecteerd in een menu.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Geef een kanaal naam op. het veld Beschrijving is optioneel. Onder kanaal instellingen selecteert u **standaard** voor de optie Live encoding, waarbij het invoer protocol is ingesteld op **RTMP**. U kunt alle andere instellingen ongewijzigd laten.

    Zorg ervoor dat het **nieuwe kanaal nu starten** is geselecteerd.

3. Klik op **kanaal maken**.

   ![Scherm afbeelding toont het dialoog venster een live kanaal maken.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Het kanaal kan Maxi maal 20 minuten duren voordat het wordt gestart.
>
>

Terwijl het kanaal wordt gestart, kunt u [het coderings programma configureren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> De facturering begint zodra de status van het kanaal gereed is. Zie [Staten van het kanaal](media-services-manage-live-encoder-enabled-channels.md#states)voor meer informatie.
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Het Wirecast-coderings programma voor Telestream configureren
In deze zelf studie worden de volgende uitvoer instellingen gebruikt. In de rest van deze sectie worden configuratie stappen in meer detail beschreven.

**Video**:

* Codec: H.264
* Profiel: hoog (niveau 4.0)
* Bitrate: 5000 kbps
* Keyframe: 2 seconden (60 seconden)
* Frame-rate: 30

**Audio**:

* Codec: AAC (LC)
* Bitsnelheid: 192 kbps
* Sample frequentie: 44,1 kHz

### <a name="configuration-steps"></a>Configuratiestappen
1. Open de Telestream Wirecast-toepassing op de computer die wordt gebruikt en stel deze in voor RTMP-streaming.
2. Configureer de uitvoer door te navigeren naar het tabblad **uitvoer** en **uitvoer instellingen te selecteren...**.

    Zorg ervoor dat de **uitvoer bestemming** is ingesteld op **RTMP-server**.
3. Klik op **OK**.
4. Stel op de pagina instellingen het **doel** veld in op **Azure Media Services**.

    Het coderings profiel is vooraf geselecteerd voor **Azure H. 264 720p 16:9 (1280x720)**. Als u deze instellingen wilt aanpassen, selecteert u het tandwiel pictogram rechts van de vervolg keuzelijst en kiest u vervolgens **nieuwe voor instelling**.

    ![Scherm afbeelding toont het dialoog venster een sjabloon kiezen met sjabloon blobtrigger geselecteerd.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Configureer voor instellingen voor code ring.

    Geef de voor instelling een naam en controleer op de volgende aanbevolen instellingen:

    **Video**

   * Coderings programma: MainConcept H. 264
   * Frames per seconde: 30
   * Gemiddelde bitsnelheid: 5000 KBits per seconde (kan worden aangepast op basis van de beperkingen van het netwerk)
   * Profiel: Main
   * Sleutel frame elke: 60 frames

     **Audio**

   * Bitsnelheid van doel: 192 KBits per seconde
   * Sample frequentie: 44,100 kHz

     ![Scherm afbeelding toont de definitie van het coderings programma voor AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Klik op **Opslaan**.

    Het veld encoding bevat nu het nieuwe profiel dat u wilt kunnen selecteren.

    Zorg ervoor dat het nieuwe profiel is geselecteerd.
7. Haal de invoer-URL van het kanaal op om deze toe te wijzen aan het Wirecast **RTMP-eind punt**.

    Ga terug naar het AMSE-hulp programma en controleer de voltooiings status van het kanaal. Zodra de status is gewijzigd van **starten** in **wordt uitgevoerd**, kunt u de invoer-URL ophalen.

    Wanneer het kanaal actief is, klikt u met de rechter muisknop op de naam van het kanaal, gaat u omlaag om de **invoer-URL naar het klem bord te verplaatsen** en selecteert u vervolgens **primaire invoer-URL**.  

    ![Scherm afbeelding toont de Kopieer invoer U R L naar Klembord optie voor primaire invoer U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Plak in het venster **uitvoer instellingen** Wirecast deze informatie in het veld **adres** van de sectie uitvoer en wijs een stroom naam toe.

    ![Scherm opname toont uitvoer instellingen.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Selecteer **OK**.
2. Bevestig in het hoofd scherm van **Wirecast** de invoer bronnen voor video en audio zijn gereed en klik vervolgens op **stroom** in de linkerbovenhoek.

    ![Scherm afbeelding toont de knop Wirecast-stream.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Voordat u op **Stream** klikt, **moet** u ervoor zorgen dat het kanaal gereed is.
> Zorg er ook voor dat u het kanaal niet langer dan > 15 minuten in de status gereed zonder invoer bijdrage laat staan.
>
>

## <a name="test-playback"></a>Afspelen testen

Ga naar het hulp programma AMSE en klik met de rechter muisknop op het kanaal dat u wilt testen. Klik in het menu met de muis aanwijzer op **het afspelen van de preview-versie** en selecteer **met Azure Media Player**.  

![Scherm afbeelding geeft de optie voor beeld afspelen met Azure Media Player geselecteerd.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Als de stroom in de speler wordt weer gegeven, is het coderings programma op de juiste wijze geconfigureerd om verbinding te maken met AMS.

Als er een fout wordt ontvangen, moet het kanaal opnieuw worden ingesteld en worden de coderings instellingen aangepast. Raadpleeg het artikel over [probleem oplossing](media-services-troubleshooting-live-streaming.md) voor hulp.  

## <a name="create-a-program"></a>Een programma maken
1. Een programma maken wanneer het afspelen van het kanaal is bevestigd. Klik onder het tabblad **Live** in het hulp programma AMSE met de rechter muisknop in het gebied Program en selecteer **nieuw programma maken**.  

    ![Scherm afbeelding toont de optie programma maken geselecteerd.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Geef het programma een naam en wijzig indien nodig de **lengte van het archief venster** (de standaard waarde is vier uur). U kunt ook een opslag locatie opgeven of de standaard waarde laten staan.  
3. Schakel het selectie vakje **programma nu starten** in.
4. Klik op **programma maken**.  

   >[!NOTE]
   >Het maken van het programma kost minder tijd dan het maken van het kanaal.
       
5. Nadat het programma is uitgevoerd, kunt u het afspelen bevestigen door met de rechter muisknop op het programma te klikken en te navigeren om **de Program ma's** af te spelen en vervolgens te selecteren **met Azure Media Player**.  
6. Nadat deze is bevestigd, klikt u opnieuw met de rechter muisknop op het programma en selecteert **u de uitvoer-URL naar het klem bord kopiëren** (of deze gegevens ophalen uit de optie **informatie en instellingen** van het programma in het menu).

De stroom is nu gereed om te worden inge sloten in een speler of gedistribueerd naar een publiek voor Live weer gave.  

## <a name="troubleshooting"></a>Problemen oplossen
Raadpleeg het artikel over [probleem oplossing](media-services-troubleshooting-live-streaming.md) voor hulp.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
