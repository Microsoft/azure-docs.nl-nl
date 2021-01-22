---
title: Overzicht van Media Services bewerkingen REST API | Microsoft Docs
description: De API ' Media Services Operations REST ' wordt gebruikt voor het maken van taken, assets, Live kanalen en andere resources in een Media Services-account. In dit artikel vindt u een overzicht van Azure Media Services v2 REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: f48a01bb81829ff2bc10b4db1ed543382f992b58
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696224"
---
# <a name="media-services-operations-rest-api-overview"></a>Overzicht van Media Services bewerkingen REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

De REST-API van **Media Services bewerkingen** wordt gebruikt voor het maken van taken, assets, Live kanalen en andere resources in een Media Services-account. Zie [Media Services Operations rest API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference)(Engelstalig) voor meer informatie.

Media Services biedt een REST API die zowel JSON als Atom + pub XML-indeling accepteert. Media Services REST API vereist specifieke HTTP-headers die elke client moet verzenden wanneer er verbinding wordt gemaakt met Media Services, evenals een set optionele headers. In de volgende secties worden de kopteksten en HTTP-termen beschreven die u kunt gebruiken bij het maken van aanvragen en het ontvangen van antwoorden van Media Services.

Verificatie voor de Media Services REST API wordt uitgevoerd via Azure Active Directory verificatie. dit wordt beschreven in het artikel [Azure AD-verificatie gebruiken om toegang te krijgen tot de API van Azure Media Services met rest](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing wanneer u REST gebruikt.

* Bij het uitvoeren van een query op entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd, omdat open bare REST v2 de query resultaten beperkt tot 1000 resultaten. U moet **overs Laan** en **nemen** (.net)/ **Top** (rest) gebruiken, zoals beschreven in [dit .net-voor beeld](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) en [Dit rest API-voor beeld](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Wanneer u JSON gebruikt en opgeeft dat het sleutel woord **__metadata** wordt gebruikt in de aanvraag (bijvoorbeeld om te verwijzen naar een gekoppeld object), moet u de **Accept** -header instellen op [JSON-indeling](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (Zie het volgende voor beeld). Odata begrijpt de eigenschap **__metadata** niet in de aanvraag, tenzij u deze instelt op uitgebreid.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standaard-HTTP-aanvraag headers die worden ondersteund door Media Services
Voor elke aanroep die u in Media Services maakt, moet u een set vereiste kopteksten opnemen in uw aanvraag en ook een set optionele kopteksten die u mogelijk wilt opnemen. De volgende tabel bevat de vereiste headers:

| Header | Type | Waarde |
| --- | --- | --- |
| Autorisatie |Drager |Bearer is het enige geaccepteerde autorisatie mechanisme. De waarde moet ook het toegangs token bevatten dat door Azure Active Directory is geleverd. |
| x-ms-version |Decimaal |2,17 (of meest recente versie)|
| DataServiceVersion |Decimaal |3,0 |
| MaxDataServiceVersion |Decimaal |3,0 |

> [!NOTE]
> Omdat Media Services OData gebruikt om de REST-Api's weer te geven, moeten de DataServiceVersion-en MaxDataServiceVersion-headers worden opgenomen in alle aanvragen; Als dat niet het geval is, 3,0 wordt de DataServiceVersion-waarde Media Services die in gebruik is, echter gebruikt.
> 
> 

Hier volgt een aantal optionele kopteksten:

| Header | Type | Waarde |
| --- | --- | --- |
| Date |RFC 1123-datum |Tijds tempel van de aanvraag |
| Accepteren |Inhoudstype |Het aangevraagde inhouds type voor het antwoord, zoals de volgende:<p> -application/json; odata = verbose<p> -Application/Atom + XML<p> Antwoorden kunnen een ander inhouds type hebben, zoals het ophalen van een blob, waarbij een geslaagd antwoord de BLOB-stream als de payload bevat. |
| Accept-Encoding |Gzip, verkleinen |GZIP-en deflate-code ring, indien van toepassing. Opmerking: voor grote bronnen kan Media Services deze header negeren en niet-gecomprimeerde gegevens retour neren. |
| Accept-Language |"en", "ES", enzovoort. |Hiermee geeft u de voorkeurs taal op voor het antwoord. |
| Accept-Charset |Charset-type like "UTF-8" |De standaard waarde is UTF-8. |
| X-HTTP-methode |HTTP-methode |Biedt clients of firewalls die geen ondersteuning bieden voor HTTP-methoden zoals PUT of DELETE om deze methoden te gebruiken, via een GET-aanroep. |
| Content-Type |Inhoudstype |Inhouds type van de aanvraag tekst in PUT-of POST-aanvragen. |
| client-aanvraag-id |Tekenreeks |Een door een aanroeper gedefinieerde waarde die de opgegeven aanvraag aanduidt. Indien opgegeven, wordt deze waarde opgenomen in het antwoord bericht als een manier om de aanvraag toe te wijzen. <p><p>**Belangrijk**<p>De waarden moeten worden afgetopt op 2096b (2 KB). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standaard-HTTP-antwoord headers die worden ondersteund door Media Services
Hier volgt een reeks kopteksten die kunnen worden geretourneerd, afhankelijk van de resource die u hebt aangevraagd en de actie die u wilt uitvoeren.

| Header | Type | Waarde |
| --- | --- | --- |
| aanvraag-id |Tekenreeks |Een unieke id voor de huidige bewerking, gegenereerde service. |
| client-aanvraag-id |Tekenreeks |Een id die is opgegeven door de aanroeper in de oorspronkelijke aanvraag, indien aanwezig. |
| Date |RFC 1123-datum |De datum/tijd waarop de aanvraag is verwerkt. |
| Content-Type |Varieert |Het inhouds type van de antwoord tekst. |
| Content-Encoding |Varieert |Gzip of verkleinen, indien van toepassing. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standaard HTTP-termen die worden ondersteund door Media Services
Hier volgt een volledige lijst met HTTP-termen die kunnen worden gebruikt bij het maken van HTTP-aanvragen:

| Verb | Beschrijving |
| --- | --- |
| GET |Retourneert de huidige waarde van een object. |
| POST |Hiermee wordt een object gemaakt op basis van de verstrekte gegevens of wordt een opdracht verzonden. |
| PUT |Hiermee wordt een object vervangen of een benoemd object gemaakt (indien van toepassing). |
| DELETE |Hiermee verwijdert u een object. |
| SAMENVOEGEN |Hiermee wordt een bestaand object bijgewerkt met de benoemde eigenschaps wijzigingen. |
| HEAD |Retourneert meta gegevens van een object voor een GET-antwoord. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Het Media Services-entiteits model ontdekken en doorzoeken
Als u wilt dat Media Services entiteiten beter kunnen worden gedetecteerd, kan de $metadata bewerking worden gebruikt. U kunt alle geldige entiteits typen, entiteits eigenschappen, koppelingen, functies, acties, enzovoort ophalen. Door de $metadata bewerking toe te voegen aan het einde van uw Media Services REST API-eind punt, kunt u toegang krijgen tot deze detectie service.

 /API/$metadata.

Voeg '? API-Version = 2. x ' aan het einde van de URI toe als u de meta gegevens in een browser wilt weer geven of als u de header x-MS-version niet in uw aanvraag wilt gebruiken.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Verifiëren met Media Services REST met behulp van Azure Active Directory

Verificatie op de REST API geschiedt via Azure Active Directory (AAD).
Zie [toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het verkrijgen van de vereiste verificatie gegevens voor uw Media Services-account in azure Portal.

Zie voor meer informatie over het schrijven van code die verbinding maakt met de REST API met behulp van Azure AD-verificatie het artikel [Azure AD-verificatie gebruiken voor toegang tot de Azure Media Services API met rest](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Volgende stappen
Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot de API van Azure Media Services met rest](media-services-rest-connect-with-aad.md)voor meer informatie over het gebruik van Azure AD-verificatie met Media Services rest API.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
