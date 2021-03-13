---
title: Een autorisatie beleid voor de inhouds sleutel configureren met REST-Azure | Microsoft Docs
description: Meer informatie over het configureren van een verificatie beleid voor een inhouds sleutel met behulp van de Media Services REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 66649eb6109339cd7723ab4a7e3a12c5c322973d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017287"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dynamische versleuteling: een autorisatie beleid voor de inhouds sleutel configureren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Overzicht
 U kunt Azure Media Services gebruiken om uw inhoud (dynamisch) te versleutelen met de Advanced Encryption Standard (AES) door gebruik te maken van 128-bits coderings sleutels en PlayReady of Widevine digital rights management (DRM). Media Services biedt ook een service voor het leveren van sleutels en PlayReady/Widevine-licenties voor gemachtigde clients.

Als u Media Services een Asset wilt versleutelen, moet u een versleutelings sleutel (CommonEncryption of EnvelopeEncryption) koppelen aan de Asset. Zie [Create content keys with rest](media-services-rest-create-contentkey.md)(Engelstalig) voor meer informatie. U moet ook autorisatie beleid configureren voor de sleutel (zoals beschreven in dit artikel).

Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES of PlayReady-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. Het autorisatie beleid voor inhouds sleutels kan een of meer autorisatie beperkingen hebben door gebruik te maken van de beperking open of token. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen simple web token ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) en JSON Web token (JWT).

Media Services biedt geen STS. U kunt een aangepaste STS maken of Azure Active Directory (Azure AD) gebruiken voor het uitgeven van tokens. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking (zoals beschreven in dit artikel). Als het token geldig is en de claims in het token overeenkomen met die zijn geconfigureerd voor de inhouds sleutel, retourneert de Media Services key delivery service de versleutelings sleutel naar de client.

Raadpleeg voor meer informatie de volgende artikelen:
- [Verificatie JWT-token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Een Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van de JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Er zijn enkele overwegingen van toepassing
* Als u dynamische pakketten en dynamische versleuteling wilt gebruiken, moet u ervoor zorgen dat het streaming-eind punt van waaruit u uw inhoud wilt streamen, de status wordt uitgevoerd heeft.
* Uw asset moet een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten. Zie [een Asset coderen](media-services-encode-asset.md)voor meer informatie.
* Upload en codeer uw assets met de optie AssetCreationOptions. StorageEncrypted.
* Als u van plan bent meerdere inhouds sleutels te hebben waarvoor dezelfde beleids configuratie is vereist, raden we u aan om één autorisatie beleid te maken en het opnieuw te gebruiken met meerdere inhouds sleutels.
* De key delivery service slaat ContentKeyAuthorizationPolicy en de bijbehorende objecten (beleids opties en-beperkingen) gedurende 15 minuten op. U kunt ContentKeyAuthorizationPolicy maken en opgeven dat u een token beperking wilt gebruiken, deze wilt testen en vervolgens het beleid wilt bijwerken naar de beperking open. Dit proces duurt ongeveer 15 minuten voordat het beleid overschakelt naar de open-versie van het beleid.
* Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
* Op dit moment kunt u geen progressieve down loads versleutelen.
* Media Services streaming-eind punt stelt de waarde van het CORS Access-Control-Allow-Origin-header in het Preflight-antwoord in als het Joker teken \* . Deze waarde is goed geschikt voor de meeste spelers, waaronder Azure Media Player, Roku en JWPlayer, en andere. Sommige spelers die gebruikmaken van dash.js, werken echter niet omdat de XMLHttpRequest in hun dash.js niet het Joker teken ' \* "als waarde van Access-Control-Allow-Origin niet toestaat. Als u een tijdelijke oplossing hebt voor deze beperking in dash.js, kunt u, als u uw client vanuit één domein host, Media Services dat domein opgeven in de kopregel van het Preflight-antwoord. Open een ondersteunings ticket via de Azure Portal voor hulp.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dynamische versleuteling
> [!NOTE]
> Wanneer u werkt met de Media Services REST API, zijn de volgende overwegingen van toepassing.
> 
> Wanneer u entiteiten in Media Services opent, moet u specifieke header velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.
> 
> 
> 

### <a name="open-restriction"></a>Beperking openen
Beperking openen betekent dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden.

In het volgende voor beeld wordt een open autorisatie beleid gemaakt en toegevoegd aan de inhouds sleutel.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies"></a>ContentKeyAuthorizationPolicies maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 36

{"Name":"Open Authorization Policy"}
```

Reactie:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 211
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
request-id: aabfa731-e884-4bf3-8314-492b04747ac4
x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:25:56 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}
```

#### <a name="create-contentkeyauthorizationpolicyoptions"></a><a id="ContentKeyAuthorizationPolicyOptions"></a>ContentKeyAuthorizationPolicyOptions maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 168

{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

Reactie:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 349
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:56:40 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a><a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>ContentKeyAuthorizationPolicies koppelen aan opties
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 154

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}
```

Reactie:

```output
HTTP/1.1 204 No Content
```

#### <a name="add-an-authorization-policy-to-the-content-key"></a><a id="AddAuthorizationPolicyToKey"></a>Een autorisatie beleid toevoegen aan de inhouds sleutel
Aanvraag:

```console
PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 78

{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}
```

Reactie:

```output
HTTP/1.1 204 No Content
```

### <a name="token-restriction"></a>Token beperking
In deze sectie wordt beschreven hoe u een autorisatie beleid voor de inhouds sleutel maakt en het koppelt aan de inhouds sleutel. In het autorisatie beleid wordt beschreven aan welke autorisatie vereisten moet worden voldaan om te bepalen of de gebruiker gemachtigd is om de sleutel te ontvangen. Bevat de lijst met verificatie sleutels bijvoorbeeld de sleutel waarmee het token is ondertekend?

Als u de optie voor de token beperking wilt configureren, moet u een XML gebruiken om de autorisatie vereisten van het token te beschrijven. De configuratie-XML van de token beperking moet voldoen aan het volgende XML-schema:


#### <a name="token-restriction-schema"></a><a id="schema"></a>Schema voor token beperking
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:complexType name="TokenClaim">
    <xs:sequence>
      <xs:element name="ClaimType" nillable="true" type="xs:string" />
      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
  <xs:complexType name="TokenRestrictionTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
  <xs:complexType name="ArrayOfTokenVerificationKey">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
  <xs:complexType name="TokenVerificationKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
  <xs:complexType name="ArrayOfTokenClaim">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
  <xs:complexType name="SymmetricVerificationKey">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:TokenVerificationKey">
        <xs:sequence>
          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
</xs:schema>
```

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doel groep (ook wel bereik genoemd) beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

In het volgende voor beeld wordt een autorisatie beleid met een token beperking gemaakt. In dit voor beeld moet de client een token aanbieden dat de ondertekeningssleutel (VerificationKey), een token Uitgever en de vereiste claims bevat.

### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies maken
Maak een beleid voor het beperken van tokens, zoals wordt weer gegeven in de sectie '[ContentKeyAuthorizationPolicies maken](#ContentKeyAuthorizationPolicies)'.

### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1079

{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Reactie:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1260
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:10:37 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies koppelen aan opties
Koppel ContentKeyAuthorizationPolicies aan opties, zoals wordt weer gegeven in de sectie '[ContentKeyAuthorizationPolicies maken](#ContentKeyAuthorizationPolicies)'.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Een autorisatie beleid toevoegen aan de inhouds sleutel
Voeg AuthorizationPolicy toe aan ContentKey, zoals wordt weer gegeven in de sectie '[een autorisatie beleid toevoegen aan de inhouds sleutel](#AddAuthorizationPolicyToKey)'.

## <a name="playready-dynamic-encryption"></a>PlayReady dynamische versleuteling
U kunt Media Services gebruiken om de rechten en beperkingen te configureren die u wilt dat de PlayReady DRM-runtime wordt afgedwongen wanneer een gebruiker beveiligde inhoud wil afspelen. 

Wanneer u uw inhoud beveiligt met PlayReady, is een van de dingen die u in uw autorisatie beleid moet opgeven, een XML-teken reeks die de [PlayReady-licentie sjabloon](media-services-playready-license-template-overview.md)definieert. 

### <a name="open-restriction"></a>Beperking openen
Beperking openen betekent dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden.

In het volgende voor beeld wordt een open autorisatie beleid gemaakt en toegevoegd aan de inhouds sleutel.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies2"></a>ContentKeyAuthorizationPolicies maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 58

{"Name":"Deliver Common Content Key"}
```

Reactie:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 233
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:26:00 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
```


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 593

{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

Reactie:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 774
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:23:24 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies koppelen aan opties
Koppel ContentKeyAuthorizationPolicies aan opties, zoals wordt weer gegeven in de sectie '[ContentKeyAuthorizationPolicies maken](#ContentKeyAuthorizationPolicies)'.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Een autorisatie beleid toevoegen aan de inhouds sleutel
Voeg AuthorizationPolicy toe aan ContentKey, zoals wordt weer gegeven in de sectie '[een autorisatie beleid toevoegen aan de inhouds sleutel](#AddAuthorizationPolicyToKey)'.

### <a name="token-restriction"></a>Token beperking
Als u de optie voor de token beperking wilt configureren, moet u een XML gebruiken om de autorisatie vereisten van het token te beschrijven. De configuratie-XML van de token beperking moet voldoen aan het XML-schema dat wordt weer gegeven in de sectie "[token restrictie schema](#schema)".

#### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies maken
Maak ContentKeyAuthorizationPolicies, zoals wordt weer gegeven in de sectie '[ContentKeyAuthorizationPolicies maken](#ContentKeyAuthorizationPolicies2)'.

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions maken
Aanvraag:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1525

{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Reactie:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1706
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
request-id: ccf8a4ba-731e-4124-8192-079592c251cc
x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:58:47 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies koppelen aan opties
Koppel ContentKeyAuthorizationPolicies aan opties, zoals wordt weer gegeven in de sectie '[ContentKeyAuthorizationPolicies maken](#ContentKeyAuthorizationPolicies)'.

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Een autorisatie beleid toevoegen aan de inhouds sleutel
Voeg AuthorizationPolicy toe aan ContentKey, zoals wordt weer gegeven in de sectie '[een autorisatie beleid toevoegen aan de inhouds sleutel](#AddAuthorizationPolicyToKey)'.

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typen die worden gebruikt wanneer u ContentKeyAuthorizationPolicy definieert
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
```csharp
public enum ContentKeyRestrictionType
{
    Open = 0,
    TokenRestricted = 1, 
    IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
}
```


> [!NOTE]
> De IP-beperking voor het autorisatie beleid voor inhouds sleutels is nog niet beschikbaar in de service.


### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
```csharp
public enum ContentKeyDeliveryType
{
    None = 0,
    PlayReadyLicense = 1,
    BaselineHttp = 2,
    Widevine = 3
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u het autorisatie beleid voor de inhouds sleutel hebt geconfigureerd, raadpleegt u beleid voor het [leveren van assets configureren](media-services-rest-configure-asset-delivery-policy.md).
