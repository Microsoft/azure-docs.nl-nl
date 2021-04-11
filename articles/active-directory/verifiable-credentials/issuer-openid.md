---
title: Communicatie voorbeelden van de uitgevers service (preview)-Azure Active Directory verifieer bare referenties
description: Details van de communicatie tussen de ID-provider en de uitgevers service
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8771c61f96b244e0cc0bca1c61ceb8042b4a5b4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220195"
---
# <a name="issuer-service-communication-examples-preview"></a>Communicatie voorbeelden van de uitgevers service (preview)

De verifieer bare referentie verleners-service kan verifieer bare referenties geven door claims op te halen uit een ID-token dat is gegenereerd door de OpenID Connect-ID-provider van uw organisatie. In dit artikel wordt beschreven hoe u uw ID-provider instelt zodat de verificator ermee kan communiceren en de juiste ID-token kan ophalen om door te geven aan de verlenende service. 

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


Als u een verifieer bare referentie wilt uitgeven, wordt de verificatie door de verificator gedownload om de invoer van de gebruiker te verzamelen en die informatie naar de uitgevende service te verzenden. Als u een ID-token moet gebruiken, moet u uw ID-provider instellen zodat verificator zich kan aanmelden bij een gebruiker met behulp van het OpenID Connect Connect-protocol. De claims in de resulterende ID-token worden gebruikt om de inhoud van uw verifieer bare referentie te vullen. Verificator verifieert de gebruiker met behulp van de OpenID Connect Connect-autorisatie code stroom. Uw OpenID Connect-provider moet ondersteuning bieden voor de volgende OpenID Connect Connect-functies: 

| Functie | Beschrijving |
| ------- | ----------- |
| Toekennings type | Het toekennings type voor autorisatie code moet worden ondersteund. |
| Token indeling | Er moet een niet-versleutelde compacte JWTs worden geproduceerd. |
| Handtekening algoritme | Moet JWTs ondertekend maken met RSA 256. |
| Configuratie document | Moet ondersteuning bieden voor OpenID Connect Connect-configuratie document en `jwks_uri` . | 
| Client registratie | De registratie van open bare clients moet worden ondersteund met een `redirect_uri` waarde van `vclient://openid/` . | 
| PKCE | Aanbevolen om veiligheids redenen, maar niet vereist. |

Hieronder vindt u enkele voor beelden van HTTP-aanvragen die worden verzonden naar uw ID-provider. Uw ID-provider moet deze aanvragen accepteren en hierop reageren in overeenstemming met de OpenID Connect Connect-verificatie standaard.

## <a name="client-registration"></a>Client registratie

Als u een verifieer bare referentie wilt ontvangen, moeten uw gebruikers zich aanmelden bij uw IDP via de app Microsoft Authenticator. 

Als u deze uitwisseling wilt inschakelen, moet u een toepassing registreren bij uw ID-provider. Als u Azure AD gebruikt, kunt u de instructies [hier](../develop/quickstart-register-app.md)vinden. Gebruik de volgende waarden bij het registreren.

| Instelling | Waarde |
| ------- | ----- |
| De naam van de toepassing | `<Issuer Name> Verifiable Credential Service` |
| Omleidings-URI | `vcclient://openid/ ` |


Nadat u een toepassing hebt geregistreerd bij uw ID-provider, noteert u de client-ID. U gebruikt deze in de volgende sectie. U moet ook de URL naar het bekende eind punt schrijven voor de OIDC Compatible ID-provider. De verlenende service gebruikt dit eind punt voor het downloaden van de open bare sleutels die nodig zijn om het ID-token te valideren zodra het door de verificator wordt verzonden.

De geconfigureerde omleidings-URI wordt gebruikt door de verificator, zodat deze weet wanneer de aanmelding is voltooid en het ID-token kan ophalen. 

## <a name="authorization-request"></a>Autorisatie aanvraag

De autorisatie aanvraag die naar uw ID-provider wordt verzonden, maakt gebruik van de volgende indeling.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parameter | Waarde |
| ------- | ----------- |
| `client_id` | De client-ID die is verkregen tijdens het registratie proces voor de toepassing. |
| `redirect_uri` | Moet worden gebruikt `vcclient://openid/` . |
| `response_mode` | Moet ondersteunen `query` . |
| `response_type` | Moet ondersteunen `code` . |
| `scope` | Moet ondersteunen `openid` . |
| `state` | Moet worden geretourneerd naar de client volgens de OpenID Connect Connect-standaard. |
| `nonce` | Moet worden geretourneerd als een claim in het ID-token volgens de OpenID Connect Connect-standaard. |

Wanneer het een autorisatie aanvraag ontvangt, moet uw ID-provider de gebruiker verifiëren en alle benodigde stappen ondernemen om de aanmelding te volt ooien, zoals multi-factor Authentication.

U kunt het aanmeldings proces aanpassen aan uw behoeften. U kunt gebruikers vragen om aanvullende informatie te verstrekken, Service voorwaarden te accepteren, te betalen voor de referentie en nog veel meer. Zodra alle stappen zijn voltooid, beantwoordt u de autorisatie aanvraag door naar de omleidings-URI te omleiden, zoals hieronder wordt weer gegeven. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parameter | Waarde |
| ------- | ----------- |
| `code` |  De autorisatie code die door uw ID-provider is geretourneerd. |
| `state` | Moet worden geretourneerd naar de client volgens de OpenID Connect Connect-standaard. |

## <a name="token-request"></a>Token aanvraag

De token aanvraag die naar uw ID-provider wordt verzonden, heeft de volgende vorm.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parameter | Waarde |
| ------- | ----------- |
| `client_id` | De client-ID die is verkregen tijdens het registratie proces voor de toepassing. |
| `redirect_uri` | Moet worden gebruikt `vcclient://openid/` . |
| `scope` | Moet ondersteunen `openid` . |
| `grant_type` | Moet ondersteunen `authorization_code` . |
| `code` | De autorisatie code die door uw ID-provider is geretourneerd. |

Wanneer de token aanvraag wordt ontvangen, moet uw ID-provider reageren met een ID-token.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Het ID-token moet de JWT compact serialisatie-indeling gebruiken en mag niet worden versleuteld. Het ID-token moet de volgende claims bevatten.

| Claim | Waarde |
| ------- | ----------- |
| `kid` | De sleutel-id van de sleutel die wordt gebruikt voor het ondertekenen van het ID-token, dat overeenkomt met een vermelding in de OpenID Connect-provider `jwks_uri` . |
| `aud` | De client-ID die is verkregen tijdens het registratie proces voor de toepassing. |
| `iss` | Moet de `issuer` waarde zijn in het OpenID Connect Connect-configuratie document. |
| `exp` | Moet de verloop tijd van het ID-token bevatten. |
| `iat` | Moet het tijdstip bevatten waarop het ID-token is uitgegeven. |
| `nonce` | De waarde die is opgenomen in de autorisatie aanvraag. |
| Aanvullende claims | Het ID-token moet aanvullende claims bevatten waarvan de waarden worden opgenomen in de verifieer bare referentie die wordt uitgegeven. In deze sectie moet u kenmerken over de gebruiker, zoals hun naam, toevoegen. |

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Active Directory verifieer bare referenties aanpassen](credential-design.md)