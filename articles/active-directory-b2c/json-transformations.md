---
title: Voor beelden van JSON-claim transformatie voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Voor beelden van JSON-claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5c8e21f2ce3f6907547bf1b2fe4681eb937864b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119872"
---
# <a name="json-claims-transformations"></a>JSON-claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden voor het gebruik van de JSON-claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="generatejson"></a>GenerateJson

Gebruik claim waarden of constanten voor het genereren van een JSON-teken reeks. De padtekenreeks volgende punt notatie wordt gebruikt om aan te geven waar de gegevens moeten worden ingevoegd in een JSON-teken reeks. Na splitsing per punt worden alle gehele getallen geïnterpreteerd als de index van een JSON-matrix en worden niet-gehele getallen geïnterpreteerd als de index van een JSON-object.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Wille keurige teken reeks na de punt notatie | tekenreeks | De JsonPath van de JSON waar de claim waarde wordt ingevoegd. |
| Parameter | Wille keurige teken reeks na de punt notatie | tekenreeks | De JsonPath van de JSON waar de constante teken reeks waarde wordt ingevoegd. |
| Output claim | Output claim | tekenreeks | De gegenereerde JSON-teken reeks. |

### <a name="example-1"></a>Voorbeeld 1

In het volgende voor beeld wordt een JSON-teken reeks gegenereerd op basis van de claim waarde "email" en "otp" en ook constante teken reeksen.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

De volgende claim transformatie voert een JSON-teken reeks claim uit die de hoofd tekst van de aanvraag is die wordt verzonden naar SendGrid (een e-mail provider van derden). De structuur van het JSON-object wordt gedefinieerd door de Id's in punt notatie van de invoer parameters en de TransformationClaimTypes van de InputClaims. Getallen in de punt notatie impliceren matrices. De waarden zijn afkomstig van de waarden van de InputClaims en de waarde van de eigenschap input parameters.

- Invoer claims:
  - **e-mail**, persoonlijke instellingen van het transformatie claim type **. 0. tot. 0. e-mail adres**: someone@example.com
  - **otp**, transformatie claim type **Personalizations.0.dynamic_template_data. otp** "346349"
- Invoer parameter:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **van. e-mail adres**: service@contoso.com
  - **persoonlijke instellingen. 0. onderwerp** "e-mail verificatie code contoso-account"
- Uitvoer claim:
  - **requestBody**: JSON-waarde

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>Voorbeeld 2

In het volgende voor beeld wordt een JSON-teken reeks gegenereerd op basis van de claim waarden en constante teken reeksen.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

De volgende claim transformatie voert een JSON-teken reeks claim uit die de hoofd tekst van de aanvraag is die naar een REST API wordt verzonden. De structuur van het JSON-object wordt gedefinieerd door de Id's in punt notatie van de invoer parameters en de TransformationClaimTypes van de InputClaims. De waarden zijn afkomstig van de waarden van de InputClaims en de waarde van de eigenschap input parameters.

- Invoer claims:
  - **e-mail**, transformatie claim type **customerEntity. email**: john.s@contoso.com
  - **objectId**, transformatie claim type **customerEntity. userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **opgegeven** naam, transformatie claim type **customerEntity. FirstName** "John"
  - **Achternaam**, transformatie claim type **customerEntity. lastName** "Smith"
- Invoer parameter:
  - **customerEntity.Role.name**: beheerder
  - **customerEntity.Role.id** 1
- Uitvoer claim:
  - **requestBody**: JSON-waarde

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Een opgegeven element ophalen uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputJson | tekenreeks | De ClaimTypes die worden gebruikt door de claim transformatie om het item op te halen. |
| Parameter | claimToExtract | tekenreeks | de naam van het JSON-element dat moet worden geëxtraheerd. |
| Output claim | extractedClaim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen, is de element waarde die is opgegeven in de invoer parameter _claimToExtract_ . |

In het volgende voor beeld heeft de claim transformatie het `emailAddress` element opgehaald uit de JSON-gegevens: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputJson**: {"emailAddress": " someone@example.com ", "DisplayName": "iemand"}
- Invoer parameter:
    - **claimToExtract**: emailAddress
- Uitvoer claims:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Een lijst met opgegeven elementen uit de JSON-gegevens ophalen.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | jsonSourceClaim | tekenreeks | De ClaimTypes die worden gebruikt door de claim transformatie om de claims op te halen. |
| Parameter | errorOnMissingClaims | booleaans | Hiermee geeft u op of er een fout moet worden gegenereerd als een van de claims ontbreekt. |
| Parameter | includeEmptyClaims | tekenreeks | Geef op of lege claims moeten worden toegevoegd. |
| Parameter | jsonSourceKeyName | tekenreeks | Sleutel naam van element |
| Parameter | jsonSourceValueName | tekenreeks | Naam van element waarde |
| Output claim | Verzameling | teken reeks, int, Boolean en datum/tijd |Lijst met te extra heren claims. De naam van de claim moet gelijk zijn aan die in _jsonSourceClaim_ -invoer claim. |

In het volgende voor beeld worden met de claim transformatie de volgende claims geëxtraheerd: e-mail (teken reeks), displayName (String), membershipNum (int), Active (Boolean) en geboorte datum (datetime) van de JSON-gegevens.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoer claims:
  - **jsonSourceClaim**: [{"Key": "e-mail", "waarde": " someone@example.com "}, {"sleutel": "DisplayName", "waarde": "persoon"}, {"sleutel": "membershipNum", "waarde": 6353399}, {"sleutel", "actief", "waarde": True}, {"Key": "geboorte datum", "waarde": "1980-09-23T00:00:00Z"}]
- Invoer parameters:
    - **errorOnMissingClaims**: False
    - **includeEmptyClaims**: False
    - **jsonSourceKeyName**: sleutel
    - **jsonSourceValueName**: waarde
- Uitvoer claims:
  - **e-mail**: someone@example.com
  - **DisplayName**: ' iemand '
  - **membershipNum**: 6353399
  - **actief**: waar
  - **geboorte datum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Hiermee wordt een opgegeven numeriek (lang)-element opgehaald uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputJson | tekenreeks | De ClaimTypes die door de claim transformatie worden gebruikt om de claim op te halen. |
| Parameter | claimToExtract | tekenreeks | De naam van het JSON-element dat moet worden uitgepakt. |
| Output claim | extractedClaim | long | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen, is de waarde van het element opgegeven in de _claimToExtract_ -invoer parameters. |

In het volgende voor beeld haalt de claim transformatie het `id` element uit de JSON-gegevens.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputJson**: {"emailAddress": " someone@example.com ", "DisplayName": "iemand", "id": 6353399}
- Invoer parameters
    - **claimToExtract**: id
- Uitvoer claims:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Hiermee wordt het eerste element opgehaald uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputJson | tekenreeks | De ClaimTypes die door de claim transformatie worden gebruikt voor het ophalen van het item uit de JSON-gegevens. |
| Output claim | sleutel | tekenreeks | De eerste element sleutel in de JSON. |
| Output claim | waarde | tekenreeks | De eerste element waarde in de JSON. |

In het volgende voor beeld haalt de claim transformatie het eerste element (de opgegeven naam) uit de JSON-gegevens.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputJson**: {"opgegeven naam": "Emilty", "LastName": "Smith"}
- Uitvoer claims:
  - **sleutel**: OpgegevenNaam
  - **waarde**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Hiermee wordt het eerste element opgehaald uit een JSON-gegevens matrix.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputJsonClaim | tekenreeks | De ClaimTypes die door de claim transformatie worden gebruikt om het item uit de JSON-matrix op te halen. |
| Output claim | extractedClaim | tekenreeks | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen, het eerste element in de JSON-matrix. |

In het volgende voor beeld haalt de claim transformatie het eerste element (e-mail adres) uit de JSON-matrix  `["someone@example.com", "Someone", 6353399]` .

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputJsonClaim**: [" someone@example.com ", "iemand", 6353399]
- Uitvoer claims:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML-gegevens worden geconverteerd naar de JSON-indeling.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | xml | tekenreeks | De ClaimTypes die worden gebruikt door de claim transformatie voor het converteren van de gegevens van XML naar de JSON-indeling. |
| Output claim | json | tekenreeks | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen, is de gegevens in JSON-indeling. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

In het volgende voor beeld worden met de claim transformatie de volgende XML-gegevens geconverteerd naar de JSON-indeling.

#### <a name="example"></a>Voorbeeld
Invoer claim:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Uitvoer claim:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


