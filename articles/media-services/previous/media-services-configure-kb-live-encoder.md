---
title: De Haivision KB Encoder configureren om een live stream met één bitsnelheid naar Azure te verzenden | Microsoft Docs
description: In dit onderwerp wordt uitgelegd hoe u de Haivision KB Live Encoder kunt configureren om een enkele bitrate stroom te verzenden naar AMS-kanalen die zijn ingeschakeld voor Live encoding.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 20c75672bc7198fb028278dc5432cc6a77e3766e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016862"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>De Haivision KB Live Encoder gebruiken om een live stream met één bitsnelheid te verzenden

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

In dit onderwerp wordt uitgelegd hoe u de [HAVISION KB Live Encoder](https://www.haivision.com/products/kb-series/) encoder configureert om een enkele bitrate stroom te verzenden naar AMS-kanalen die zijn ingeschakeld voor Live encoding. Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

In deze zelf studie ziet u hoe u Azure Media Services (AMS) beheert met Azure Media Services Explorer (AMSE). Dit hulp programma wordt alleen uitgevoerd op Windows-PC'S. Als u gebruikmaakt van Mac of Linux, gebruikt u de Azure Portal om [kanalen](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) en [Program ma's](media-services-portal-creating-live-encoder-enabled-channel.md)te maken.

## <a name="prerequisites"></a>Vereisten
*   Toegang tot een Haivision KB-encoder, waarop SW v 5.01 of hoger wordt uitgevoerd.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Geef een kanaal naam op. het veld Beschrijving is optioneel. Onder kanaal instellingen selecteert u **standaard** voor de optie Live encoding, waarbij het invoer protocol is ingesteld op **RTMP**. U kunt alle andere instellingen ongewijzigd laten. Zorg ervoor dat het **nieuwe kanaal nu starten** is geselecteerd.
3. Klik op **kanaal maken**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Het kanaal kan Maxi maal 20 minuten duren voordat het wordt gestart.

## <a name="configure-the-haivision-kb-encoder"></a>De Haivision KB-Encoder configureren
In deze zelf studie worden de volgende uitvoer instellingen gebruikt. In de rest van deze sectie worden configuratie stappen in meer detail beschreven.

Video:
-   Codec: H.264
-   Profiel: hoog (niveau 4.0)
-   Bitrate: 5000 kbps
-   Keyframe: 2 seconden (60 frames)
-   Frame-rate: 30

Geluiden
-   Codec: AAC (LC)
-   Bitsnelheid: 192 kbps
-   Sample frequentie: 44,1 kHz

## <a name="configuration-steps"></a>Configuratiestappen
1.  Meld u aan bij de gebruikers interface van Haivision KB.
2.  Klik op de **menu knop** in het kanaal besturings centrum en selecteer **kanaal toevoegen**  
    ![Scherm afbeelding 2017-08-14 bij 9.15.09 uur](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ de **naam** van het kanaal in het veld naam en klik op volgende.  
    ![Scherm afbeelding 2017-08-14 bij 9.19.07 uur](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Selecteer de **invoer bron** voor het kanaal in de vervolg keuzelijst **invoer bron** en klik op volgende.
    ![Scherm afbeelding 2017-08-14 bij 9.20.44 uur](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Kies in de vervolg keuzelijst **coderings sjabloon** **H264-720-AAC-192** en klik op volgende.
    ![Scherm afbeelding 2017-08-14 bij 9.23.15 uur](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Kies in de vervolg keuzelijst **nieuwe uitvoer selecteren** **RTMP** en klik op volgende.  
    ![Scherm afbeelding 2017-08-14 bij 9.27.51 uur](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Vul in het venster **kanaal uitvoer** de gegevens van de Azure stream in. Plak de **RTMP** -koppeling vanuit de eerste kanaal installatie in het gebied **Server** . Typ in het gebied **uitvoer naam** de naam van het kanaal. Gebruik in het gebied sjabloon voor stream-naam de sjabloon RTMPStreamName_% video_bitrate% om de stroom een naam te benoemen.
    ![Scherm afbeelding 2017-08-14 bij 9.33.17 uur](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klik op volgende en klik vervolgens op gereed.
9.  Klik op de **afspeel knop** om het coderings kanaal te starten.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Afspelen testen
Ga naar het hulp programma AMSE en klik met de rechter muisknop op het kanaal dat u wilt testen. Klik in het menu met de muis aanwijzer op het afspelen van de preview-versie en selecteer met Azure Media Player.

Als de stroom in de speler wordt weer gegeven, is het coderings programma op de juiste wijze geconfigureerd om verbinding te maken met AMS.

Als er een fout wordt ontvangen, moet het kanaal opnieuw worden ingesteld en worden de coderings instellingen aangepast. Raadpleeg het artikel over probleem oplossing voor hulp.

## <a name="create-a-program"></a>Een programma maken
1.  Een programma maken wanneer het afspelen van het kanaal is bevestigd. Klik onder het tabblad Live in het hulp programma AMSE met de rechter muisknop in het gebied Program en selecteer nieuw programma maken.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Geef het programma een naam en wijzig indien nodig de lengte van het archief venster (de standaard waarde is vier uur). U kunt ook een opslag locatie opgeven of de standaard waarde laten staan.
2.  Schakel het selectie vakje programma nu starten in.
3.  Klik op programma maken.
4.  Nadat het programma is uitgevoerd, kunt u het afspelen bevestigen door met de rechter muisknop op het programma te klikken en te navigeren om de Program ma's af te spelen en vervolgens te selecteren met Azure Media Player.
5.  Nadat deze is bevestigd, klikt u opnieuw met de rechter muisknop op het programma en selecteert u de uitvoer-URL naar het klem bord kopiëren (of deze gegevens ophalen uit de optie informatie en instellingen van het programma in het menu).

De stroom is nu gereed om te worden inge sloten in een speler of gedistribueerd naar een publiek voor Live weer gave.

> [!NOTE]
> Het maken van het programma kost minder tijd dan het maken van het kanaal.
