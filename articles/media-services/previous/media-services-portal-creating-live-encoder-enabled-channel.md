---
title: Live streamen met Azure Media Services om multi-bitrate streams te maken met Azure Portal | Microsoft Docs
description: In deze zelfstudie wordt u begeleid bij de stappen voor het met Azure Portal maken van een kanaal dat een single-bitrate live stream ontvangt en deze codeert naar een multi-bitrate stream.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 36a4eb2856beb3ae2b0227e92f0db26e01a1b616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012748"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Live streamen met Media Services om multi-bitrate streams te maken met Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](/rest/api/media/operations/channel)
> 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In deze zelfstudie wordt u begeleid bij de stappen voor het maken van een **kanaal** dat een single-bitrate livestream ontvangt, en het coderen van deze stream naar een multi-bitrate stream.

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer conceptuele informatie over kanalen die zijn ingeschakeld voor live codering.

## <a name="common-live-streaming-scenario"></a>Algemeen scenario voor live streamen

Hieronder volgen de algemene stappen voor het maken van veelvoorkomende toepassingen voor live streamen.

> [!NOTE]
> De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amshelp@microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.

1. Sluit een videocamera aan op een computer. <br/>Bekijk voor het instellen van ideeën de [eenvoudige en draag bare video versnellings configuratie voor Video's]( https://link.medium.com/KNTtiN6IeT).

    Als u geen toegang hebt tot een camera, kunt u hulpprogram ma's zoals [Wirecast](media-services-configure-wirecast-live-encoder.md) gebruiken om een live-feed uit een video bestand te genereren.
1. Start en configureer een on-premises livecoderingsprogramma dat een single-bitrate stream in een van de volgende protocollen kan uitvoeren: RTMP of Smooth Streaming. Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](https://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie. <br/>Bekijk ook deze blog: [productie van live streams met IB](https://link.medium.com/ttuwHpaJeT).

    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.
1. Maak en start een kanaal.
1. Haal de URL voor opnemen voor het kanaal op.

    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.
1. Haal de voorbeeld-URL voor het kanaal op.

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
1. Maak een gebeurtenis/programma (hierbij wordt ook een asset gemaakt).
1. Publiceer de gebeurtenis (hierbij wordt ook een OnDemand-locator voor de gekoppelde asset gemaakt).
1. Start de gebeurtenis wanneer u klaar bent om te streamen en te archiveren.
1. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
1. Stop de gebeurtenis als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
1. Verwijder de gebeurtenis (en verwijder desgewenst de asset).

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

* U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken.
  Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Een Media Services-account. Zie [Account maken](media-services-portal-create-account.md) voor meer informatie over het maken van een Media Services-account.
* Een webcam en een coderingsprogramma dat een single bitrate livestream kan verzenden.

## <a name="create-a-channel"></a>Een kanaal maken

1. Selecteer in [Azure Portal](https://portal.azure.com/) de optie Media Services en klik vervolgens op de naam van uw Media Services-account.
2. Selecteer **Live streamen**.
3. Selecteer **Aangepast maken**. Met deze optie kunt u een kanaal maken dat is ingeschakeld voor real-time codering.

    ![Een kanaal maken](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Klik op **Instellingen**.

   1. Kies het kanaaltype **Live Encoding**. U geeft met dit type op dat u een kanaal wilt maken dat voor live codering is ingeschakeld. Dit betekent dat de binnenkomende single-bitrate stream naar het kanaal wordt verzonden en naar een multi-bitrate stream wordt gecodeerd met de opgegeven instellingen van het live coderingsprogramma. Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie. Klik op OK.
   2. Geef een naam op voor het kanaal.
   3. Klik op OK onder aan het scherm.
5. Selecteer het tabblad **Opnemen**.

   1. Op deze pagina kunt u een streaming-protocol selecteren. Voor het kanaaltype **Live Encoding** zijn geldige protocolopties:

      * Single-bitrate Fragmented MP4 (Smooth Streaming);
      * Single-bitrate RTMP;

        Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor een gedetailleerde beschrijving van de protocollen.

        U kunt de protocoloptie niet wijzigen terwijl het kanaal of de gekoppelde gebeurtenissen/programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk streaming-protocol.  
   2. U kunt IP-beperking toepassen op de opname.

       U kunt de IP-adressen definiëren die een video naar dit kanaal mogen publiceren. Toegestane IP-adressen kunnen worden opgegeven als een enkel IP-adres (bijvoorbeeld 10.0.0.1), een IP-bereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-bereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld 10.0.0.1 (255.255.252.0)).

       Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.
6. Pas op het tabblad **Voorbeeld** IP-beperking toe op de preview.
7. Geef op het tabblad **Codering** de vooraf gedefinieerde instellingen voor codering op.

    Momenteel kunt u alleen de vooraf ingestelde systeemwaarde **Default 720p** (Standaard 720p) selecteren. Open een Microsoft-ondersteuningsticket als u aangepaste vooraf gedefinieerde instellingen voor codering wilt opgeven. Voer vervolgens de naam in van de vooraf gedefinieerde instellingen die voor u zijn gemaakt.

> [!NOTE]
> Momenteel kan het tot dertig minuten duren voordat het kanaal wordt gestart. Het opnieuw instellen van een kanaal kan tot vijf minuten duren.

Als u het kanaal hebt gemaakt, kunt u op het kanaal klikken en het tabblad **Instellingen** selecteren, waar u de configuraties van de kanalen kunt bekijken.

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

## <a name="get-ingest-urls"></a>URL’s voor opnemen ophalen

Wanneer het kanaal is gemaakt, kunt u URL’s voor opnemen ophalen die u aan het live coderingsprogramma levert. Het coderingsprogramma gebruikt deze URL's voor het invoeren van een live stream.

![opname-url's](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Gebeurtenissen maken en beheren

### <a name="overview"></a>Overzicht

Een kanaal is gekoppeld aan gebeurtenissen/programma's waarmee u het publiceren en opslaan van segmenten in een live stream kunt beheren. Kanalen beheren gebeurtenissen/programma’s. De kanaal-/programmarelatie lijkt erg op traditionele media waarbij een kanaal een constante stream met inhoud heeft en een programma is afgestemd op een bepaalde getimede gebeurtenis op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor de gebeurtenis wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. De lengte van een archiefvenster bepaalt ook de maximale hoeveelheid tijd die clients terug in de tijd kunnen zoeken vanaf de huidige live positie. Gebeurtenissen kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elke gebeurtenis is gekoppeld aan een asset. Als u de gebeurtenis wilt publiceren, moet u een OnDemand-locator voor de gekoppelde asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve gebeurtenissen, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een gebeurtenis te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve gebeurtenissen maken. De ene gebeurtenis wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma niet te publiceren. De andere gebeurtenis wordt ingesteld om tien minuten te archiveren en dit programma wel te publiceren.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. In plaats daarvan maakt en start u een nieuw programma voor elke gebeurtenis.

Start een gebeurtenis/het programma wanneer u klaar bent om te streamen en te archiveren. Stop de gebeurtenis als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u de gebeurtenis en verwijdert u vervolgens de gekoppelde asset. Een asset kan niet worden verwijderd als deze wordt gebruikt door de gebeurtenis. U moet eerst de gebeurtenis verwijderen.

Zelfs na het stoppen en verwijderen van de gebeurtenis kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

Als u de gearchiveerde inhoud wilt behouden maar deze niet langer voor streaming beschikbaar mag zijn, verwijdert u de streaming-locator.

### <a name="createstartstop-events"></a>Gebeurtenissen maken/starten/stoppen

Zodra de stream het kanaal binnenkomt, kunt u de streaminggebeurtenis starten door een asset, programma en streaming-locator te maken. Hiermee wordt de stream gearchiveerd en beschikbaar gesteld aan kijkers via het streaming-eindpunt.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

Gebeurtenissen kunnen op twee manieren worden gestart:

1. Klik vanaf de pagina **Kanaal** op **Live gebeurtenis** om een nieuwe gebeurtenis toe te voegen.

    Geef het volgende op: gebeurtenisnaam, assetnaam, archiefvenster en versleutelingsoptie.

    ![programma maken](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Als u **Deze live gebeurtenis nu publiceren** ingeschakeld hebt gelaten, worden er PUBLICATIE-URL's gemaakt.

    U kunt op **Starten** klikken, wanneer u klaar bent om de gebeurtenis te streamen.

    Als u de gebeurtenis hebt gestart, drukt u op **Bekijken** om het afspelen van de inhoud te starten.
2. U kunt ook een snelkoppeling gebruiken en op de knop **Go Live** op de pagina **Kanaal** klikken. Hiermee wordt een standaardasset, -programma en -streaming-locator gemaakt.

    De gebeurtenis heet **default** en het archiefvenster is ingesteld op 8 uur.

U kunt de gepubliceerde gebeurtenis bekijken op de pagina **Live gebeurtenis**.

Als u op **Uit lucht** klikt, worden alle live evenementen gestopt.

## <a name="watch-the-event"></a>De gebeurtenis bekijken

Als u een gebeurtenis wilt bekijken, klikt u op **Bekijken** in Azure Portal of kopieert u de streaming-URL en gebruikt u een speler van uw keuze.

![Gemaakt](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

De live gebeurtenis wordt automatisch geconverteerd naar inhoud op aanvraag wanneer deze wordt gestopt.

## <a name="clean-up"></a>Opschonen

Als u klaar bent met het streamen van gebeurtenissen en de resources wilt opruimen die eerder zijn ingericht, volgt u de volgende procedure.

* Stop het pushen van de stream vanuit het coderingsprogramma.
* Stop het kanaal. Nadat het kanaal is gestopt, worden hiervoor geen kosten meer in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
* U kunt uw streaming-eindpunt stoppen, tenzij u het archief van uw live gebeurtenis wilt blijven leveren als stream op aanvraag. Nadat het kanaal is gestopt, worden hiervoor geen kosten meer in rekening gebracht.

## <a name="view-archived-content"></a>Gearchiveerde inhoud weergeven

Zelfs na het stoppen en verwijderen van de gebeurtenis kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

> [!WARNING]
> Een activum **mag niet** worden verwijderd als het wordt gebruikt door een gebeurtenis; de gebeurtenis moet eerst worden verwijderd.

Voor het beheren van uw assets selecteert u **instelling** en klikt u op **assets**.

![Assets](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Overwegingen

* De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amshelp@microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.
* Controleer of het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** heeft.

## <a name="next-steps"></a>Volgende stappen

Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
