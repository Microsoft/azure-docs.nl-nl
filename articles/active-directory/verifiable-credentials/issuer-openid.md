---
title: Voorbeelden van communicatie van verlenerservice (preview) - Azure Active Directory Verifiable Credentials
description: Details van de communicatie tussen id-provider en verlenerservice
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 942b77f8338636f9dda5dcf6cd4262dad57b4b0a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726264"
---
# <a name="issuer-service-communication-examples-preview"></a>Voorbeelden van servicecommunicatie van verlener (preview)

De azure AD Verifiable Credential-service kan verifieerbare referenties uitgeven door claims op te haalt uit een id-token dat is gegenereerd door de OpenID-compatibele id-provider van uw organisatie. In dit artikel wordt beschreven hoe u uw id-provider in kunt stellen, zodat Authenticator hiermee kan communiceren en het juiste id-token kan ophalen om door te geven aan de verlenende service. 

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


Als u een verifieerbare referentie wilt uitgeven, krijgt Authenticator via het downloaden van het contract de opdracht om invoer van de gebruiker te verzamelen en die informatie naar de verlenende service te verzenden. Als u een id-token moet gebruiken, moet u uw id-provider instellen zodat Authenticator een gebruiker kan aanmelden met behulp van het OpenID Connect-protocol. De claims in het resulterende id-token worden gebruikt om de inhoud van uw verifieerbare referentie in te vullen. Authenticator verifieert de gebruiker met behulp van de OpenID Connect autorisatiecodestroom. Uw OpenID-provider moet de volgende OpenID Connect ondersteunen: 

| Functie | Beschrijving |
| ------- | ----------- |
| Toekenningstype | Moet het toekenningstype voor autorisatiecode ondersteunen. |
| Tokenindeling | Moet niet-versleutelde compacte JWT's produceren. |
| Handtekeningalgoritme | Moet JWT's produceren die zijn ondertekend met RSA 256. |
| Configuratiedocument | Moet ondersteuning OpenID Connect configuratiedocument en `jwks_uri` . | 
| Clientregistratie | Moet openbare clientregistratie ondersteunen met behulp van `redirect_uri` de waarde `vclient://openid/` . | 
| PKCE | Aanbevolen uit veiligheidsoverwegingen, maar niet vereist. |

Hieronder vindt u voorbeelden van de HTTP-aanvragen die naar uw id-provider worden verzonden. Uw id-provider moet deze aanvragen accepteren en hierop reageren in overeenstemming met OpenID Connect verificatiestandaard.

## <a name="client-registration"></a>Clientregistratie

Als u een verifieerbare referentie wilt ontvangen, moeten uw gebruikers zich aanmelden bij uw IDP vanuit de Microsoft Authenticator app. 

Als u deze uitwisseling wilt inschakelen, registreert u een toepassing bij uw id-provider. Als u Azure AD gebruikt, kunt u de instructies [hier vinden.](../develop/quickstart-register-app.md) Gebruik de volgende waarden bij het registreren.

| Instelling | Waarde |
| ------- | ----- |
| De naam van de toepassing | `<Issuer Name> Verifiable Credential Service` |
| Omleidings-URI | `vcclient://openid/ ` |


Nadat u een toepassing bij uw id-provider hebt geregistreerd, registreert u de client-id. U gebruikt deze in de volgende sectie. U moet ook de URL naar het bekende eindpunt voor de met OIDC compatibele id-provider schrijven. De verlenende service gebruikt dit eindpunt om de openbare sleutels te downloaden die nodig zijn om het id-token te valideren zodra het is verzonden door Authenticator.

De geconfigureerde omleidings-URI wordt gebruikt door Authenticator, zodat deze weet wanneer de aanmelding is voltooid en het id-token kan worden opgehaald. 

## <a name="authorization-request"></a>Autorisatieaanvraag

De autorisatieaanvraag die naar uw id-provider wordt verzonden, heeft de volgende indeling.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parameter | Waarde |
| ------- | ----------- |
| `client_id` | De client-id die is verkregen tijdens het registratieproces van de toepassing. |
| `redirect_uri` | Moet `vcclient://openid/` gebruiken. |
| `response_mode` | Moet ondersteuning bieden `query` voor . |
| `response_type` | Moet ondersteuning bieden `code` voor . |
| `scope` | Moet ondersteuning bieden `openid` voor . |
| `state` | Moet worden geretourneerd naar de client volgens de OpenID Connect standaard. |
| `nonce` | Moet worden geretourneerd als een claim in het id-token volgens OpenID Connect standaard. |

Wanneer een autorisatieaanvraag wordt ontvangen, moet uw id-provider de gebruiker verifiëren en de benodigde stappen nemen om de aanmelding te voltooien, zoals meervoudige verificatie.

U kunt het aanmeldingsproces aanpassen aan uw behoeften. U kunt gebruikers vragen aanvullende informatie te verstrekken, servicevoorwaarden te accepteren, te betalen voor hun referentie en meer. Zodra alle stappen zijn voltooid, reageert u op de autorisatieaanvraag door om te leiden naar de omleidings-URI, zoals hieronder wordt weergegeven. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parameter | Waarde |
| ------- | ----------- |
| `code` |  De autorisatiecode die wordt geretourneerd door uw id-provider. |
| `state` | Moet worden geretourneerd naar de client volgens de OpenID Connect standaard. |

## <a name="token-request"></a>Tokenaanvraag

De tokenaanvraag die naar uw id-provider wordt verzonden, heeft het volgende formulier.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parameter | Waarde |
| ------- | ----------- |
| `client_id` | De client-id die is verkregen tijdens het registratieproces van de toepassing. |
| `redirect_uri` | Moet `vcclient://openid/` gebruiken. |
| `scope` | Moet ondersteuning bieden `openid` voor . |
| `grant_type` | Moet ondersteuning bieden `authorization_code` voor . |
| `code` | De autorisatiecode die wordt geretourneerd door uw id-provider. |

Na ontvangst van de tokenaanvraag moet uw id-provider reageren met een id-token.

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

Het id-token moet de compacte JWT-serialisatie-indeling gebruiken en mag niet worden versleuteld. Het id-token moet de volgende claims bevatten.

| Claim | Waarde |
| ------- | ----------- |
| `kid` | De sleutel-id van de sleutel die wordt gebruikt om het id-token te ondertekenen, dat overeenkomt met een vermelding in de OpenID-provider. `jwks_uri` |
| `aud` | De client-id die is verkregen tijdens het registratieproces van de toepassing. |
| `iss` | Moet de waarde `issuer` in uw OpenID Connect zijn. |
| `exp` | Moet de verlooptijd van het id-token bevatten. |
| `iat` | Moet het tijdstip bevatten waarop het id-token is uitgegeven. |
| `nonce` | De waarde die is opgenomen in de autorisatieaanvraag. |
| Aanvullende claims | Het id-token moet aanvullende claims bevatten waarvan de waarden worden opgenomen in de verifieerbare referentie die wordt uitgegeven. In deze sectie moet u eventuele kenmerken van de gebruiker opnemen, zoals de naam. |

## <a name="next-steps"></a>Volgende stappen

- [Uw verifieerbare Azure Active Directory aanpassen](credential-design.md)
