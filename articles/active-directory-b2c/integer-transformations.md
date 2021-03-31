---
title: Voor beelden van het transformeren van een geheel getal voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Voor beelden van een geheel getal voor trans formatie van het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 066a6489e6244369453ec5d9f21d5e1e83fcd6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85201747"
---
# <a name="integer-claims-transformations"></a>Geheeltallige claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van trans formaties met een geheel getal voor het Framework van identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Converteert een lang gegevens type naar een teken reeks gegevens type.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | long | Het claim type dat moet worden geconverteerd naar een teken reeks. |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

In dit voor beeld `numericUserId` wordt de claim met het waardetype Long omgezet in een `UserId` claim met het waardetype string.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: 12334 (lang)
- Uitvoer claims:
    - **output claim**: "12334" (teken reeks)

