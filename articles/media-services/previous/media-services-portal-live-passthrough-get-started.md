---
title: Live streamen met on-premises encoders met behulp van Azure Portal | Microsoft Docs
description: In deze zelfstudie wordt u begeleid bij het maken van een kanaal dat is geconfigureerd voor een doorvoerlevering.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6dd2f8a228c582e99f7d7281bd2d31faff8cadda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010793"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Live streamen met on-premises encoders met behulp van Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Deze zelfstudie bevat de stappen voor het maken van een **kanaal** via Azure Portal dat is geconfigureerd voor een doorvoerlevering. 

## <a name="prerequisites"></a>Vereisten
Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Een Azure-account. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie. 
* Een Media Services-account. Zie [Een Media Services-account maken](media-services-portal-create-account.md) voor meer informatie over het maken van een Media Services-account.
* Een webcam. Bijvoorbeeld [Telestream Wirecast-coderingsprogramma](media-services-configure-wirecast-live-encoder.md). 

Het wordt ten zeerste aanbevolen de volgende artikelen te lezen:

* [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Overzicht van live streamen met Azure Media Services](media-services-manage-channels-overview.md)
* [Live streamen met on-premises coderingsprogramma's die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md)

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Algemeen scenario voor live streamen

In de volgende stappen worden de taken beschreven voor het maken van algemene toepassingen voor live streamen die kanalen gebruiken die zijn geconfigureerd voor doorvoerlevering. In deze zelfstudie wordt getoond hoe een doorvoerkanaal en live gebeurtenissen worden gemaakt en beheerd.

> [!NOTE]
> Controleer of het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** heeft. 
    
1. Sluit een videocamera aan op een computer. <br/>Bekijk voor het instellen van ideeën de [eenvoudige en draag bare video versnellings configuratie voor Video's]( https://link.medium.com/KNTtiN6IeT).
1. Start en configureer een on-premises live coderingsprogramma waarmee een multi-bitrate RTMP- of Fragmented MP4-stream wordt uitgevoerd. Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](https://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie.<br/>Bekijk ook deze blog: [productie van live streams met IB](https://link.medium.com/ttuwHpaJeT).
   
    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.
1. Maak en start een doorvoerkanaal.
1. Haal de URL voor opnemen voor het kanaal op. 
   
    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.
1. Haal de voorbeeld-URL voor het kanaal op. 
   
    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
1. Maak een live gebeurtenis/programma. 
   
    Wanneer u Azure Portal gebruikt, wordt bij het maken van een live gebeurtenis ook een asset gemaakt. 

1. Start de gebeurtenis/het programma wanneer u klaar bent om te streamen en te archiveren.
1. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
1. Stop de gebeurtenis/het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
1. Verwijder de gebeurtenis/het programma (en verwijder desgewenst de asset).     

> [!IMPORTANT]
> Zie [Live streamen met on-premises coderingsprogramma's die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie over de concepten en overwegingen ten aanzien van live streamen met on-premises coderingsprogramma's en doorvoerkanalen.
> 
> 

## <a name="to-view-notifications-and-errors"></a>Meldingen en fouten weergeven
Als u de meldingen en fouten wilt weergeven die door Azure Portal zijn gegenereerd, klikt u op het pictogram Melding.

![Meldingen](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Doorvoerkanalen en gebeurtenissen maken en starten
Een kanaal is gekoppeld aan gebeurtenissen/programma's waarmee u het publiceren en opslaan van segmenten in een live stream kunt beheren. Kanalen beheren gebeurtenissen. 

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. De lengte van een archiefvenster bepaalt ook de maximale hoeveelheid tijd die clients terug in de tijd kunnen zoeken vanaf de huidige live positie. Gebeurtenissen kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elke gebeurtenis is gekoppeld aan een asset. Als u de gebeurtenis wilt publiceren, moet u een OnDemand-locator voor de gekoppelde asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve gebeurtenissen, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Gebruik de bestaande live gebeurtenissen niet opnieuw. In plaats daarvan maakt en start u een nieuwe gebeurtenis voor elke gebeurtenis.

Start de gebeurtenis wanneer u klaar bent om te streamen en te archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren. 

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u de gebeurtenis en verwijdert u vervolgens de gekoppelde asset. Een asset kan niet worden verwijderd als deze wordt gebruikt door een gebeurtenis. U moet eerst de gebeurtenis verwijderen. 

Zelfs na het stoppen en verwijderen van de gebeurtenis kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

Als u de gearchiveerde inhoud wilt behouden maar deze niet langer voor streaming beschikbaar mag zijn, verwijdert u de streaming-locator.

### <a name="to-use-the-portal-to-create-a-channel"></a>De portal gebruiken om een kanaal te maken
In deze secties ziet u hoe u de optie **Snelle invoer** gebruikt om een doorvoerkanaal te maken.

Zie [Live streamen met on-premises coderingsprogramma's die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie over doorvoerkanalen.

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Klik in het venster **Instellingen** op **Live streamen**. 
   
    ![Aan de slag](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Het venster **Live streamen** wordt weergegeven.
3. Klik op **Snelle invoer** om een doorvoerkanaal te maken met het RTMP-opnameprotocol.
   
    Het venster **EEN NIEUW KANAAL MAKEN** wordt weergegeven.
4. Geef het nieuwe kanaal een naam en klik op **Maken**. 
   
    Hierop wordt een doorvoerkanaal gemaakt met het RTMP-opnameprotocol.

## <a name="create-events"></a>Gebeurtenissen maken
1. Selecteer een kanaal waaraan u een gebeurtenis wilt toevoegen.
2. Klik op de knop **Live gebeurtenis**.

![Gebeurtenis](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>URL’s voor opnemen ophalen
Wanneer het kanaal is gemaakt, kunt u URL’s voor opnemen ophalen die u aan het live coderingsprogramma levert. Het coderingsprogramma gebruikt deze URL's voor het invoeren van een live stream.

![Scherm opname van de pagina live streamen met een geselecteerd kanaal en het kanaal venster wordt weer gegeven.](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>De gebeurtenis bekijken
Als u een gebeurtenis wilt bekijken, klikt u op **Bekijken** in Azure Portal of kopieert u de streaming-URL en gebruikt u een speler van uw keuze. 

![Gemaakt](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

De live gebeurtenis wordt automatisch geconverteerd naar inhoud op aanvraag wanneer deze wordt gestopt.

## <a name="clean-up"></a>Opschonen
Zie [Live streamen met on-premises coderingsprogramma's die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie over doorvoerkanalen.

* Een kanaal kan alleen worden gestopt, wanneer alle gebeurtenissen/programma's op het kanaal zijn gestopt.  Nadat het kanaal is gestopt, worden hiervoor geen kosten meer in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
* Een kanaal kan alleen worden verwijderd, wanneer alle live gebeurtenissen op het kanaal zijn verwijderd.

## <a name="view-archived-content"></a>Gearchiveerde inhoud weergeven
Zelfs na het stoppen en verwijderen van de gebeurtenis kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd. Een asset kan niet worden verwijderd als deze wordt gebruikt door een gebeurtenis. U moet eerst de gebeurtenis verwijderen. 

Voor het beheren van uw assets selecteert u **instelling** en klikt u op **assets**.

![Assets](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Volgende stap
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
