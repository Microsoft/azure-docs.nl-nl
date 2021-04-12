---
title: Uw Azure Active Directory verifieer bare referenties aanpassen (preview-versie)
description: Dit artikel laat u zien hoe u uw eigen aangepaste verifieer bare referentie maakt
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222925"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Uw verifieer bare referenties aanpassen (preview-versie)

Controleer bare referenties bestaan uit twee onderdelen, de regels en weergave bestanden. Het regel bestand bepaalt wat de gebruiker moet opgeven voordat ze een verifieer bare referentie ontvangen. Het weergave bestand bepaalt de huis stijl van de referentie en de stijl van de claims. In deze hand leiding wordt uitgelegd hoe u beide bestanden kunt aanpassen om te voldoen aan de vereisten van uw organisatie. 

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="rules-file-requirements-from-the-user"></a>Regel bestand: vereisten van de gebruiker

Het regel bestand is een eenvoudig JSON-bestand dat belang rijke eigenschappen van Controleer bare referenties beschrijft. In het bijzonder wordt beschreven hoe claims worden gebruikt om uw verifieer bare referentie te vullen.

Er zijn momenteel drie invoer typen die in het regel bestand kunnen worden geconfigureerd. Deze typen worden gebruikt door de verifieer bare referentie verleners om claims in te voegen in een verifieer bare referentie en te voldoen aan die informatie met uw. Hier volgen de drie typen met uitleg.

- ID-token
- Verifieer bare referenties via een verifieer bare presentatie.
- Self-Attested claims

**Id-token:** De voor beeld-app en zelf studie gebruiken de ID-token. Wanneer deze optie is geconfigureerd, moet u een open ID-URI voor de Connect-configuratie opgeven en de claims opnemen die moeten worden opgenomen in het VC. De gebruiker wordt gevraagd om zich aan te melden bij de verificator-app om aan deze vereiste te voldoen en de bijbehorende claims van het account toe te voegen. 

**Verifieer bare referenties:** Het eind resultaat van een uitgifte stroom is het produceren van een verifieer bare referentie, maar u kunt de gebruiker ook vragen een verifieer bare referentie op te geven om er een te verlenen. Het regel bestand kan specifieke claims van de gepresenteerde verifieer bare referentie uitvoeren en deze claims opnemen in de zojuist verleende, geverifieerde referentie van uw organisatie. 

**Zelf attestende claims:** Als deze optie is geselecteerd, kan de gebruiker rechtstreeks gegevens in verificator typen. Op dit moment zijn teken reeksen de enige invoer die wordt ondersteund voor zelf Attestation-claims. 

![gedetailleerde weer gave van de verifieer bare referentie kaart](media/credential-design/issuance-doc.png) 

**Statische claims:** Daarnaast kunnen we een statische claim declareren in het regel bestand, maar deze invoer is niet afkomstig van de gebruiker. De uitgever definieert een statische claim in het regel bestand en lijkt op elke andere claim in de verifieer bare referentie. U hoeft alleen maar een credentialSubject toe te voegen na VC. Typ en Declareer het kenmerk en de claim. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Invoer type: ID-token

Om ID-token als invoer op te halen, moet het regel bestand het bekende eind punt van het OIDC-compatibele identiteits systeem configureren. In dat systeem moet u een toepassing registreren met de juiste gegevens van de [communicatie voorbeelden van de uitgevers service](issuer-openid.md). Daarnaast moet de client_id in het regel bestand worden geplaatst, en moet er een bereik parameter worden ingevuld met de juiste bereiken. Azure Active Directory moet bijvoorbeeld het e-mail bereik hebben als u een e-mail claim wilt retour neren in het ID-token.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Eigenschap | Beschrijving |
| -------- | ----------- |
| `attestations.idTokens` | Een matrix van OpenID Connect Connect-id-providers die worden ondersteund voor het sourcing van gebruikers gegevens. |
| `...mapping` | Een object dat beschrijft hoe claims in elk ID-token worden toegewezen aan kenmerken in de resulterende verifieer bare referentie. |
| `...mapping.{attribute-name}` | Het kenmerk dat moet worden ingevuld in de resulterende verifieer bare referentie. |
| `...mapping.{attribute-name}.claim` | De claim in ID-tokens waarvan de waarde moet worden gebruikt om het kenmerk in te vullen. |
| `...configuration` | De locatie van het configuratie document van uw ID-provider. Deze URL moet voldoen aan de [OpenID Connect Connect Standard voor de meta gegevens van de identiteits provider](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Het configuratie document moet de `issuer` velden, `authorization_endpoint` , `token_endpoint` en bevatten `jwks_uri` . |
| `...client_id` | De client-ID die is verkregen tijdens het registratie proces van de client. |
| `...scope` | Een door spaties gescheiden lijst met bereiken die de IDP moet kunnen retour neren van de juiste claims in het ID-token. |
| `...redirect_uri` | Moet altijd de waarde gebruiken `vcclient://openid/` . |
| `validityInterval` | Een tijds duur, in seconden, die de levens duur van uw verifieer bare referenties aangeeft. Na deze tijds periode is de Controleer bare referentie niet langer geldig. Als deze waarde wordt wegge laten, blijft de verifieer bare referentie geldig totdat deze expliciet wordt ingetrokken. |
| `vc.type` | Een matrix met teken reeksen die de schema ('s) aangeven waaraan uw Controleer bare referentie voldoet. Zie de sectie hieronder voor meer details. |

### <a name="vctype-choose-credential-types"></a>VC. type: Selecteer referentie type (n) 

Alle verifieer bare referenties moeten hun type in hun regel bestand declareren. Het type referentie onderscheidt uw verifieer bare referenties van referenties die zijn uitgegeven door andere organisaties en zorgt voor de interoperabiliteit tussen emittenten en controle Programma's. Als u een referentie type wilt opgeven, moet u een of meer referentie typen opgeven waaraan de referentie voldoet. Elk type wordt vertegenwoordigd door een unieke teken reeks. er wordt vaak een URI gebruikt om wereld wijd uniekheid te garanderen. De URI hoeft niet adresseerbaar te zijn. het wordt beschouwd als een teken reeks. 

Zo kan een diploma referentie die is uitgegeven door Contoso University de volgende typen declareren:

| Type | Doel |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Verklaart dat diploma's die zijn uitgegeven door Contoso University, kenmerken bevatten die worden gedefinieerd door het object van het schema. org `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Verklaart dat de door Contoso University verleende diploma's kenmerken bevatten die zijn gedefinieerd door de Verenigde Staten Department of Education. |
| `https://schemas.contoso.edu/diploma2020` | Verklaart dat diploma's die zijn uitgegeven door Contoso University, kenmerken bevatten die door Contoso University zijn gedefinieerd. |

Door alle drie de typen te declareren, kunnen de diploma's van de Contoso University worden gebruikt om te voldoen aan verschillende aanvragen van de controle. Een bank kan een set van `EducationCredential` s aanvragen bij een gebruiker en het diploma kan worden gebruikt om aan de aanvraag te voldoen. Maar de Alumni Association van Contoso University kan een referentie van het type aanvragen `https://schemas.contoso.edu/diploma2020` en het diploma wordt ook aan de aanvraag voldaan.

Om de interoperabiliteit van uw referenties te waarborgen, is het raadzaam nauw samen te werken met gerelateerde organisaties om referentie typen, schema's en Uri's te definiëren die in uw branche kunnen worden gebruikt. Veel industrie organisaties bieden richt lijnen voor de structuur van officiële documenten die kunnen worden beoogd voor het definiëren van de inhoud van verifieer bare referenties. U moet ook nauw keurig samen werken met de verificaties van uw referenties om inzicht te krijgen in de manier waarop ze uw verifieer bare referenties willen aanvragen en gebruiken.

## <a name="input-type-verifiable-credential"></a>Invoer type: verifieer bare referentie

>[!NOTE]
>Regel bestanden die naar een verifieer bare referentie vragen, maken geen gebruik van de indeling van de presentatie-uitwisseling voor het aanvragen van referenties. Dit wordt bijgewerkt wanneer de verlenende service ondersteuning biedt voor het standaard referentie manifest. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Eigenschap | Beschrijving |
| -------- | ----------- |
| `attestations.presentations` | Een matrix met Controleer bare referenties die worden aangevraagd als invoer. |
| `...mapping` | Een object dat beschrijft hoe claims in elke door gegeven verifieer bare referentie worden toegewezen aan kenmerken in de resulterende verifieer bare referentie. |
| `...mapping.{attribute-name}` | Het kenmerk dat moet worden ingevuld in de resulterende verifieer bare referentie. |
| `...mapping.{attribute-name}.claim` | De claim in de verifieer bare referentie waarvan de waarde moet worden gebruikt om het kenmerk in te vullen. |
| `...mapping.{attribute-name}.indexed` | Er kan slechts één per Controleer bare referentie worden ingeschakeld om op te slaan voor intrekken. Raadpleeg het [artikel over het intrekken van een referentie](how-to-issuer-revoke.md) voor meer informatie. |
| `credentialType` | De credentialType van de verifieer bare referentie die u de gebruiker vraagt te presen teren. |
| `contracts` | De URI van het contract in de verifieer bare referentie Service Portal. |
| `issuers.iss` | De uitgever heeft de verifieer bare referentie voor de gebruiker gevraagd. |
| `validityInterval` | Een tijds duur, in seconden, die de levens duur van uw verifieer bare referenties aangeeft. Na deze tijds periode is de Controleer bare referentie niet langer geldig. Als deze waarde wordt wegge laten, blijft de verifieer bare referentie geldig totdat deze expliciet wordt ingetrokken. |
| `vc.type` | Een matrix met teken reeksen die de schema ('s) aangeven waaraan uw Controleer bare referentie voldoet. |


## <a name="input-type-self-attested-claims"></a>Invoer type: zelf Attestation-claims

Tijdens de uitgifte stroom kan de gebruiker worden gevraagd om een eigen attest informatie in te voeren. Vanaf nu is het enige invoer type een ' teken reeks '. 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Eigenschap | Beschrijving |
| -------- | ----------- |
| `attestations.selfIssued` | Een matrix van zelf-uitgegeven claims waarvoor invoer van de gebruiker is vereist. |
| `...mapping` | Een object dat beschrijft hoe zelf-uitgegeven claims worden toegewezen aan kenmerken in de resulterende verifieer bare referentie. |
| `...mapping.alias` | Het kenmerk dat moet worden ingevuld in de resulterende verifieer bare referentie. |
| `...mapping.alias.claim` | De claim in de verifieer bare referentie waarvan de waarde moet worden gebruikt om het kenmerk in te vullen. |
| `validityInterval` | Een tijds duur, in seconden, die de levens duur van uw verifieer bare referenties aangeeft. Na deze tijds periode is de Controleer bare referentie niet langer geldig. Als deze waarde wordt wegge laten, blijft de verifieer bare referentie geldig totdat deze expliciet wordt ingetrokken. |
| `vc.type` | Een matrix met teken reeksen die de schema ('s) aangeven waaraan uw Controleer bare referentie voldoet. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Weergave bestand: Controleer bare referenties in Microsoft Authenticator

Controleer bare referenties bieden een beperkt aantal opties die kunnen worden gebruikt om uw merk weer te geven. In dit artikel vindt u instructies voor het aanpassen van uw referenties en aanbevolen procedures voor het ontwerpen van referenties die geweldig worden weer gegeven aan gebruikers.

Controleer bare referenties die zijn uitgegeven aan gebruikers worden weer gegeven als kaarten in Microsoft Authenticator. Als beheerder kunt u de kleur, het pictogram en de teken reeksen van de kaart kiezen die overeenkomen met het merk van uw organisatie.

![uitgifte documentatie](media/credential-design/detailed-view.png) 

Kaarten bevatten ook aanpas bare velden die u kunt gebruiken om gebruikers te laten weten wat het doel is van de kaart, de kenmerken die het bevat en nog veel meer.

## <a name="create-a-credential-display-file"></a>Een weergave bestand voor referentie maken

Net als het regel bestand is het weergave bestand een eenvoudig JSON-bestand dat beschrijft hoe de inhoud van uw verifieer bare referenties in de Microsoft Authenticator-app moet worden weer gegeven. 

>[!NOTE]
> Op dit moment wordt dit weergave model alleen gebruikt door Microsoft Authenticator.

Het weergave bestand heeft de volgende structuur.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Eigenschap | Beschrijving |
| -------- | ----------- |
| `locale` | De taal van de verifieer bare referentie. Gereserveerd voor toekomstig gebruik. | 
| `card.title` | Hiermee wordt het type referentie voor de gebruiker weer gegeven. Aanbevolen maximum lengte van 25 tekens. | 
| `card.issuedBy` | Hier wordt de naam van de verlenende organisatie voor de gebruiker weer gegeven. Aanbevolen maximum lengte van 40 tekens. |
| `card.backgroundColor` | Bepaalt de achtergrond kleur van de kaart, in hexadecimale notatie. Een subtiele kleur overgang wordt toegepast op alle kaarten. |
| `card.textColor` | Bepaalt de tekst kleur van de kaart, in hexadecimale notatie. Wordt aanbevolen om zwart of wit te gebruiken. |
| `card.logo` | Een logo dat wordt weer gegeven op de kaart. De verschafte URL moet openbaar adresseerbaar zijn. Aanbevolen maximum hoogte van 36 PX en maximale breedte van 100 px, ongeacht de grootte van de telefoon. Het aanbevelen van PNG met transparante achtergrond. | 
| `card.description` | Aanvullende tekst die naast elke kaart wordt weer gegeven. Kan voor elk doel einde worden gebruikt. Aanbevolen maximum lengte van 100 tekens. |
| `consent.title` | Aanvullende tekst die wordt weer gegeven wanneer een kaart wordt uitgegeven. Wordt gebruikt om details over het uitgifte proces op te geven. Aanbevolen lengte van 100 tekens. |
| `consent.instructions` | Aanvullende tekst die wordt weer gegeven wanneer een kaart wordt uitgegeven. Wordt gebruikt om details over het uitgifte proces op te geven. Aanbevolen lengte van 100 tekens. |
| `claims` | Hiermee kunt u labels opgeven voor kenmerken die zijn opgenomen in elke referentie. |
| `claims.{attribute}` | Hiermee wordt het kenmerk van de referentie aangegeven waarop het label van toepassing is. |
| `claims.{attribute}.type` | Hiermee wordt het kenmerk type aangegeven. Momenteel wordt ' String ' alleen ondersteund. |
| `claims.{attribute}.label` | De waarde die moet worden gebruikt als label voor het kenmerk, die wordt weer gegeven in verificator. Dit kan afwijken van het label dat in het regel bestand is gebruikt. Aanbevolen maximum lengte van 40 tekens. |

>[!NOTE]
  >Als een claim is opgenomen in het regel bestand en vervolgens wordt wegge laten in het weergave bestand, zijn er twee verschillende soorten ervaring. Op iOS wordt de claim niet weer gegeven in de sectie Details die in de bovenstaande afbeelding wordt weer gegeven, terwijl op Android de claim wordt weer gegeven.  

## <a name="next-steps"></a>Volgende stappen

U hebt nu een beter inzicht in het ontwerp van de referentie en hoe u uw eigen referenties kunt maken om aan uw behoeften te voldoen.

- [Communicatie voorbeelden van de uitgevers service](issuer-openid.md)
