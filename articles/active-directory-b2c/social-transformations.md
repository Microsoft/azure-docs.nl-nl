---
title: Voor beelden van claim transformatie voor een sociaal account voor aangepast beleid
titleSuffix: Azure AD B2C
description: Voor beelden van claim transformatie voor een sociaal account voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953114"
---
# <a name="social-accounts-claims-transformations"></a>Sociale accounts claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) worden identiteiten van sociale accounts opgeslagen in een `userIdentities` kenmerk van een claim type **alternativeSecurityIdCollection** . Elk item in de **alternativeSecurityIdCollection** geeft de verlener (naam van de identiteits provider, zoals Facebook.com) en de `issuerUserId` , aan. Dit is een unieke gebruikers-id voor de verlener.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

In dit artikel vindt u voor beelden van het gebruik van de claim transformaties van het account voor de identiteits ervaring in Azure AD B2C. Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Hiermee maakt u een JSON-weer gave van de eigenschap alternativeSecurityId van de gebruiker die kan worden gebruikt in de aanroepen van Azure Active Directory. Zie het [AlternativeSecurityId](/graph/api/resources/alternativesecurityid) -schema voor meer informatie.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | sleutel | tekenreeks | Het claim type dat de unieke gebruikers-id specificeert die wordt gebruikt door de ID-provider voor sociale netwerken. |
| Input claim | Identity provider | tekenreeks | Het claim type dat de naam van de ID-provider van de sociale account opgeeft, zoals facebook.com. |
| Output claim | alternativeSecurityId | tekenreeks | Het claim type dat is geproduceerd nadat de ClaimsTransformation is aangeroepen. Bevat informatie over de identiteit van een gebruiker van een sociaal account. De **Uitgever** is de waarde van de `identityProvider` claim. De **issuerUserId** is de waarde van de `key` claim in Base64-indeling. |

Gebruik deze claim transformatie om een claim type te genereren `alternativeSecurityId` . Dit wordt gebruikt door alle technische profielen van de sociale ID-provider, zoals `Facebook-OAUTH` . De volgende claims transformatie ontvangt de gebruikers-ID en de naam van de ID-provider. De uitvoer van dit technische profiel is een JSON-teken reeks indeling die kan worden gebruikt in azure AD Directory Services.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **sleutel**: 12334
    - **Identity provider**: Facebook.com
- Uitvoer claims:
    - **alternativeSecurityId**: {"verlener": "Facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Hiermee voegt `AlternativeSecurityId` u een aan een `alternativeSecurityIdCollection` claim toe.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | item | tekenreeks | Het claim type dat aan de uitvoer claim moet worden toegevoegd. |
| Input claim | verzameling | alternativeSecurityIdCollection | De ClaimTypes die worden gebruikt door de claim transformatie, indien beschikbaar in het beleid. Als deze wordt vermeld, wordt aan het `item` einde van de verzameling de claim transformatie toegevoegd. |
| Output claim | verzameling | alternativeSecurityIdCollection | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. De nieuwe verzameling die zowel de items van invoer `collection` als bevat `item` . |

In het volgende voor beeld wordt een nieuwe sociale identiteit gekoppeld aan een bestaand account. Een nieuwe sociale identiteit koppelen:
1. Voer in de technische profielen **Aad-UserReadUsingAlternativeSecurityId** en **Aad-UserReadUsingObjectId** de **alternativeSecurityIds** -claim van de gebruiker uit.
1. Vraag de gebruiker zich aan te melden met een van de id-providers die niet aan deze gebruiker zijn gekoppeld.
1. Maak met behulp van de **CreateAlternativeSecurityId** -claim transformatie een nieuw **alternativeSecurityId** -claim type met de naam `AlternativeSecurityId2`
1. Roep de **AddItemToAlternativeSecurityIdCollection** -claim transformatie aan om de **AlternativeSecurityId2** -claim toe te voegen aan de bestaande **AlternativeSecurityIds** -claim.
1. De **alternativeSecurityIds** claim naar het gebruikers account behouden

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **item**: {"verlener": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **verzameling**: [{"verlener": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Uitvoer claims:
    - **verzameling**: [{"verlener": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Retourneert een lijst met verleners van de claim **alternativeSecurityIdCollection** naar een nieuwe **stringCollection** -claim.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Het claim type dat moet worden gebruikt om de lijst met id-providers (verlener) op te halen. |
| Output claim | identityProvidersCollection | stringCollection | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. Lijst met id-providers die zijn gekoppeld aan de invoer claim alternativeSecurityIdCollection |

De volgende claim transformatie leest de claim van de gebruiker **alternativeSecurityIds** en extraheert de lijst met namen van id-providers die aan dat account zijn gekoppeld. Gebruik uitvoer **identityProvidersCollection** om de lijst met id-providers weer te geven die aan het account zijn gekoppeld. Of filter op de pagina identiteits provider selecteren de lijst met id-providers op basis van de **identityProvidersCollection** claim van de uitvoer. De gebruiker kan dus kiezen om een nieuwe sociale identiteit te koppelen die nog niet aan het account is gekoppeld.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoer claims:
    - **alternativeSecurityIdCollection**: [{"verlener": "Google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Uitvoer claims:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Hiermee verwijdert u een **AlternativeSecurityId** van een **alternativeSecurityIdCollection** -claim.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Identity provider | tekenreeks | Het claim type dat de naam van de identiteits provider bevat die uit de verzameling moet worden verwijderd. |
| Input claim | verzameling | alternativeSecurityIdCollection | De ClaimTypes die worden gebruikt door de claim transformatie. De claim transformatie verwijdert de Identity provider uit de verzameling. |
| Output claim | verzameling | alternativeSecurityIdCollection | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. De nieuwe verzameling, nadat de Identity provider is verwijderd uit de verzameling. |

In het volgende voor beeld wordt een van de sociale identiteiten ontkoppeld met een bestaand account. Een sociale identiteit ontkoppelen:
1. Voer in de technische profielen **Aad-UserReadUsingAlternativeSecurityId** en **Aad-UserReadUsingObjectId** de **alternativeSecurityIds** -claim van de gebruiker uit.
2. Vraag de gebruiker welk sociaal account moet worden verwijderd uit de lijst met id-providers die aan deze gebruiker zijn gekoppeld.
3. Een technisch profiel voor het transformeren van claims aanroepen dat de **RemoveAlternativeSecurityIdByIdentityProvider** -claim transformatie aanroept, waarbij de geselecteerde sociale identiteit is verwijderd met behulp van de naam van de identiteits provider.
4. Behoud de **alternativeSecurityIds** -claim naar het gebruikers account.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **Identity provider**: Facebook.com
    - **verzameling**: [{"verlener": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Uitvoer claims:
    - **verzameling**: [{"verlener": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]