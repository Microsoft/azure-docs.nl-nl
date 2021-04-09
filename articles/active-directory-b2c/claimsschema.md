---
title: ClaimsSchema-Azure Active Directory B2C | Microsoft Docs
description: Geef het ClaimsSchema-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654027"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **ClaimsSchema** definieert de claimtypen waarnaar kan worden verwezen als onderdeel van het beleid. Het claimsschema is de plaats waar u uw claims declareert. Een claim kan de voor naam, achternaam, weergave naam, het telefoon nummer en meer zijn. Het element ClaimsSchema bevat een lijst met **claim** type-elementen. Het element **claim** type bevat het kenmerk **id** , dat de claim naam is.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>Claim type

Het element **claim** type bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een id die wordt gebruikt voor het claim type. Andere elementen kunnen deze id in het beleid gebruiken. |

Het element **claim** type bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | De titel die wordt weer gegeven voor gebruikers op verschillende schermen. De waarde kan worden [gelokaliseerd](localization.md). |
| DataType | 1:1 | Het type claim. |
| DefaultPartnerClaimTypes | 0:1 | De standaard claim typen voor de partner die moeten worden gebruikt voor een opgegeven protocol. De waarde kan worden overschreven in het **PartnerClaimType** dat is opgegeven in de **input claim** -of **output claim** -elementen. Gebruik dit element om de standaard naam voor een protocol op te geven.  |
| Maskeren | 0:1 | Een optionele teken reeks maskerings tekens die kunnen worden toegepast wanneer de claim wordt weer gegeven. Het telefoon nummer 324-232-4343 kan bijvoorbeeld worden gemaskeerd als XXX-XXX-4343. |
| UserHelpText | 0:1 | Een beschrijving van het claim type waarmee gebruikers het doel kunnen begrijpen. De waarde kan worden [gelokaliseerd](localization.md). |
| UserInputType | 0:1 | Het type invoer besturings element dat beschikbaar moet zijn voor de gebruiker bij het hand matig invoeren van de claim gegevens voor het claim type. Zie de gebruikers invoer typen die verderop op deze pagina zijn gedefinieerd. |
| AdminHelpText | 0:1 | Een beschrijving van het claim type die nuttig kan zijn voor beheerders om het doel ervan te begrijpen. |
| Beperking | 0:1 | De waarde-beperkingen voor deze claim, zoals een reguliere expressie (regex) of een lijst met acceptabele waarden. De waarde kan worden [gelokaliseerd](localization.md). |
PredicateValidationReference| 0:1 | Een verwijzing naar een **PredicateValidationsInput** -element. Met de **PredicateValidationReference** -elementen kunt u een validatie proces uitvoeren om ervoor te zorgen dat alleen goed gevormde gegevens worden ingevoerd. Zie [predikaten](predicates.md)voor meer informatie. |



### <a name="datatype"></a>DataType

Het **Data type** -element ondersteunt de volgende waarden:

| Type | Beschrijving |
| ------- | ----------- |
|booleaans|Vertegenwoordigt een Booleaanse `true` waarde (of `false` ).|
|date| Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum van een dag. De waarde van de datum volgt de ISO 8601-Conventie.|
|dateTime|Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag. De waarde van de datum volgt de ISO 8601-Conventie.|
|duur|Vertegenwoordigt een tijds interval in jaren, maanden, dagen, uren, minuten en seconden. De indeling van is `PnYnMnDTnHnMnS` , waarbij `P` positief of voor een `N` negatieve waarde wordt aangegeven. `nY` is het aantal jaren, gevolgd door een letterlijke waarde `Y` . `nMo` is het aantal maanden gevolgd door een letterlijke waarde `Mo` . `nD` is het aantal dagen, gevolgd door een letterlijke waarde `D` . Voor beelden: `P21Y` vertegenwoordigen 21 jaar. `P1Y2Mo` vertegenwoordigt één jaar en twee maanden. `P1Y2Mo5D` vertegenwoordigt één jaar, twee maanden en vijf dagen.  `P1Y2M5DT8H5M620S` vertegenwoordigt één jaar, twee maanden, vijf dagen, acht uur, vijf minuten en twintig seconden.  |
|phoneNumber|Vertegenwoordigt een telefoon nummer. |
|int| Vertegenwoordigt een getal tussen-2.147.483.648 en 2.147.483.647|
|long| Vertegenwoordigt een getal tussen-9.223.372.036.854.775.808 en 9.223.372.036.854.775.807 |
|tekenreeks| Geeft tekst als een reeks van UTF-16-code-eenheden.|
|stringCollection|Vertegenwoordigt een verzameling van `string` .|
|userIdentity| Vertegenwoordigt een gebruikers-id.|
|userIdentityCollection|Vertegenwoordigt een verzameling van `userIdentity` .|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

De **DefaultPartnerClaimTypes** kan het volgende element bevatten:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Protocol | 1: n | Lijst met protocollen met de standaard type naam van de partner claim. |

Het **protocol** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Yes | De naam van een geldig protocol dat door Azure AD B2C wordt ondersteund. Mogelijke waarden zijn: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Yes | De claim type naam die moet worden gebruikt. |

In het volgende voor beeld, wanneer het Framework voor identiteits ervaring communiceert met een SAML2-ID-provider of Relying Party toepassing, wordt de claim **Achternaam** toegewezen aan `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` , met OpenIdConnect en OAuth2, de claim wordt toegewezen aan `family_name` .

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Als gevolg hiervan levert het JWT-token dat is uitgegeven door Azure AD B2C, het `family_name` in plaats van de naam van claim type name.

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maskeren

Het **masker** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type van het claim masker. Mogelijke waarden: `Simple` of `Regex` . De `Simple` waarde geeft aan dat een eenvoudig tekst masker wordt toegepast op het voorste gedeelte van een teken reeks claim. De `Regex` waarde geeft aan dat een reguliere expressie wordt toegepast op de teken reeks claim als geheel.  Als de `Regex` waarde is opgegeven, moet er ook een optioneel kenmerk worden gedefinieerd met de reguliere expressie die moet worden gebruikt. |
| `Regex` | No | Als **`Type`** is ingesteld op `Regex` , geeft u de reguliere expressie op die moet worden gebruikt.

In het volgende voor beeld wordt een **phonenumber** -claim met het `Simple` masker geconfigureerd:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

In het Framework voor identiteits ervaring wordt het telefoon nummer weer gegeven en worden de eerste zes cijfers verborgen:

![De telefoon nummer claim die in de browser wordt weer gegeven met de eerste zes cijfers, gemaskeerd door XS](./media/claimsschema/mask.png)

In het volgende voor beeld wordt een **AlternateEmail** -claim met het `Regex` masker geconfigureerd:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

In het Framework voor identiteits ervaring worden alleen de eerste letter van het e-mail adres en de domein naam van het e-mail bericht weer gegeven:

![E-mail claim die in de browser wordt weer gegeven met tekens die worden gemaskeerd door sterretjes](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Beperking

Het **beperkings** element kan het volgende kenmerk bevatten:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| MergeBehavior | No | De methode die wordt gebruikt voor het samen voegen van opsommings waarden met een claim type in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in het basis beleid. Mogelijke waarden: `Append` , `Prepend` of `ReplaceAll` . De `Append` waarde is een verzameling gegevens die moet worden toegevoegd aan het einde van de verzameling die in het bovenliggende beleid is opgegeven. De `Prepend` waarde is een verzameling gegevens die moet worden toegevoegd vóór de verzameling die in het bovenliggende beleid is opgegeven. De `ReplaceAll` waarde is een verzameling gegevens die is opgegeven in het bovenliggende beleid en die moet worden genegeerd. |

Het **beperkings** element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Inventarisatie | 1: n | De beschik bare opties in de gebruikers interface waarmee de gebruiker een claim kan selecteren, zoals een waarde in een vervolg keuzelijst. |
| Patroon | 1:1 | De reguliere expressie die moet worden gebruikt. |

#### <a name="enumeration"></a>Inventarisatie

Het **opsommings** element definieert beschik bare opties die de gebruiker kan selecteren voor een claim in de gebruikers interface, zoals een waarde in een `CheckboxMultiSelect` , `DropdownSingleSelect` of `RadioSingleSelect` . U kunt ook beschik bare opties definiëren en lokaliseren met het element [LocalizedCollections](localization.md#localizedcollections) . Gebruik [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) -claim transformatie om een item op te zoeken in een claim **opsommings** verzameling.

Het **opsommings** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Tekst | Yes | De weergave teken reeks die wordt weer gegeven aan de gebruiker in de gebruikers interface voor deze optie. |
|Waarde | Yes | De claim waarde die is gekoppeld aan het selecteren van deze optie. |
| SelectByDefault | No | Hiermee wordt aangegeven of deze optie standaard moet worden geselecteerd in de gebruikers interface. Mogelijke waarden: True of false. |

In het volgende voor beeld wordt een lijst claim voor een **plaats** keuzelijst geconfigureerd met een standaard waarde ingesteld op `New York` :

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Lijst met vervolg steden met een standaard waarde ingesteld op New York:

![Besturings element voor vervolg keuzelijst wordt weer gegeven in de browser en de standaard waarde wordt weer gegeven](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Patroon

Het element **pattern** kan de volgende kenmerken bevatten:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| RegularExpression | Yes | De reguliere expressie die de claims van dit type moet overeenkomen om geldig te zijn. |
| HelpText | No | Een fout bericht voor gebruikers als de reguliere expressie is mislukt. |

In het volgende voor beeld wordt een **e-mail** claim geconfigureerd met de reguliere expressie invoer validatie en Help-tekst:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

In het Framework voor identiteits ervaring wordt de e-mailadres claim met invoer validatie voor de e-mail indeling weer gegeven:

![Tekstvak met fout bericht geactiveerd door regex-beperking](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C ondersteunt diverse invoer typen voor gebruikers, zoals een tekstvak, wacht woord en vervolg keuzelijst die kunnen worden gebruikt bij het hand matig invoeren van claim gegevens voor het claim type. U moet de **UserInputType** opgeven wanneer u gegevens van de gebruiker verzamelt met behulp van een [zelfondertekend technisch profiel](self-asserted-technical-profile.md) en [weer gave-besturings elementen](display-controls.md).

Het **UserInputType** -element beschik bare invoer typen voor gebruikers:

| UserInputType | Ondersteunde claim type | Description |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Vervolg keuzelijst meervoudige selectie. De claim waarde wordt weer gegeven in een komma als scheidings teken reeks van de geselecteerde waarden. |
|DateTimeDropdown | `date`, `dateTime` |Vervolg keuzelijst om een dag, maand en jaar te selecteren. |
|DropdownSingleSelect |`string` |Eén vervolg keuzelijst selecteren. De claim waarde is de geselecteerde waarde.|
|EmailBox | `string` |Invoer veld voor e-mail. |
|Alinea | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Een veld waarin alleen tekst in een alinea code wordt weer gegeven. |
|Wachtwoord | `string` |Tekstvak voor wacht woord.|
|RadioSingleSelect |`string` | Verzameling keuze rondjes. De claim waarde is de geselecteerde waarde.|
|Kenmerk | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Tekstvak met het kenmerk alleen-lezen. |
|Tekstvak |`boolean`, `int`, `string` |Tekstvak met één regel. |


#### <a name="textbox"></a>Tekstvak

Het invoer type **tekstvak** wordt gebruikt om een tekstvak met één regel te maken.

![Tekstvak met de eigenschappen die zijn opgegeven bij claim type](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Het invoer type voor de **EmailBox** -gebruiker wordt gebruikt om een eenvoudig invoer veld voor e-mail op te geven.

![EmailBox die de eigenschappen weer geven die zijn opgegeven bij claim type](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Wachtwoord

Het invoer type voor de **wachtwoord** gebruiker wordt gebruikt om een wacht woord op te nemen dat door de gebruiker is ingevoerd.

![Claim type gebruiken met wacht woord](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Het invoer type **DateTimeDropdown** -gebruiker wordt gebruikt om een set vervolg keuzelijsten op te geven om een dag, maand en jaar te selecteren. U kunt predikaten en PredicateValidations-elementen gebruiken om de minimum-en maximum datum waarden te bepalen. Zie de sectie **een datum bereik configureren** van [predikaten en PredicateValidations](predicates.md)voor meer informatie.

![Claim type gebruiken met datetimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Het invoer type van de **RadioSingleSelect** -gebruiker wordt gebruikt om een verzameling keuze rondjes te bieden waarmee de gebruiker één optie kan selecteren.

![Claim type gebruiken met radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Het invoer type **DropdownSingleSelect** -gebruiker wordt gebruikt om een vervolg keuzelijst te bieden waarmee de gebruiker één optie kan selecteren.

![Claim type gebruiken met dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Het invoer type **CheckboxMultiSelect** -gebruiker wordt gebruikt om een verzameling selectie vakjes op te geven waarmee de gebruiker meerdere opties kan selecteren.

![Claim type gebruiken met checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Kenmerk

Het invoer type **ReadOnly** wordt gebruikt om een veld met het kenmerk alleen-lezen te bieden om de claim en waarde weer te geven.

![Claim type gebruiken met alleen-lezen](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Alinea

Het invoer type **alinea** gebruiker wordt gebruikt om een veld op te geven waarin alleen tekst in een alinea code wordt weer gegeven.  Bijvoorbeeld, &lt; p &gt; tekst &lt; /p &gt; . Een **alinea** -invoer type `OutputClaim` van de gebruiker met een zelfbevestigend technisch profiel moet het `Required` kenmerk instellen `false` (standaard).

![Claim type gebruiken met alinea](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
