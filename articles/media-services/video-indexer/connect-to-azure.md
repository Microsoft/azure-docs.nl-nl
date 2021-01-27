---
title: Een Video Indexer-account maken dat is verbonden met Azure
titleSuffix: Azure Media Services
description: Meer informatie over het maken van een Video Indexer-account dat is verbonden met Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 8e110ba9818b48d66c5f17bb524bada567d808ab
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897153"
---
# <a name="create-a-video-indexer-account"></a>Een Video Indexer-account maken

Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij een gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met de betaalde optie maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement. U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

In dit artikel wordt beschreven hoe u een Video Indexer-account maakt dat is gekoppeld aan een Azure-abonnement en een Azure Media Services-account. Het onderwerp bevat stappen voor het maken van verbinding met Azure met behulp van de automatische stroom (standaard). Ook wordt uitgelegd hoe u hand matig verbinding maakt met Azure (Geavanceerd).

Als u van een *proef versie* overgaat naar een *betaalde* video indexer account, kunt u ervoor kiezen om alle Video's en het model aan te passen aan het nieuwe account, zoals wordt beschreven in de sectie [uw inhoud uit de proef account importeren](#import-your-content-from-the-trial-account) .

Het artikel heeft ook betrekking [op het koppelen van een video indexer-account aan Azure Government](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Vereisten voor het maken van verbinding met Azure

* Een Azure-abonnement.

    Als u nog geen Azure-abonnement hebt, meldt u zich aan voor een [gratis proef versie van Azure](https://azure.microsoft.com/free/).
* Een Azure Active Directory-domein (Azure AD).

    Als u geen Azure AD-domein hebt, maakt u dit domein met uw Azure-abonnement. Zie [aangepaste domein namen beheren in uw Azure AD](../../active-directory/enterprise-users/domains-manage.md) voor meer informatie.
* Een gebruiker in uw Azure AD-domein met een rol van **toepassings beheerder** . U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

    Deze gebruiker moet een Azure AD-gebruiker zijn met een werk-of school account. Gebruik geen persoonlijk account, zoals outlook.com, live.com of hotmail.com.

    ![alle Azure AD-gebruikers](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Aanvullende vereisten voor automatische stroom

* Een gebruiker en lid van uw Azure AD-domein.

    U gebruikt dit lid wanneer u uw Video Indexer-account verbindt met Azure.

    Deze gebruiker moet lid zijn van uw Azure-abonnement met ofwel een rol van **eigenaar** , ofwel beheerder rollen voor **Inzender** en **gebruikers toegang** . Een gebruiker kan twee maal worden toegevoegd met twee rollen. Eenmaal met Inzender en eenmaal met beheerder voor gebruikers toegang. Zie [de toegang tot een gebruiker tot Azure-resources weer geven](../../role-based-access-control/check-access.md)voor meer informatie.

    ![Toegangs beheer](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Aanvullende vereisten voor hand matige stroom

* Registreer de EventGrid-resource provider met behulp van de Azure Portal.

    Ga in [Azure Portal](https://portal.azure.com/)naar **abonnementen**-> [Subscription]->**ResourceProviders**.

    Zoek naar **micro soft. Media** en **micro soft. EventGrid**. Als dat niet het geval is, klikt u op **registreren**. Het duurt enkele minuten om u te registreren.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Een nieuw account maken in azure 

> [!NOTE]
> Als uw Azure-abonnement gebruikmaakt van multi-factor Authentication op basis van certificaten, is het van cruciaal belang dat u de volgende stappen uitvoert op een apparaat waarop de vereiste certificaten zijn geïnstalleerd.

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
1. Selecteer de knop **onbeperkt account maken** :

    ![Nieuw Video Indexer-account maken](./media/create-account/create-unlimited-account.png)
1. Wanneer de lijst abonnementen wordt weer gegeven, selecteert u het abonnement dat u wilt gebruiken.

    ![Video Indexer verbinden met Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Selecteer een Azure-regio op de locatie die wordt ondersteund: VS-West 2, Europa-noord of Azië-oost.
1. Kies onder **Azure Media Services account** een van de volgende opties:

    * Als u een nieuw Media Services account wilt maken, selecteert u **nieuwe resource groep maken**. Geef een naam op voor de resource groep.

        In azure wordt uw nieuwe account in uw abonnement gemaakt, met inbegrip van een nieuw Azure Storage-account.  
    * Als u een bestaand Media Services account wilt gebruiken, selecteert u **bestaande resource gebruiken**. Selecteer uw account in de lijst accounts.

        Uw Media Services-account moet dezelfde regio hebben als uw Video Indexer-account.

        > [!NOTE]
        > Om de indexerings duur en lage door voer te minimaliseren, wordt het ten zeerste aanbevolen om het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) in uw Media Services-account aan te passen aan **10 S3 gereserveerde eenheden**. Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md). De gereserveerde eenheden worden in rekening gebracht voor uw account. Bekijk [prijs gegevens](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Als u de verbinding hand matig wilt configureren, selecteert u de koppeling **overschakelen naar hand matige configuratie** .

        Zie de sectie [verbinding maken met Azure Manual](#connect-to-azure-manually-advanced-option) (geavanceerde optie) hieronder voor gedetailleerde informatie.
1. Wanneer u klaar bent, kiest u **Maken**. Deze bewerking kan enkele minuten duren.

    Nadat u verbinding hebt gemaakt met Azure, wordt uw nieuwe Video Indexer-account weer gegeven in de lijst met accounts:

    ![nieuw account](./media/create-account/new-account.png)
1. Zorg ervoor dat het streaming-eind punt van het Media Services-account wordt uitgevoerd voordat u uw Video's in de Video Indexer Web-App kunt afspelen (druk op Start als de status is gestopt).

> [!TIP]
> Ga naar **instellingen** om een gebruiks vriendelijke hostnaam voor uw account te geven.

## <a name="connect-to-azure-manually-advanced-option"></a>Hand matig verbinding maken met Azure (geavanceerde optie)

Als de verbinding met Azure is mislukt, kunt u proberen om het probleem op te lossen door hand matig verbinding te maken.

> [!NOTE]
> Het is raadzaam om de volgende drie accounts in dezelfde regio te hebben: het Video Indexer account waarmee u verbinding maakt met het Media Services account, evenals het Azure Storage-account dat is verbonden met hetzelfde Media Services-account.

### <a name="create-and-configure-a-media-services-account"></a>Een Media Services-account maken en configureren

1. Gebruik [Azure](https://portal.azure.com/) Portal om een Azure Media Services-account te maken, zoals wordt beschreven in [een account maken](../previous/media-services-portal-create-account.md).

     Zorg ervoor dat het Media Services-account is gemaakt met de klassieke Api's. 
 
    ![Klassieke API Media Services](./media/create-account/enable-classic-api.png)


    Wanneer u een opslag account voor uw Media Services-account maakt, selecteert u **StorageV2** voor de account soort en **geo-redundante** (GRS) voor de replicatie velden.

    ![Nieuw AMS-account](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Zorg ervoor dat u de Media Services resource-en account namen noteert. U hebt deze nodig voor de stappen in de volgende sectie.
1. Pas het type en het aantal [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md ) aan op **10 S3 gereserveerde eenheden** in het Media Services-account dat u hebt gemaakt. Zie [Portal gebruiken om gereserveerde eenheden te wijzigen](../previous/media-services-portal-scale-media-processing.md).

    De gereserveerde eenheden worden in rekening gebracht voor uw account en [prijs gegevens](https://azure.microsoft.com/pricing/details/media-services/#analytics)weer geven. s
1. Voordat u uw Video's kunt afspelen in de Video Indexer web-app, moet u het standaard **streaming-eind punt** van het nieuwe Media Services-account starten.

    Selecteer in het nieuwe Media Services account **streaming-eind punten**. Selecteer vervolgens het streaming-eind punt en klik op Start.

    ![Streaming-eindpunten](./media/create-account/create-ams-account-se.png)
4. Er moet een AD-App worden gemaakt om Video Indexer te verifiëren met Media Services-API. De volgende stappen leiden u door het Azure AD-verificatie proces dat wordt beschreven in [aan de slag met Azure AD-verificatie met behulp van de Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Selecteer in het nieuwe Media Services account **API-toegang**.
    2. Selecteer de [verificatie methode](../previous/media-services-portal-get-started-with-aad.md)voor de Service-Principal.
    3. De client-ID en het client geheim ophalen

        Nadat u **instellingen** -> **sleutels** hebt geselecteerd, voegt u **Beschrijving** toe, drukt u op **Opslaan** en de sleutel waarde wordt ingevuld.

        Als de sleutel verloopt, moet de eigenaar van het account contact opnemen met Video Indexer ondersteuning om de sleutel te vernieuwen.

        > [!NOTE]
        > Zorg ervoor dat u de sleutel waarde en de toepassings-ID noteert. U hebt deze nodig voor de stappen in de volgende sectie.

### <a name="connect-manually"></a>Hand matig verbinding maken

Selecteer in het dialoog venster **een nieuw account maken in een Azure-abonnement op** uw [video indexer](https://www.videoindexer.ai/) pagina de **optie overschakelen naar hand matige configuratie** .

Geef in het dialoog venster de volgende informatie op:

|Instelling|Beschrijving|
|---|---|
|Regio van Video Indexer-account|De naam van de regio van het Video Indexer-account. Voor betere prestaties en lagere kosten wordt u ten zeerste aangeraden de naam op te geven van de regio waar de Azure Media Services resource en het Azure Storage account zich bevinden. |
|Azure AD-tenant|De naam van de Azure AD-Tenant, bijvoorbeeld ' contoso.onmicrosoft.com '. De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechter bovenhoek. Zoek de naam rechts van het **domein**.|
|Abonnements-id|Het Azure-abonnement waarmee deze verbinding moet worden gemaakt. De abonnements-ID kan worden opgehaald uit de Azure Portal. Selecteer **alle services** in het linkerdeel venster en zoek naar ' Abonnementen '. Selecteer **abonnementen** en kies de gewenste id in de lijst met uw abonnementen.|
|Naam van de resource groep Azure Media Services|De naam voor de resource groep waarin u het Media Services-account hebt gemaakt.|
|Resource naam van media service|De naam van het Azure Media Services-account dat u in de vorige sectie hebt gemaakt.|
|Toepassings-id|De Azure AD-toepassings-ID (met machtigingen voor het opgegeven Media Services-account) dat u in de vorige sectie hebt gemaakt.|
|Toepassings sleutel|De Azure AD-toepassings sleutel die u in de vorige sectie hebt gemaakt. |

### <a name="import-your-content-from-the-trial-account"></a>De inhoud van de *proef* account importeren

Wanneer u een nieuw account maakt, hebt u de mogelijkheid om de inhoud van de *proef* account te importeren in het nieuwe account. Als u de optie *importeren* in het dialoog venster **een nieuw account maken in een Azure-abonnement** hebt ingeschakeld, worden alle aanpassingen van het media-en inhouds model gekopieerd van de *proef* account naar het nieuwe account.

De mogelijkheid om de inhoud te importeren is geldig voor zowel geautomatiseerde als hand matige benaderingen die hierboven worden beschreven.

> [!NOTE]
> De inhoud kan slechts eenmaal van elk account worden geïmporteerd.
>
> De *proef* account is niet availagle op de Azure Government Cloud.

## <a name="azure-media-services-considerations"></a>Azure Media Services overwegingen

De volgende Azure Media Services gerelateerde overwegingen zijn van toepassing:

* Als u van plan bent om verbinding te maken met een bestaand Media Services-account, controleert u of het Media Services-account is gemaakt met de klassieke Api's. 
 
    ![Klassieke API Media Services](./media/create-account/enable-classic-api.png)
* Als u verbinding maakt met een bestaand Media Services-account, Video Indexer de bestaande configuratie van **gereserveerde** media-eenheden niet wijzigen.

   Mogelijk moet u het type en het aantal gereserveerde media-eenheden aanpassen op basis van de geplande belasting. Als uw belasting hoog is en u onvoldoende eenheden of snelheid hebt, kan de video verwerking leiden tot time-outstoringen.
* Als u verbinding maakt met een nieuw Media Services-account, wordt het standaard **streaming-eind punt** in video indexer automatisch gestart:

    ![Media Services streaming-eind punt](./media/create-account/ams-streaming-endpoint.png)

    Streaming-eind punten hebben een aanzienlijke opstart tijd. Daarom kan het enkele minuten duren vanaf het moment dat u uw account hebt verbonden met Azure totdat uw Video's kunnen worden gestreamd en bekeken in de Video Indexer web-app.
* Als u verbinding maakt met een bestaand Media Services-account, Video Indexer de standaard configuratie van het onderkleuren van het eind punt niet wijzigen. Als er geen actief **streaming-eind punt** is, kunt u geen Video's bekijken uit dit Media Services-account of video indexer.
* Als u automatisch verbinding maakt, stelt Video Indexer de **gereserveerde** media-eenheden in op 10 S3-eenheden:

    ![Gereserveerde eenheden Media Services](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Het maken van het Video Indexer-account automatiseren

Om het maken van het account te automatiseren, is een proces van twee stappen:
 
1. Gebruik Azure Resource Manager om een Azure Media Services-account + Azure AD-toepassing te maken.

    Bekijk een voor beeld van de sjabloon voor het maken van het [Media Services-account](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. Roep [create-account aan met de Media Services-en Azure AD-toepassing](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account).

## <a name="video-indexer-in-azure-government"></a>Video Indexer in Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>Vereisten voor het maken van verbinding met Azure Government

-   Een Azure-abonnement in [Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Een Azure AD-account in Azure Government.
- Alle vooraf-vereisten van machtigingen en bronnen zoals hierboven beschreven in [vereisten voor het maken van verbinding met Azure](#prerequisites-for-connecting-to-azure).

### <a name="create-new-account-via-the-azure-government-portal"></a>Nieuw account maken via de Azure Government Portal

> [!NOTE]
> De Azure Government Cloud bevat geen *proef* ervaring van video indexer.

Een betaald account maken via de Video Indexer portal:

1. Ga naar https://videoindexer.ai.azure.us 
1. Meld u aan met uw Azure Government Azure AD-account.
1.  Als u geen Video Indexer accounts hebt in Azure Government waarbij u een eigenaar of bijdrager bent, krijgt u een lege ervaring waar u uw account kunt gaan maken. 

    De rest van de stroom is zoals hierboven is beschreven. alleen de regio's waaruit u kunt selecteren, zijn overheids regio's waarin video indexer beschikbaar is 

    Als u al een bijdrager of beheerder van een bestaand Video Indexer account in Azure Government bent, wordt u naar dat account geleid en kunt u aan de hand van de volgende stappen beginnen om een extra account te maken, zoals hierboven is beschreven.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Maak een nieuw account via de API op Azure Government

Als u een betaald account in Azure Government wilt maken, volgt u de instructies in [Create-betalen-account](https://api-portal.videoindexer.ai.azure.us/docs/services/Operations/operations/Create-Paid-Account). Dit eind punt API bevat alleen overheids Cloud regio's.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Beperkingen van Video Indexer op Azure Government

*   Er is geen hand matige toezicht op inhoud beschikbaar in de Government Cloud. 

    In de open bare Cloud wanneer inhoud op basis van een inhouds beheerder als aanstootgevend wordt beschouwd, kan de klant voor een persoon vragen om die inhoud te bekijken en deze beslissing mogelijk te herstellen.  
*   Geen proef accounts. 
* Bing description-in gov-Cloud geven we geen beschrijving van beroemdheden en benoemde entiteiten die worden geïdentificeerd. Dit is alleen de mogelijkheid van de gebruikers interface. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, verwijdert u de resources die u niet wilt gebruiken.

### <a name="delete-a-video-indexer-account"></a>Een Video Indexer account verwijderen

Als u een Video Indexer account wilt verwijderen, kunt u het account verwijderen van de Video Indexer-website. Als u het account wilt verwijderen, moet u de eigenaar zijn.

Selecteer de account-> **instellingen**  ->  **dit account verwijderen**. 

Het account wordt over 90 dagen definitief verwijderd.

## <a name="next-steps"></a>Volgende stappen

U kunt programmatisch communiceren met uw proef account en/of met uw Video Indexer-accounts die zijn verbonden met Azure door de instructies te volgen in: [Api's gebruiken](video-indexer-use-apis.md).

U moet dezelfde Azure AD-gebruiker gebruiken die u hebt gebruikt om verbinding te maken met Azure.
