---
title: Een Video Indexer account beheren
titleSuffix: Azure Media Services
description: Meer informatie over het beheren van een Video Indexer-account dat is verbonden met Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898440"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Een Video Indexer-account beheren dat is verbonden met Azure

In dit artikel wordt beschreven hoe u een Video Indexer-account kunt beheren dat is verbonden met uw Azure-abonnement en een Azure Media Services-account.

> [!NOTE]
> U moet de eigenaar van het Video Indexer account zijn voor het uitvoeren van aanpassingen van de account configuratie die in dit onderwerp worden beschreven.

## <a name="prerequisites"></a>Vereisten

Verbind uw Video Indexer-account met Azure, zoals beschreven in [verbonden met Azure](connect-to-azure.md).

Zorg ervoor dat u voldoet aan de [vereisten](connect-to-azure.md#prerequisites-for-connecting-to-azure) en Lees [overwegingen](connect-to-azure.md#azure-media-services-considerations) in het artikel.

## <a name="examine-account-settings"></a>Account instellingen controleren

In deze sectie worden de instellingen van uw Video Indexer-account onderzocht.

Instellingen weer geven:

1. Klik in de rechter bovenhoek op het pictogram van de gebruiker en selecteer **instellingen**.

    ![Instellingen in Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Selecteer op de pagina **instellingen** het tabblad **account** .

Als uw video indexer-account is verbonden met Azure, ziet u de volgende dingen:

* De naam van het onderliggende Azure Media Services-account.
* Het aantal uitgevoerde indexerings taken en de wachtrij.
* Het aantal en het type toegewezen gereserveerde eenheden.

Als uw account aanpassingen vereist, ziet u relevante fouten en waarschuwingen over uw account configuratie op de pagina **instellingen** . De berichten bevatten koppelingen naar exacte locaties in Azure Portal waar u wijzigingen moet aanbrengen. Zie de sectie met [fouten en waarschuwingen](#errors-and-warnings) voor meer informatie.

## <a name="repair-the-connection-to-azure"></a>De verbinding met Azure herstellen

In het dialoog venster **verbinding bijwerken met Azure Media Services** van uw [video indexer](https://www.videoindexer.ai/) pagina, wordt u gevraagd waarden op te geven voor de volgende instellingen:

|Instelling|Beschrijving|
|---|---|
|Azure-abonnements-ID|De abonnements-ID kan worden opgehaald uit de Azure Portal. Klik op **alle services** in het linkerdeel venster en zoek naar ' Abonnementen '. Selecteer **abonnementen** en kies de gewenste id in de lijst met uw abonnementen.|
|Naam van de resource groep Azure Media Services|De naam voor de resource groep waarin u het Media Services-account hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-ID (met machtigingen voor het opgegeven Media Services-account) dat u voor dit Video Indexer account hebt gemaakt. <br/><br/>Als u de App-ID wilt ophalen, gaat u naar Azure Portal. Kies onder het Media Services account uw account en ga naar **API-toegang**. Selecteer **verbinding maken met Media Services-API met Service-Principal**  ->  **Azure AD-App**. Kopieer de relevante para meters.|
|Toepassings sleutel|De Azure AD-toepassings sleutel die is gekoppeld aan uw Media Services account dat u hierboven hebt opgegeven. <br/><br/>Als u de app-sleutel wilt ophalen, gaat u naar Azure Portal. Kies onder het Media Services account uw account en ga naar **API-toegang**. Selecteer **verbinding maken met Media Services-API met Service-Principal**  ->  **toepassings**  ->  **certificaten beheren & geheimen**. Kopieer de relevante para meters.|

## <a name="autoscale-reserved-units"></a>Gereserveerde eenheden automatisch schalen

Op de pagina **instellingen** kunt u automatisch schalen instellen voor gereserveerde media-eenheden (ru). Als de optie is **ingeschakeld**, kunt u het maximum aantal aan Rus toewijzen en ervoor zorgen dat de video indexer automatisch Rus wordt gestopt/gestart. Met deze optie betaalt u geen extra geld voor niet-actieve tijd, maar u hoeft niet te wachten op het volt ooien van index taken wanneer de indexerings belasting hoog is.

Automatisch schalen kan niet lager dan 1 RU of hoger zijn dan de standaard limiet van het Media Services-account. Als u de limiet wilt verhogen, maakt u een service aanvraag. Zie [quota's en beperkingen](../../media-services/previous/media-services-quotas-and-limitations.md)voor meer informatie over quota en beperkingen en het openen van een ondersteunings ticket.

![Video Indexer automatisch schalen gereserveerde eenheden](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen

Als uw account aanpassingen vereist, ziet u relevante fouten en waarschuwingen over uw account configuratie op de pagina **instellingen** . De berichten bevatten koppelingen naar exacte locaties in Azure Portal waar u wijzigingen moet aanbrengen. In deze sectie vindt u meer informatie over de fout-en waarschuwings berichten.

* EventGrid

    U moet de EventGrid-resource provider registreren met behulp van de Azure Portal. Ga in het [Azure Portal](https://portal.azure.com/)naar **abonnementen** > [Subscription] > **ResourceProviders**  >  **micro soft. EventGrid**. Als dat **niet het geval** is, selecteert u **registreren**. Het duurt enkele minuten om u te registreren.

* Streaming-eindpunt

    Zorg ervoor dat de onderliggende Media Services account het standaard **streaming-eind punt** met de status gestart heeft. Als dat niet het geval is, kunt u geen Video's bekijken uit dit Media Services-account of in Video Indexer.

* Door media gereserveerde eenheden

    U moet gereserveerde media-eenheden toewijzen aan uw media service bron om Video's te kunnen indexeren. Voor optimale index prestaties is het raadzaam om ten minste 10 S3-gereserveerde eenheden toe te wijzen. Zie de sectie Veelgestelde vragen op de pagina met [prijzen voor Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor prijs informatie.

## <a name="next-steps"></a>Volgende stappen

U kunt programmatisch communiceren met uw proef account of Video Indexer accounts die zijn verbonden met Azure door de instructies te volgen in: [Api's gebruiken](video-indexer-use-apis.md).

Gebruik dezelfde Azure AD-gebruiker die u hebt gebruikt om verbinding te maken met Azure.
