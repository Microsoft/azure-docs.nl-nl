---
title: Bestanden uploaden naar een Azure Media Services-account met behulp van REST | Microsoft Docs
description: Meer informatie over het verkrijgen van media-inhoud in Media Services door assets te maken en te uploaden met behulp van REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012935"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Bestanden uploaden naar een Media Services-account met REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

In Media Services uploadt u de digitale bestanden naar (of neemt u deze op in) een asset. De [Asset](/rest/api/media/operations/asset) -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten.  Zodra de bestanden in de Asset zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming. 

In deze zelf studie leert u hoe u een bestand en een andere bewerking die eraan is gekoppeld, kunt uploaden:

> [!div class="checklist"]
> * Postman instellen voor alle upload bewerkingen
> * Verbinding met Media Services maken 
> * Een toegangs beleid maken met schrijf machtiging
> * Een asset maken
> * Een SAS-Locator maken en de upload-URL maken
> * Een bestand uploaden naar Blob Storage met behulp van de upload-URL
> * Een meta gegevens maken in de Asset voor het Media bestand dat u hebt geüpload

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- [Maak een Azure Media Services-account met behulp van de Azure Portal](media-services-portal-create-account.md).
- Raadpleeg het artikel [toegang tot Azure Media Services-API met Aad-verificatie](media-services-use-aad-auth-to-access-ams-api.md) .
- Zie ook voor meer informatie de [Azure AD-verificatie gebruiken om toegang te krijgen tot de Media Services-API met het rest](./media-services-rest-connect-with-aad.md) -artikel.
- Een **postman** configureren zoals beschreven in [postman configureren voor Media Services rest API-aanroepen](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing wanneer u Media Services REST API gebruikt:
 
* Bij het openen van entiteiten die gebruikmaken van Media Services REST API, moet u specifieke header velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie. <br/>De Postman-verzameling die in deze zelf studie wordt gebruikt, zorgt ervoor dat alle benodigde headers worden ingesteld.
* Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van Url's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}. Origin. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Daarom is het percentage code ring niet toegestaan. De waarde van de eigenschap **name** mag niet de volgende tekens voor [percentage versleuteling](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)bevatten:! * ' ();: @ &= + $,/?% # [] '. Daarnaast kan er slechts één '. ' zijn voor de bestandsnaam extensie.
* De lengte van de naam mag niet langer zijn dan 260 tekens.
* Er is een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. Raadpleeg [dit](media-services-quotas-and-limitations.md) artikel voor meer informatie over de maximale bestandsgrootte.

## <a name="set-up-postman"></a>Postman instellen

Zie voor meer informatie over het instellen van Postman voor deze zelf studie [postman configureren](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

1. Verbindings waarden toevoegen aan uw omgeving. 

    Sommige variabelen die deel uitmaken van de **Media Services** - [omgeving](postman-environment.md) , moeten hand matig worden ingesteld voordat u bewerkingen kunt uitvoeren die in de [verzameling](postman-collection.md)zijn gedefinieerd.

    Als u waarden voor de eerste vijf variabelen wilt ophalen, raadpleegt u [de Azure Media Services-API openen met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Scherm afbeelding met het pictogram ' tandwiel ' geselecteerd in de rechter bovenhoek en de eerste vijf variabelen die zijn geselecteerd op het tabblad beheer omgevingen.](./media/media-services-rest-upload-files/postman-import-env.png)
2. Geef de waarde op voor de omgevings variabele **MediaFileName** .

    Geef de bestands naam op van de media die u wilt uploaden. In dit voor beeld gaan we de BigBuckBunny.mp4 uploaden. 
3. Controleer de **AzureMediaServices.postman_environment.jsin** het bestand. U ziet dat bijna alle bewerkingen in de verzameling een test script uitvoeren. De scripts hebben een aantal waarden die worden geretourneerd door het antwoord en stellen geschikte omgevings variabelen in.

    Met de eerste bewerking wordt bijvoorbeeld een toegangs token opgehaald en ingesteld op de omgevings variabele **AccessToken** die wordt gebruikt in alle andere bewerkingen.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Klik aan de linkerkant van het venster **postman** op **1. Token voor Aad-verificatie ophalen** het  ->  **Azure AD-Token ophalen voor de Service-Principal**.

    Het URL-gedeelte wordt gevuld met de omgevings variabele **AzureADSTSEndpoint** (eerder in de zelf studie stelt u de waarden van omgevings variabelen in die ondersteuning bieden voor de verzameling).

    ![Scherm afbeelding waarin "1" wordt weer gegeven. Een D-verificatie Token ophalen: Azure A D-token voor Service-Principal ophalen, geselecteerd in het venster ' postman ' en de knop verzenden geselecteerd.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Druk op **Verzenden**.

    U ziet het antwoord met de access_token. Het script test neemt deze waarde en stelt de omgevings variabele **AccessToken** in (zoals hierboven beschreven). Als u de omgevings variabelen onderzoekt, ziet u dat deze variabele nu de toegangs token (Bearer-token) bevat die in de rest van de bewerkingen wordt gebruikt. 

    Als het token verloopt, gaat u verder met de stap ' Azure AD-token voor Service-Principal ophalen '. 

## <a name="create-an-access-policy-with-write-permission"></a>Een toegangs beleid maken met schrijf machtiging

### <a name="overview"></a>Overzicht 

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

Voordat u bestanden naar Blob Storage uploadt, stelt u de rechten voor het toegangs beleid in voor het schrijven naar een Asset. Hiertoe plaatst u een HTTP-aanvraag naar de AccessPolicies-entiteitset. Definieer een DurationInMinutes-waarde bij het maken of ontvang een fout bericht 500 interne server als reactie. Zie [AccessPolicy](/rest/api/media/operations/accesspolicy)voor meer informatie over AccessPolicies.

### <a name="create-an-access-policy"></a>Een toegangs beleid maken

1. Selecteer **AccessPolicy**  ->  **maken om AccessPolicy te uploaden**.
2. Druk op **Verzenden**.

    ![Scherm afbeelding met de optie ' AccessPolicy-AccessPolicy voor uploaden ', geselecteerd in het menu aan de linkerkant en de knop verzenden geselecteerd.](./media/media-services-rest-upload-files/postman-access-policy.png)

    Het script ' test ' haalt de AccessPolicy-id op en stelt de juiste omgevings variabele in.

## <a name="create-an-asset"></a>Een asset maken

### <a name="overview"></a>Overzicht

Een [Asset](/rest/api/media/operations/asset) is een container voor meerdere typen of verzamelingen van objecten in Media Services, zoals video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden. In de REST API vereist het maken van een Asset POST-aanvraag verzenden naar Media Services en het plaatsen van eigenschaps informatie over uw asset in de hoofd tekst van de aanvraag.

Een van de eigenschappen die u kunt toevoegen wanneer u een Asset maakt, is **Opties**. U kunt een van de volgende versleutelings opties opgeven: **geen** (standaard, er wordt geen versleuteling gebruikt), **StorageEncrypted** (voor inhoud die vooraf is versleuteld met opslag versleuteling aan de client zijde), **CommonEncryptionProtected** of **EnvelopeEncryptionProtected**. Wanneer u een versleuteld activum hebt, moet u een leverings beleid configureren. Zie beleid voor levering van [assets configureren](media-services-rest-configure-asset-delivery-policy.md)voor meer informatie.

Als uw asset is versleuteld, moet u een **ContentKey** maken en koppelen aan uw asset, zoals wordt beschreven in het volgende artikel: [een ContentKey maken](media-services-rest-create-contentkey.md). Nadat u de bestanden in de Asset hebt geüpload, moet u de versleutelings eigenschappen van de entiteit **AssetFile** bijwerken met de waarden die u tijdens de **Asset** Encryption hebt gekregen. Doe dit met behulp van de HTTP-aanvraag voor **samen voegen** . 

In dit voor beeld maken we een niet-versleutelde Asset. 

### <a name="create-an-asset"></a>Een asset maken

1. Selecteer **activa**  ->  **maken activa**.
2. Druk op **Verzenden**.

    ![Scherm opname van ' assets: activa maken ' geselecteerd in het menu verzamelingen en de knop verzenden geselecteerd.](./media/media-services-rest-upload-files/postman-create-asset.png)

    Het script test haalt de Asset-id op en stelt de juiste omgevings variabele in.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Een SAS-Locator maken en de upload-URL maken

### <a name="overview"></a>Overzicht

Zodra u de AccessPolicy en de Locator hebt ingesteld, wordt het daad werkelijke bestand geüpload naar een Azure Blob Storage-container met behulp van de Azure Storage REST-Api's. U moet de bestanden uploaden als blok-blobs. Pagina-blobs worden niet ondersteund door Azure Media Services.  

Zie [BLOB Service rest API](/rest/api/storageservices/blob-service-rest-api)voor meer informatie over het werken met Azure Storage-blobs.

Als u de daad werkelijke upload-URL wilt ontvangen, moet u een SAS-Locator maken (zie hieronder). Locators definiëren de begin tijd en het type van het verbindings eindpunt voor clients die toegang willen krijgen tot bestanden in een Asset. U kunt meerdere Locator-entiteiten maken voor een bepaald AccessPolicy en activa paar voor het afhandelen van verschillende client aanvragen en-behoeften. Elk van deze locators gebruikt de waarde StartTime plus de DurationInMinutes-waarde van de AccessPolicy om te bepalen hoe lang een URL kan worden gebruikt. Zie [Locator](/rest/api/media/operations/locator)voor meer informatie.

Een SAS-URL heeft de volgende indeling:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Overwegingen

Hierbij geldt het volgende:

* U kunt niet meer dan vijf unieke Locators tegelijk koppelen aan een bepaalde Asset. Zie Locator voor meer informatie.
* Als u uw bestanden onmiddellijk moet uploaden, moet u de waarde voor StartTime instellen op vijf minuten voor de huidige tijd. Dit komt omdat er mogelijk een klok verschil tussen uw client computer en Media Services. De waarde voor StartTime moet ook de volgende datum notatie hebben: JJJJ-MM-DDTUU: mm: ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").    
* Er kan een vertraging van 30-40 seconden optreden nadat een Locator is gemaakt op het moment dat deze beschikbaar is voor gebruik.

### <a name="create-a-sas-locator"></a>Een SAS-Locator maken

1. Selecteer een **Locator**  ->  **maken SAS-Locator**.
2. Druk op **Verzenden**.

    Met het script ' test ' wordt de ' upload-URL ' gemaakt op basis van de media bestands naam die u hebt opgegeven en de SAS-locatorgegevens en wordt de juiste omgevings variabele ingesteld.

    ![Scherm afbeelding met de optie ' Locator-Create S A S Locator ' geselecteerd in het menu ' verzamelingen ' en de knop verzenden geselecteerd.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Een bestand uploaden naar Blob Storage met behulp van de upload-URL

### <a name="overview"></a>Overzicht

Nu u de upload-URL hebt, moet u code schrijven met behulp van de Azure Blob Api's om uw bestand te uploaden naar de SAS-container. Raadpleeg voor meer informatie de volgende artikelen:

- [De Azure Storage REST API gebruiken](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB plaatsen](/rest/api/storageservices/put-blob)
- [Blobs uploaden naar Blob Storage](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Een bestand uploaden met de Postman

Als voor beeld gebruiken we postman om een klein MP4-bestand te uploaden. Er is mogelijk een maximale bestands grootte voor het uploaden van binaire bestanden via een postman.

De upload aanvraag maakt geen deel uit van de **Media** -verzameling. 

Een nieuwe aanvraag maken en instellen:
1. Klik op **+** om een nieuw tabblad aanvraag te maken.
2. Selecteer **put** -bewerking en plak **{{UploadURL}}** in de URL.
2. Laat het tabblad **autorisatie** ongewijzigd (niet instellen op het **Bearer-token**).
3. Geef op het tabblad **headers** : **Key**: "x-MS-BLOB-type" en **waarde**: "BlockBlob" op.
2. Klik op het tabblad **hoofd tekst** op **binair**.
4. Kies het bestand met de naam die u hebt opgegeven in de omgevings variabele **MediaFileName** .
5. Druk op **Verzenden**.

    ![Scherm opname van het tabblad ' (UploadU R L) ' is geselecteerd.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Een meta gegevens in de Asset maken

Zodra het bestand is geüpload, moet u een meta gegevens maken in de Asset voor het Media bestand dat u hebt geüpload naar de Blob-opslag die is gekoppeld aan uw asset.

1. Selecteer **AssetFiles**  ->  **CreateFileInfos**.
2. Druk op **Verzenden**.

    ![Een bestand uploaden](./media/media-services-rest-upload-files/postman-create-file-info.png)

Het bestand moet worden geüpload en de meta gegevens zijn ingesteld.

## <a name="validate"></a>Valideren

Als u wilt controleren of het bestand is geüpload, wilt u mogelijk een query uitvoeren op de [AssetFile](/rest/api/media/operations/assetfile) en de **ContentFileSize** (of andere details) vergelijken met wat u verwacht te zien in de nieuwe Asset. 

Met de volgende **Get** -bewerking worden bijvoorbeeld bestands gegevens voor uw asset-bestand (in of geval het BigBuckBunny.mp4-bestand) geplaatst. De query gebruikt de [omgevings variabelen](postman-environment.md) die u eerder hebt ingesteld.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Het antwoord bevat een grootte, naam en andere informatie.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.

U kunt ook Azure Functions gebruiken om een coderingstaak te activeren op basis van een bestand dat binnenkomt in de geconfigureerde container. Zie [dit voorbeeld](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) voor meer informatie.
