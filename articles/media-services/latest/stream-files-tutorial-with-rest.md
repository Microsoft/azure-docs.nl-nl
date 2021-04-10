---
title: Een extern bestand en een stream coderen met Media Services
description: Volg de stappen van deze zelfstudie om met behulp van REST een bestand te coderen op basis van een URL en inhoud te streamen met Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 139970bb043c745d63f2ef795ae1c8aef4bda0fa
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108109"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Zelfstudie: Extern bestand coderen op basis van URL en video streamen - REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u mediabestanden coderen in indelingen die kunnen worden afgespeeld met een groot aantal verschillende browsers en apparaten. Zo kunt u bijvoorbeeld inhoud streamen in de indelingen Apple HLS of MPEG DASH. Voordat u gaat streamen, moet u uw digitale mediabestand van hoge kwaliteit coderen. Zie [Encoding](encode-concept.md) voor richtlijnen voor codering.

In deze zelfstudie leert u hoe u met behulp van REST een bestand kunt coderen en de video kunt streamen met Azure Media Services. 

![De video afspelen](./media/stream-files-tutorial-with-api/final-video.png)

In deze zelfstudie ontdekt u hoe u:    

> [!div class="checklist"]
> * Een Media Services-account kunt maken
> * Toegang kunt krijgen tot de Media Services API
> * Postman-bestanden downloaden
> * Postman configureren
> * Aanvragen verzenden met behulp van Postman
> * De streaming-URL testen
> * Resources opschonen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](./account-create-how-to.md).

    Vergeet niet welke waarden u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account

- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Enkele andere alternatieven: **Visual Studio Code** met de REST-invoegtoepassing of **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Postman-bestanden downloaden

Kloon een GitHub-opslagplaats die de Postman verzameling en -omgevingsbestanden bevat.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>Toegang tot API

Zie [Referenties ophalen voor toegang tot de Media Services API ](access-api-howto.md) voor meer informatie

## <a name="configure-postman"></a>Postman configureren

### <a name="configure-the-environment"></a>De omgeving configureren 

1. Open de **Postman**-app.
2. Selecteer rechts van het scherm de optie **Manage environment**.

    ![Omgeving beheren](./media/develop-with-postman/postman-import-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
2. Blader naar het bestand `Azure Media Service v3 Environment.postman_environment.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde.
6. De omgeving **Azure Media Service v3 Environment** is toegevoegd.

    > [!Note]
    > Werk de toegangsvariabelen bij met waarden die u hebt gekregen in de sectie **Toegang krijgen tot de Media Services API** hierboven.

7. Dubbelklik op het geselecteerde bestand en voer de waarden in die u hebt verkregen door de stappen voor het verkrijgen van [toegang tot API's](#access-api) te volgen.
8. Sluit het dialoogvenster.
9. Selecteer de omgeving **Azure Media Service v3 Environment** in de vervolgkeuzelijst.

    ![Omgeving kiezen](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>De collectie configureren

1. Klik op **Import** om het verzamelingbestand te importeren.
1. Blader naar het bestand `Media Services v3.postman_collection.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde
3. Kies het bestand **Media Services v3.postman_collection.json**.

    ![Een bestand importeren](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Aanvragen verzenden met behulp van Postman

In deze sectie verzenden we aanvragen die relevant zijn voor het coderen en maken van URL's zodat uw bestand kan worden gestreamd. In het bijzonder worden de volgende aanvragen verzonden:

1. Azure AD-token verkrijgen voor service-principal-verificatie
1. Een streaming-eindpunt starten
2. Een uitvoeractivum maken
3. Een transformatie maken
4. Een taak maken
5. Een streaming-locator maken
6. De paden van de streaming-locator weergeven

> [!Note]
>  In deze zelfstudie wordt ervan uitgegaan dat u alle resources maakt met unieke namen.  

### <a name="get-azure-ad-token"></a>Azure AD-token verkrijgen 

1. Selecteer 'Stap 1' in het linkervenster van de Postman-app: Get AAD Auth token'.
2. Selecteer vervolgens Get Azure AD Token for Service Principal Authentication.
3. Druk op **Verzenden**.

    De volgende **POST**-bewerking wordt verzonden.

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. Het antwoord bevat de token en stelt de omgevingsvariabele AccessToken in op de tokenwaarde. Klik op het tabblad **Tests** voor de code waarmee AccessToken wordt ingesteld. 

    ![AAD-token verkrijgen](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Een streaming-eindpunt starten

Als u streaming wilt inschakelen, moet u eerst het [streaming-eindpunt](./streaming-endpoint-concept.md) starten op de locatie van waaruit u de video wilt streamen.

> [!NOTE]
> U wordt alleen gefactureerd wanneer uw streaming-eindpunt actief is.

1. Selecteer 'Streaming en live' in het linkervenster van de Postman-app.
2. Selecteer vervolgens 'Streaming-eindpunt starten'.
3. Druk op **Verzenden**.

    * De volgende **POST**-bewerking wordt verzonden:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Als de aanvraag is geaccepteerd, wordt `Status: 202 Accepted` geretourneerd.

        Deze status betekent dat de aanvraag is geaccepteerd voor verwerking. De verwerking is echter niet voltooid. U kunt de bewerkingsstatus opvragen op basis van de waarde in de `Azure-AsyncOperation`-antwoordheader.

        Met de volgende GET-bewerking wordt bijvoorbeeld de status van uw bewerking geretourneerd:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        In het artikel [Asynchrone Azure-bewerkingen volgen](../../azure-resource-manager/management/async-operations.md) wordt uitvoerig beschreven hoe de status van asynchrone Azure-bewerkingen wordt gevolgd aan de hand van waarden die zijn geretourneerd in het antwoord.

### <a name="create-an-output-asset"></a>Een uitvoeractivum maken

In de [uitvoerasset](/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. 

1. Selecteer 'Assets' in het linkervenster van de Postman-app.
2. Selecteer vervolgens Create or update an Asset.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> Zorg ervoor dat u de namen van het opslagaccount en de container vervangt door de namen uit het omgevingsbestand, of geef zelf namen op.
>
> Wanneer u de stappen uit de rest van het artikel hebt uitgevoerd, zorgt u ervoor dat u geldige parameters opgeeft in de hoofdtekst van de aanvragen.

### <a name="create-a-transform"></a>Een transformatie maken

Bij het coderen of verwerken van inhoud in Media Services is het een gangbaar patroon om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u dat recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt aangeroepen als een **transformatie**. Zie [Transformaties en taken](./transforms-jobs-concept.md) voor meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten. 

Bij het maken van een nieuw [transformatie](/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid en produceert ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar. Zie [een bitrate-ladder automatisch genereren](encode-autogen-bitrate-ladder.md) voor meer informatie over deze voorinstelling.

U kunt een ingebouwde EncoderNamedPreset gebruiken of aangepaste voorinstellingen gebruiken. 

> [!Note]
> Bij het maken van een [transformatie](/rest/api/media/transforms) moet u met de methode **Get** eerst controleren of er al een bestaat. In deze zelfstudie wordt ervan uitgegaan dat u alle transformaties maakt met een unieke naam.

1. Selecteer 'Codering en analyse' in het linkervenster van de Postman-app.
2. Selecteer daarna Create Transform.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Een taak maken

Een [taak](/rest/api/media/jobs) is de eigenlijke aanvraag aan Media Services om de gemaakte **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In dit voorbeeld is de invoer van de taak gebaseerd op een HTTPS-URL (https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/).

1. Selecteer 'Codering en analyse' in het linkervenster van de Postman-app.
2. Selecteer vervolgens Create or Update Job.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. Als u de voortgang van de taak wilt zien, kunt u het best Event Grid gebruiken. Dit is ontworpen voor hoge beschikbaarheid, consistente prestaties en dynamische schaalbaarheid. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. Eenvoudige, op HTTP gebaseerde reactieve gebeurtenisafhandeling maakt het mogelijk om efficiënte oplossingen te bouwen met intelligente filtering en routering van gebeurtenissen.  Zie [Gebeurtenissen routeren naar een aangepast eindpunt](monitoring/job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **Gepland**, **In de wachtrij geplaatst**, **Verwerken**, **Voltooid** (de eindstatus). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de melding **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

#### <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Een streaming-locator te maken

Wanneer de coderingstaak is voltooid, gaat u in de volgende stap de video in de uitvoer **asset** beschikbaar maken voor weergave door clients. U kunt dit doen in twee stappen: maak eerst een [StreamingLocator](/rest/api/media/streaminglocators) en bouw vervolgens de streaming-URL's die clients kunnen gebruiken. 

Het maken van een streaming-locator wordt publiceren genoemd. De streaming-locator is standaard onmiddellijk geldig nadat u de API-aanroepen hebt uitgevoerd en totdat deze wordt verwijderd, tenzij u de optionele start- en eindtijden configureert. 

Bij het maken van een [StreamingLocator](/rest/api/media/streaminglocators) moet u de gewenste **StreamingPolicyName** opgeven. In dit voorbeeld streamt u niet-versleutelde inhoud, zodat het vooraf gedefinieerde beleid voor het streamen van niet-versleutelde inhoud, Predefined_ClearStreamingOnly, wordt gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. 

Uw Media Service-account heeft een quotum voor het aantal **streaming-beleidsvermeldingen**. U hoeft geen nieuw **streamingbeleid** te maken voor elke streaming-locator.

1. Selecteer 'Streamingbeleid en locators' in het linkervenster van de Postman-app.
2. Selecteer vervolgens 'Een streaming-locator maken (voor niet-versleutelde inhoud)'.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Paden weergeven en streaming-URL's maken

#### <a name="list-paths"></a>Paden weergeven

Nu de [streaming-locator](/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen

1. Selecteer 'Streamingbeleid' in het linkervenster van de Postman-app.
2. Selecteer vervolgens List Paths.
3. Druk op **Verzenden**.

    * De volgende **POST**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * De bewerking heeft geen hoofdtekst:
        
4. Noteer een van de paden die u voor streaming wilt gebruiken. U hebt deze nodig in de volgende sectie. In dit geval worden de volgende paden geretourneerd:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>De streaming-URL's maken

In deze sectie gaat u een URL voor HLS-streaming maken. URL's bestaan uit de volgende waarden:

1. Het protocol waarmee gegevens worden verzonden. In dit geval 'https'.

    > [!NOTE]
    > Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

2. De hostnaam van het StreamingEndpoint. In dit geval is de naam 'amsaccount-usw22.streaming.media.azure.net'.

    U kunt de volgende GET-bewerking gebruiken om de hostnaam op te halen:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    en zorg ervoor dat u de parameters `resourceGroupName` en `accountName` zo instelt dat ze overeenkomen met het omgevingsbestand. 
    
3. Een pad dat u in de vorige sectie (Paden weergeven) hebt verkregen.  

Als resultaat wordt de volgende HLS-URL gemaakt

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>De streaming-URL testen


> [!NOTE]
> Controleer of het **streaming-eindpunt** vanwaar u wilt streamen, wordt uitgevoerd.

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in de gemaakte URL in het vak **URL:** . 
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent te hergebruiken (meestal gebruikt u **transformaties** opnieuw en behoudt u **streaming-locators**, enzovoort). Als u wilt dat uw account na het experiment is opgeschoond, moet u de resources verwijderen die u niet van plan bent te hergebruiken.  

Als u een resource wilt verwijderen, selecteert u de Delete-bewerking onder de resource die u wilt verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.  

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw video kunt uploaden, coderen en streamen, kunt u doorgaan naar het volgende artikel: 

> [!div class="nextstepaction"]
> [Video's analyseren](analyze-videos-tutorial.md)
