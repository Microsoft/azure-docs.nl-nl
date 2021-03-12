---
title: Streaming-eind punten beheren met de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u streaming-eind punten beheert met de Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017389"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Streaming-eindpunten beheren met de Azure-portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit artikel wordt beschreven hoe u de Azure Portal gebruikt voor het beheren van streaming-eind punten. 

>[!NOTE]
>Zorg ervoor dat u het artikel [overzicht](media-services-streaming-endpoints-overview.md) bekijkt. 

Zie [Dit](media-services-portal-scale-streaming-endpoints.md) artikel voor meer informatie over het schalen van het streaming-eind punt.

## <a name="start-managing-streaming-endpoints"></a>Beginnen met het beheren van streaming-eind punten 

Ga als volgt te werk om het beheren van streaming-eind punten voor uw account te starten.

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in  de Blade instellingen **streaming-eind punten**.
   
    ![Scherm opname van de pagina Media Services met ' streaming-eind punten ' die is geselecteerd op de Blade instellingen.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Er worden alleen kosten in rekening gebracht wanneer het streaming-eind punt wordt uitgevoerd.

## <a name="adddelete-a-streaming-endpoint"></a>Een streaming-eind punt toevoegen/verwijderen

>[!NOTE]
>Het standaard-streaming-eind punt kan niet worden verwijderd.

Ga als volgt te werk om streaming-eind punt toe te voegen/te verwijderen met behulp van de Azure Portal:

1. Klik op het **plus punt** boven aan de pagina om een streaming-eind punt toe te voegen. 

    Mogelijk wilt u meerdere streaming-eind punten als u van plan bent verschillende Cdn's of een CDN en directe toegang te hebben.

2. Als u een streaming-eind punt wilt verwijderen, drukt u op de knop **verwijderen** .      
3. Klik op de knop **starten** om het streaming-eind punt te starten.
   
    ![Scherm afbeelding met de geselecteerde actie eind punt en het deel venster Details van streaming-eind punt wordt weer gegeven.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Het streaming-eind punt configureren
Met streaming-eind punt kunt u de volgende eigenschappen configureren:

* Toegangsbeheer
* Cachebeheer
* Beleid voor meerdere sites-toegang

Zie [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)voor meer informatie over deze eigenschappen.

>[!NOTE]
>Wanneer CDN is ingeschakeld, hebt u geen toegang tot IP-toegang. IP-toegang is alleen van toepassing als u geen CDN hebt.

U kunt het streaming-eind punt configureren door het volgende te doen:

1. Selecteer het streaming-eind punt dat u wilt configureren.
2. Klik op **Instellingen**.

Een korte beschrijving van de velden volgt.

![Scherm afbeelding met de geselecteerde actie voor het streaming-eind punt.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximum cache beleid: wordt gebruikt voor het configureren van de cache levensduur voor assets die via dit streaming-eind punt worden bediend. Als er geen waarde is ingesteld, wordt de standaard instelling gebruikt. De standaard waarden kunnen ook rechtstreeks in azure Storage worden gedefinieerd. Als Azure CDN is ingeschakeld voor het streaming-eind punt, moet u de waarde voor het cache beleid niet instellen op minder dan 600 seconden.  
2. Toegestane IP-adressen: deze worden gebruikt om IP-adressen op te geven die verbinding mogen maken met het gepubliceerde streaming-eind punt. Als er geen IP-adressen zijn opgegeven, zou een IP-adres verbinding kunnen maken. IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-bereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-bereik met IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld ' 10.0.0.1 (255.255.255.0) ').
3. Configuratie voor de verificatie van Akamai-handtekening header: wordt gebruikt om op te geven hoe de verificatie aanvraag voor de handtekening header van Akamai-servers is geconfigureerd. Verval datum is UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Uw Premium-streaming-eind punt schalen

Zie [Dit](media-services-portal-scale-streaming-endpoints.md) artikel voor meer informatie.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Integratie van Azure CDN inschakelen

Wanneer u een nieuw account maakt, wordt standaard streaming-eind punt Azure CDN integratie is standaard ingeschakeld.

Als u het CDN later wilt uitschakelen/inschakelen, moet het streaming-eind punt de status **gestopt** hebben. Het kan tot twee uur duren voordat de Azure CDN integratie is ingeschakeld en de wijzigingen voor alle CDN-Pop's actief zijn. Uw streaming-eind punt kan echter wel worden gestart zonder onderbreking van het streaming-eind punt en zodra de integratie is voltooid, wordt de stroom vanuit het CDN geleverd. Tijdens de inrichtings periode krijgt het streaming-eind punt de status **begin** en kan de prestaties afnemen.

CDN-integratie is ingeschakeld in alle Azure-data centers, met uitzonde ring van China en federale overheids regio's.

Als deze functie is ingeschakeld, worden de **Access Control**, * * aangepaste hostnaam en configuratie van **Akamai-handtekening verificatie** uitgeschakeld.
 
> [!IMPORTANT]
> Azure Media Services integratie met Azure CDN is geïmplementeerd op **Azure CDN van Verizon** voor Standard streaming-eind punten. Premium streaming-eind punten kunnen worden geconfigureerd met alle **Azure CDN prijs categorieën en providers**. Zie [overzicht van CDN](../../cdn/cdn-overview.md)voor meer informatie over Azure CDN-functies.
 
### <a name="additional-considerations"></a>Aanvullende overwegingen

* Wanneer CDN is ingeschakeld voor een streaming-eind punt, kunnen clients geen inhoud rechtstreeks aanvragen van de oorsprong. Als u de mogelijkheid wilt bieden om uw inhoud te testen met of zonder CDN, kunt u een ander streaming-eind punt maken dat niet is ingeschakeld voor CDN.
* De hostnaam van het streaming-eind punt blijft hetzelfde nadat u CDN hebt ingeschakeld. U hoeft geen wijzigingen aan te brengen in de werk stroom van Media Services nadat CDN is ingeschakeld. Als de hostnaam van het streaming-eind punt bijvoorbeeld strasbourg.streaming.mediaservices.windows.net is, wordt na het inschakelen van CDN exact dezelfde hostnaam gebruikt.
* Voor nieuwe streaming-eind punten kunt u CDN gewoon inschakelen door een nieuw eind punt te maken. voor bestaande streaming-eind punten moet u het eind punt eerst stoppen en vervolgens het CDN in-of uitschakelen.
* Standard streaming-eind punt kan alleen worden geconfigureerd met een **Verizon-standaard CDN-provider** met behulp van de klassieke Azure-Portal. U kunt echter andere Azure CDN providers inschakelen met behulp van REST Api's.

## <a name="configure-cdn-profile"></a>CDN-profiel configureren

U kunt het CDN-profiel configureren door de knop **CDN beheren** te selecteren vanaf de bovenkant.

![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
