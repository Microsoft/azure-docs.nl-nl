---
title: Predikaten en PredicateValidations
titleSuffix: Azure AD B2C
description: Voor komen dat ongeldige gegevens worden toegevoegd aan uw Azure AD B2C-Tenant met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108413"
---
# <a name="predicates-and-predicatevalidations"></a>Predikaten en PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met de elementen **predikaten** en **PredicateValidations** kunt u een validatie proces uitvoeren om ervoor te zorgen dat alleen goed gevormde gegevens worden ingevoerd in uw Azure Active Directory B2C (Azure AD B2C)-Tenant.

In het volgende diagram ziet u de relatie tussen de elementen:

![Diagram van de relatie met de validatie van predikaten en predikaten](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicaten

Het **predicaat** element definieert een basis validatie om de waarde van een claim type te controleren en retourneert `true` of `false` . De validatie wordt uitgevoerd met behulp van een opgegeven **methode** -element en een set **parameter** elementen die relevant zijn voor de methode. Een predikaat kan bijvoorbeeld controleren of de lengte van een teken reeks claim waarde binnen het opgegeven minimum-en maximum aantal para meters ligt, of dat een teken reeks claim waarde een tekenset bevat. Het element **UserHelpText** biedt een fout bericht voor gebruikers als de controle mislukt. De waarde van het element **UserHelpText** kan worden gelokaliseerd met behulp van [taal aanpassing](localization.md).

Het element **predikaten** moet direct na het **ClaimsSchema** -element binnen het element [BuildingBlocks](buildingblocks.md) worden weer gegeven.

Het element **predikaten** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Predicaat | 1: n | Een lijst met predikaten. |

Het **predicaat** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een id die wordt gebruikt voor het predicaat. Andere elementen kunnen deze id in het beleid gebruiken. |
| Methode | Yes | Het type methode dat moet worden gebruikt voor validatie. Mogelijke waarden: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)of [IsDateRange](#isdaterange).  |
| HelpText | No | Een fout bericht voor gebruikers als de controle mislukt. Deze teken reeks kan worden gelokaliseerd met behulp van de [taal aanpassing](localization.md) |

Het **predicaat** -element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | Keur Een fout bericht voor gebruikers als de controle mislukt. |
| Parameters | 1:1 | De para meters voor het methode type van de teken reeks validatie. |

Het element **para meters** bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Parameter | 1: n | De para meters voor het methode type van de teken reeks validatie. |

Het **parameter** element bevat de volgende kenmerken:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Id | 1:1 | De id van de para meter. |

### <a name="predicate-methods"></a>Predikaten-methoden

#### <a name="islengthrange"></a>IsLengthRange

De methode IsLengthRange controleert of de lengte van een teken reeks claim waarde binnen het opgegeven minimum-en maximum aantal para meters ligt. Het predicaat-element ondersteunt de volgende para meters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Maximum | Yes | Het maximum aantal tekens dat kan worden ingevoerd. |
| Minimum | Yes | Het minimum aantal tekens dat moet worden ingevoerd. |


In het volgende voor beeld ziet u een methode IsLengthRange met de para meters `Minimum` en `Maximum` geeft u het lengte bereik van de teken reeks op:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

De methode MatchesRegex controleert of een teken reeks claim waarde overeenkomt met een reguliere expressie. Het predicaat-element ondersteunt de volgende para meters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| RegularExpression | Yes | Het reguliere expressie patroon dat moet worden vergeleken. |

In het volgende voor beeld ziet `MatchesRegex` u een methode met de para meter `RegularExpression` die een reguliere expressie specificeert:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

De methode IncludesCharacters controleert of een teken reeks claim waarde een tekenset bevat. Het predicaat-element ondersteunt de volgende para meters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| CharacterSet | Yes | De set tekens die kan worden ingevoerd. Bijvoorbeeld kleine letters  `a-z` , hoofd letters, `A-Z` cijfers, `0-9` of een lijst met symbolen, zoals `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

In het volgende voor beeld ziet `IncludesCharacters` u een methode met de para meter `CharacterSet` die de set tekens specificeert:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

De methode IsDateRange controleert of een datum claim waarde ligt tussen een bereik van de opgegeven minimum-en maximum parameters. Het predicaat-element ondersteunt de volgende para meters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Maximum | Yes | De grootste mogelijke datum die kan worden ingevoerd. De notatie van de datum volgt de `yyyy-mm-dd` Conventie of `Today` . |
| Minimum | Yes | De kleinste mogelijke datum die kan worden ingevoerd. De notatie van de datum volgt de `yyyy-mm-dd` Conventie of `Today` .|

In het volgende voor beeld ziet u een `IsDateRange` methode met de para meters `Minimum` en `Maximum` geeft u het datum bereik op met een notatie van `yyyy-mm-dd` en `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Hoewel de predikaten de validatie definiëren voor het controleren op basis van een claim type, wordt in de **PredicateValidations** groep een reeks predikaten gevormd om een gebruikers invoer validatie te vormen die kan worden toegepast op een claim type. Elk **PredicateValidation** -element bevat een set **PredicateGroup** -elementen die een set **PredicateReference** -elementen bevatten die naar een **predikaat** verwijzen. Om de validatie door te voeren, moet de waarde van de claim alle tests van een predikaat door geven onder alle **PredicateGroup** met hun set **PredicateReference** -elementen.

Het element **PredicateValidations** moet direct na het element **predikaten** worden weer gegeven binnen het [BuildingBlocks](buildingblocks.md) -element.

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Het element **PredicateValidations** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Een lijst met validatie van predikaten. |

Het element **PredicateValidation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een id die wordt gebruikt voor de validatie van predikaten. Het element **claim** type kan deze id in het beleid gebruiken. |

Het element **PredicateValidation** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Een lijst met predicaat groepen. |

Het element **PredicateGroups** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Een lijst met predikaten. |

Het element **PredicateGroup** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een id die wordt gebruikt voor de predicaat groep.  |

Het **PredicateGroup** -element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Een beschrijving van het predikaat waarmee gebruikers kunnen zien welke waarde ze moeten typen. |
| PredicateReferences | 1: n | Een lijst met predikaten-verwijzingen. |

Het **PredicateReferences** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| MatchAtLeast | No | Hiermee geeft u op dat de waarde moet overeenkomen met een groot aantal predicaat definities voor de invoer die moet worden geaccepteerd. Als niet wordt opgegeven, moet de waarde overeenkomen met alle predicaat definities. |

Het **PredicateReferences** -element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Een verwijzing naar een predikaat. |

Het **PredicateReference** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een id die wordt gebruikt voor de validatie van predikaten.  |


## <a name="configure-password-complexity"></a>Wachtwoord complexiteit configureren

Met **predikaten** en **PredicateValidationsInput** kunt u de complexiteits vereisten bepalen voor wacht woorden die door een gebruiker worden verschaft bij het maken van een account. Azure AD B2C maakt standaard gebruik van sterke wacht woorden. Azure AD B2C biedt ook ondersteuning voor configuratie opties voor het beheren van de complexiteit van wacht woorden die klanten kunnen gebruiken. U kunt wachtwoord complexiteit definiëren met behulp van deze predicaat elementen:

- Met **behulp van** de `IsLengthRange` -methode valideert u of het wacht woord tussen de 8 en 64 tekens moet lang zijn.
- Met de methode wordt in **kleine letters** `IncludesCharacters` gecontroleerd of het wacht woord een kleine letter bevat.
- **Hoofd letters** met de `IncludesCharacters` methode valideert of het wacht woord een hoofd letter bevat.
-  Als u de `IncludesCharacters` methode gebruikt, wordt gevalideerd of het wacht woord een cijfer bevat.
-  Met het symbool `IncludesCharacters` dat de methode gebruikt, wordt gevalideerd of het wacht woord een van de symbool tekens bevat.
- **Pincode** met behulp van de `MatchesRegex` methode valideert of het wacht woord alleen cijfers bevat.
-  Met behulp `MatchesRegex` van de methode valideert u of het wacht woord alleen een ongeldig teken heeft opgegeven.
- **DisallowedWhitespace** met behulp van de `MatchesRegex` -methode valideert of het wacht woord niet begint of eindigt met een spatie.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Nadat u de basis validaties hebt gedefinieerd, kunt u deze combi neren en een set wachtwoord beleidsregels maken die u in uw beleid kunt gebruiken:

- **SimplePassword** valideert de DisallowedWhitespace, AllowedAADCharacters en IsLengthBetween8And64
- **StrongPassword** valideert de DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. De laatste groep `CharacterClasses` voert een extra set predikaten uit met de `MatchAtLeast` waarde 3. Het wacht woord van de gebruiker moet tussen 8 en 16 tekens lang zijn en drie van de volgende tekens bevatten: kleine letters, hoofd letters, cijfer of symbool.
- **CustomPassword** valideert alleen DisallowedWhitespace, AllowedAADCharacters. De gebruiker kan dus elke wille keurige lengte opgeven, op voor waarde dat de tekens geldig zijn.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Voeg in uw claim type het element **PredicateValidationReference** toe en geef de id op als een van de validaties voor het predicaat, zoals SimplePassword, StrongPassword of CustomPassword.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Hieronder ziet u hoe de elementen worden georganiseerd wanneer Azure AD B2C het fout bericht weergeeft:

![Diagram van het voor beeld van een predikaat en PredicateGroup-wachtwoord complexiteit](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Een datum bereik configureren

Met de **predikaten** en **PredicateValidations** elementen kunt u de minimum-en maximum datum waarden van de **UserInputType** bepalen met behulp van een `DateTimeDropdown` . Hiervoor maakt u een **predikaat** met de- `IsDateRange` methode en geeft u de minimale en maximale para meters op.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Voeg een **PredicateValidation** toe met een verwijzing naar het `DateRange` predicaat.

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Voeg in uw claim type het element **PredicateValidationReference** toe en geef de id op als `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoord complexiteit met aangepaste beleids regels in azure Active Directory B2C](password-complexity.md) met behulp van validatie van predikaten.