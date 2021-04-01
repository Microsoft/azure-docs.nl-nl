---
title: Een validatie technische profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Valideer claims met behulp van een validatie technische profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202990"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technische validatie profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een technische validatie profiel is een normaal technisch profiel van elk protocol, zoals [Azure Active Directory](active-directory-technical-profile.md) of een [rest API](restful-technical-profile.md). Het technische profiel voor validatie retourneert uitvoer claims of retourneert 4xx HTTP-status code, met de volgende gegevens. Zie voor meer informatie [retour neren fout bericht](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Het bereik van de uitvoer claims van een validatie technische profiel is beperkt tot het [zelfondertekende technische profiel](self-asserted-technical-profile.md) dat het validatie technische profiel aanroept, en de bijbehorende validatie technische profielen. Als u de uitvoer claims in de volgende Orchestration-stap wilt gebruiken, voegt u de uitvoer claims toe aan het zelfondertekende technische profiel dat het technische profiel voor validatie aanroept.

Validatie van technische profielen worden uitgevoerd in de volg orde waarin ze worden weer gegeven in het **ValidationTechnicalProfiles** -element. U kunt configureren in een technisch profiel voor validatie, ongeacht of de uitvoering van de volgende validatie technische profielen moet worden voortgezet als er een fout optreedt in het technische profiel van de validatie.

Een validatie technische profiel kan voorwaardelijk worden uitgevoerd op basis van de voor waarden die zijn gedefinieerd in het **ValidationTechnicalProfile** -element. U kunt bijvoorbeeld controleren of een specifieke claim bestaat of of een claim gelijk is aan of niet aan de opgegeven waarde.

Een zelf-bevestigd technisch profiel kan een technisch profiel voor validatie definiëren dat moet worden gebruikt voor het valideren van sommige of alle bijbehorende uitvoer claims. Alle invoer claims van het technische profiel waarnaar wordt verwezen, moeten worden weer gegeven in de uitvoer claims van het referentie-technische profiel voor validatie.

> [!NOTE]
> Alleen zelf-beweringen technische profielen kunnen validatie technische profielen gebruiken. Als u de uitvoer claims van niet-zelfondertekende technische profielen moet valideren, kunt u overwegen om een aanvullende indelings stap in uw gebruikers traject te gebruiken om het technische profiel dat verantwoordelijk is voor de validatie, toe te passen.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Het **ValidationTechnicalProfiles** -element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Een technisch profiel dat moet worden gebruikt voor het valideren van een aantal of alle uitvoer claims van het referentie-technische profiel. |

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een technisch profiel is al gedefinieerd in het beleid of het bovenliggende beleid. |
|ContinueOnError|No| Hiermee wordt aangegeven of de validatie van de volgende validatie technische profielen moet worden voortgezet als het technische profiel voor de validatie een fout veroorzaakt. Mogelijke waarden: `true` of `false` (de verwerking van verdere validatie profielen wordt gestopt en er wordt een fout geretourneerd). |
|ContinueOnSuccess | No | Hiermee wordt aangegeven of de validatie van de volgende validatie profielen moet worden voortgezet als het technische profiel voor de validatie is geslaagd. Mogelijke waarden: `true` of `false` . De standaard waarde is `true` , wat inhoudt dat de verwerking van verdere validatie profielen zal worden voortgezet. |

Het element **ValidationTechnicalProfile** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Voor waarden | 0:1 | Een lijst met voor waarden waaraan moet worden voldaan om het technische profiel voor validatie te kunnen uitvoeren. |

Het element **voor waarde** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type controle of query dat moet worden uitgevoerd voor de voor waarde. Ofwel `ClaimsExist` wordt opgegeven om ervoor te zorgen dat acties moeten worden uitgevoerd als de opgegeven claims bestaan in de huidige claimset van de gebruiker, of `ClaimEquals` dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. |
| `ExecuteActionsIf` | Yes | Hiermee wordt aangegeven of de acties in de voor waarde moeten worden uitgevoerd als de test True of False is. |

Het element **voor waarde** bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Waarde | 1: n | De gegevens die worden gebruikt door de controle. Als het type van deze controle is `ClaimsExist` , geeft dit veld een ClaimTypeReferenceId op die moet worden opgevraagd. Als het type controle is `ClaimEquals` , specificeert dit veld een ClaimTypeReferenceId om op te vragen. Een ander value-element bevat de waarde die moet worden gecontroleerd.|
| Bewerking | 1:1 | De actie die moet worden uitgevoerd als de voor waarde wordt gecontroleerd binnen een Orchestration-stap. De waarde van de **actie** wordt ingesteld op `SkipThisValidationTechnicalProfile` . Hiermee geeft u op dat het bijbehorende technische profiel voor validatie niet moet worden uitgevoerd. |

### <a name="example"></a>Voorbeeld

In het volgende voor beeld worden deze technische profielen voor validatie gebruikt:

1. Het eerste validatie profiel controleert de gebruikers referenties en gaat niet verder als er een fout optreedt, zoals een ongeldige gebruikers naam of een onjuist wacht woord.
2. Het volgende validatie technische profiel wordt niet uitgevoerd als de User type-claim niet bestaat of als de waarde van de User type is `Partner` . Het technische profiel voor validatie probeert het gebruikers profiel te lezen uit de interne klanten database en door te gaan als er een fout optreedt, zoals REST API service niet beschikbaar of een interne fout.
3. Het laatste validatie-technische profiel wordt niet uitgevoerd als de User type-claim niet bestaat of als de waarde van de User type is `Customer` . Het validatie technische profiel probeert het gebruikers profiel te lezen uit de interne partner database en gaat door als er een fout optreedt, zoals REST API service niet beschikbaar of een interne fout.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
