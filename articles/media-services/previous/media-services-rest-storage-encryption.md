---
title: Uw inhoud versleutelen met opslag versleuteling met behulp van AMS REST API
description: Meer informatie over hoe u uw inhoud kunt versleutelen met opslag versleuteling met behulp van AMS REST-Api's.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ae1d19ee3da59c43722ca1ea720eb441f6dd484
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696207"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Uw inhoud versleutelen met opslag versleuteling

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > er geen nieuwe functies of functionaliteit aan Media Services v2 worden toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Dit artikel bevat een overzicht van de AMS-opslag versleuteling en laat zien hoe u de versleutelde inhoud kunt uploaden:

* Maak een inhoudssleutel.
* Een Asset maken. Stel de AssetCreationOption in op StorageEncryption bij het maken van de Asset.
  
     Versleutelde assets zijn gekoppeld aan inhouds sleutels.
* Koppel de inhouds sleutel aan de Asset.  
* Stel de versleutelings gerelateerde para meters in voor de AssetFile-entiteiten.

## <a name="considerations"></a>Overwegingen 

Als u een door opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren. Voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven leverings beleid. Zie beleid voor levering van [assets configureren](media-services-rest-configure-asset-delivery-policy.md)voor meer informatie.

Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie. 

### <a name="storage-side-encryption"></a>Versleuteling van opslag side

|Versleutelings optie|Beschrijving|Media Services v2|Media Services v3|
|---|---|---|---|
|Opslag versleuteling Media Services|AES-256-versleuteling, sleutel beheerd door Media Services|Ondersteund<sup>(1)</sup>|Niet ondersteund<sup>(2)</sup>|
|[Storage Service Encryption voor Data-at-rest](../../storage/common/storage-service-encryption.md)|Versleuteling aan de server zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door Azure of door de klant|Ondersteund|Ondersteund|
|[Versleuteling van opslag Client-Side](../../storage/common/storage-client-side-encryption.md)|Versleuteling aan de client zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door de klant in Key Vault|Niet ondersteund|Niet ondersteund|

<sup>1</sup> hoewel Media Services het verwerken van inhoud in de Clear/zonder vorm van versleuteling ondersteunt, wordt dit niet aanbevolen.

<sup>2</sup> in Media Services V3 wordt opslag VERSLEUTELING (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat v3 werkt met bestaande, versleutelde opslag assets, maar geen nieuwe apparaten mag maken.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="storage-encryption-overview"></a>Overzicht van opslag versleuteling
Met de AMS-opslag versleuteling wordt de **AES-** bewerkings modus versleuteling toegepast op het hele bestand.  AES-bewerkings modus is een blok versleuteling waarmee wille keurige lengte gegevens kunnen worden versleuteld zonder dat er opvulling hoeft te worden uitgevoerd. Er wordt gebruikgemaakt van het versleutelen van een counter Block met het AES-algoritme en vervolgens de uitvoer van AES met de gegevens die moeten worden versleuteld of ontsleuteld.  Het gebruikte meter item wordt samengesteld door de waarde van de InitializationVector te kopiëren naar bytes 0 tot en met 7 van de item waarde en de bytes 8 tot en met 15 van de item waarde worden ingesteld op nul. Van het 16-byte Counter Block, bytes 8 tot 15 (dat wil zeggen, de minst significante bytes) worden gebruikt als een eenvoudig 64-bits geheel getal zonder teken dat wordt verhoogd door één voor elk volgend gegevens blok dat wordt verwerkt en wordt in de byte volgorde van het netwerk bewaard. Als dit geheel getal de maximum waarde (0xFFFFFFFFFFFFFFFF) bereikt, wordt de blok teller opnieuw ingesteld op nul (bytes 8 tot 15) zonder dat dit van invloed is op de andere 64 bits van de teller (dat wil zeggen, bytes 0 tot en met 7).   Als u de beveiliging van de versleuteling van de AES-bewerkings modus wilt behouden, moet de InitializationVector-waarde voor een bepaalde sleutel-id voor elke inhouds sleutel uniek zijn voor elk bestand en moeten de bestanden minder dan 2 ^ 64 blokken lang zijn.  Deze unieke waarde is om ervoor te zorgen dat een item waarde nooit opnieuw wordt gebruikt met een bepaalde sleutel. Voor meer informatie over de plaats modus, Zie [deze wiki-pagina](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (in het wiki-artikel wordt de term ' nonce ' gebruikt in plaats van ' InitializationVector ').

Gebruik **opslag versleuteling** om uw ongecodeerde inhoud lokaal te versleutelen met AES-256-bits versleuteling en upload deze vervolgens naar Azure Storage waar deze wordt opgeslagen op rest. Assets die zijn beveiligd met opslag versleuteling, worden automatisch niet-versleuteld en in een versleuteld bestands systeem geplaatst vóór de code ring en eventueel opnieuw versleuteld voordat ze worden geüpload als een nieuwe uitvoer-Asset. Het primaire gebruiks voorbeeld voor opslag versleuteling is wanneer u uw invoer bestanden met een hoge kwaliteit wilt beveiligen met een sterke versleuteling van de schijf.

Als u een door opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven leverings beleid (bijvoorbeeld AES, algemene versleuteling of geen versleuteling).

## <a name="create-contentkeys-used-for-encryption"></a>ContentKeys maken die worden gebruikt voor versleuteling
Versleutelde assets zijn gekoppeld aan versleutelings sleutels voor opslag. Maak de inhouds sleutel die moet worden gebruikt voor versleuteling voordat u de Asset-bestanden maakt. In deze sectie wordt beschreven hoe u een inhouds sleutel maakt.

Hieronder vindt u algemene stappen voor het genereren van inhouds sleutels die u koppelt aan activa die u wilt versleutelen. 

1. Genereer voor opslag versleuteling wille keurig een 32-bytes AES-sleutel. 
   
    De 32-bytes AES-sleutel is de inhouds sleutel voor uw asset, wat betekent dat alle bestanden die aan die Asset zijn gekoppeld, dezelfde inhouds sleutel moeten gebruiken tijdens het ontsleutelen. 
2. Roep de methoden [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) en [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) aan om het juiste X. 509-certificaat op te halen dat moet worden gebruikt om uw inhouds sleutel te versleutelen.
3. Versleutel uw inhouds sleutel met de open bare sleutel van het X. 509-certificaat. 
   
   Media Services .NET SDK gebruikt RSA met OAEP wanneer de versleuteling wordt uitgevoerd.  U kunt een .NET-voor beeld bekijken in de [functie EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Een controlesom waarde maken die wordt berekend op basis van de sleutel-id en de inhouds sleutel. In het volgende .NET-voor beeld wordt de controlesom berekend met behulp van het GUID-gedeelte van de sleutel-id en de sleutel voor het wissen van inhoud.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Maak de inhouds sleutel met de **EncryptedContentKey** (geconverteerd naar base64-gecodeerde teken reeks), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** en **controlesom** waarden die u in de vorige stappen hebt ontvangen.

    Voor opslag versleuteling moeten de volgende eigenschappen worden opgenomen in de hoofd tekst van de aanvraag.

    Eigenschap aanvraag hoofdtekst    | Beschrijving
    ---|---
    Id | De ContentKey-ID wordt gegenereerd met de volgende indeling, "NB: Kid: UUID: \<NEW GUID> ".
    ContentKeyType | Het type inhouds sleutel is een geheel getal dat de sleutel definieert. Voor de indeling van opslag versleuteling is de waarde 1.
    EncryptedContentKey | We maken een nieuwe waarde voor de inhouds sleutel die een 256-bits (32 bytes) waarde is. De sleutel wordt versleuteld met het 509-certificaat dat wordt opgehaald uit Microsoft Azure Media Services door een HTTP GET-aanvraag voor de methoden GetProtectionKeyId en GetProtectionKey uit te voeren. Zie voor beeld de volgende .NET-code: de methode  **EncryptSymmetricKeyData** die [hier](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)is gedefinieerd.
    ProtectionKeyId | Dit is de beveiligings sleutel-ID voor het 509-certificaat dat is gebruikt voor het versleutelen van onze inhouds sleutel.
    ProtectionKeyType | Dit is het versleutelings type voor de beveiligings sleutel die is gebruikt voor het versleutelen van de inhouds sleutel. Deze waarde is StorageEncryption (1) voor ons voor beeld.
    Controlesom |De MD5-berekende controlesom voor de inhouds sleutel. Het wordt berekend door de inhouds-ID te versleutelen met de inhouds sleutel. De voorbeeld code laat zien hoe u de controlesom berekent.


### <a name="retrieve-the-protectionkeyid"></a>De ProtectionKeyId ophalen
In het volgende voor beeld ziet u hoe u de ProtectionKeyId, een certificaat vingerafdruk, voor het certificaat kunt ophalen dat u moet gebruiken bij het versleutelen van uw inhouds sleutel. Voer deze stap uit om ervoor te zorgen dat u het juiste certificaat al op uw computer hebt.

Aanvraag:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Reactie:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>De ProtectionKey voor de ProtectionKeyId ophalen
In het volgende voor beeld ziet u hoe u het X. 509-certificaat ophaalt met behulp van de ProtectionKeyId die u in de vorige stap hebt ontvangen.

Aanvraag:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Reactie:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>De inhouds sleutel maken
Nadat u het X. 509-certificaat hebt opgehaald en de open bare sleutel hebt gebruikt om uw inhouds sleutel te versleutelen, maakt u een **ContentKey** -entiteit en stelt u de bijbehorende eigenschaps waarden dienovereenkomstig in.

Een van de waarden die u moet instellen wanneer u de inhouds sleutel maakt, is van het type. Wanneer opslag versleuteling wordt gebruikt, moet de waarde worden ingesteld op 1. 

In het volgende voor beeld ziet u hoe u een **ContentKey** maakt met een **ContentKeyType** -set voor opslag versleuteling (' 1 ') en de **ProtectionKeyType** ingesteld op ' 0 ' om aan te geven dat de id van de beveiligings sleutel de X. 509-vinger afdruk van het certificaat is.  

Aanvraag

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Reactie:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>Een asset maken
In het volgende voor beeld ziet u hoe u een Asset maakt.

**HTTP-aanvraag**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>De ContentKey koppelen aan een Asset
Nadat u de ContentKey hebt gemaakt, koppelt u deze aan uw asset met behulp van de $links bewerking, zoals wordt weer gegeven in het volgende voor beeld:

Aanvraag:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Reactie:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Een AssetFile maken
De entiteit [AssetFile](/rest/api/media/operations/assetfile) vertegenwoordigt een video-of audio bestand dat is opgeslagen in een BLOB-container. Een activum bestand is altijd gekoppeld aan een Asset en een Asset kan een of meer Asset-bestanden bevatten. De Media Services encoder-taak mislukt als een object van een activa bestand niet is gekoppeld aan een digitaal bestand in een BLOB-container.

Het **AssetFile** -exemplaar en het daad werkelijke media bestand zijn twee verschillende objecten. Het AssetFile-exemplaar bevat meta gegevens over het Media bestand, terwijl het Media bestand de daad werkelijke media-inhoud bevat.

Nadat u uw digitale media bestand naar een BLOB-container hebt geüpload, gebruikt u de HTTP-aanvraag voor **samen voegen** om de AssetFile bij te werken met informatie over uw media bestand (niet weer gegeven in dit artikel). 

**HTTP-aanvraag**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-antwoord**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
