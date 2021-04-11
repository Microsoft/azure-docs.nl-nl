---
title: Gebruikers toegang beheren in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het identificeren van minder jarigen, het verzamelen van de geboorte datum en land-en regio gegevens en het accepteren van gebruiks voorwaarden in uw toepassing met behulp van Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518050"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gebruikers toegang beheren in Azure Active Directory B2C

In dit artikel wordt beschreven hoe u de gebruikers toegang tot uw toepassingen beheert met behulp van Azure Active Directory B2C (Azure AD B2C). Toegangs beheer in uw toepassing omvat:

- Minder jarigen identificeren en de gebruikers toegang tot uw toepassing beheren.
- Ouderlijke toestemming voor minder jarigen vereisen om uw toepassingen te gebruiken.
- De geboorte-en land-of regio gegevens van gebruikers verzamelen.
- Vastleggen van de gebruiksrecht overeenkomst en beperking-toegang.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Beperkte toegang beheren

Toepassingen en organisaties kunnen ervoor kiezen om minder jarigen te blok keren voor het gebruik van toepassingen en services die niet zijn gericht op deze doel groep. Toepassingen en organisaties kunnen er ook voor kiezen om minder jarigen te accepteren en vervolgens de ouderlijke toestemming te beheren en de toegestane ervaringen te leveren voor minder jarigen, zoals bepaald door bedrijfs regels en volgens de regelgeving.

Als een gebruiker is geïdentificeerd als een kleine, kunt u de gebruikers stroom in Azure AD B2C instellen op een van de volgende drie opties:

- **Een ondertekende JWT-id_token terugsturen naar de toepassing**: de gebruiker is geregistreerd in de Directory en er wordt een token naar de toepassing geretourneerd. De toepassing gaat vervolgens verder met het Toep assen van bedrijfs regels. De toepassing kan bijvoorbeeld door gaan met een proces voor het uitvoeren van een ouder toestemming. Als u deze methode wilt gebruiken, moet u ervoor kiezen om de **ageGroup** -en **consentProvidedForMinor** -claims van de toepassing te ontvangen.

- **Een niet-ondertekend JSON-token naar de toepassing verzenden**: met Azure AD B2C wordt de toepassing gewaarschuwd dat de gebruiker een kleine is en de status van de ouderlijke toestemming van de gebruiker wordt verstrekt. De toepassing gaat vervolgens verder met het Toep assen van bedrijfs regels. Een JSON-token heeft geen geslaagde verificatie met de toepassing voltooid. De toepassing moet de niet-geverifieerde gebruiker verwerken volgens de claims die zijn opgenomen in het JSON-token, zoals **naam**, **e-mail adres**, **ageGroup** en **consentProvidedForMinor**.

- **De gebruiker blok keren**: als een gebruiker een kleine en een ouderlijke toestemming heeft gekregen, kan Azure AD B2C de gebruiker op de hoogte stellen dat ze zijn geblokkeerd. Er is geen token uitgegeven, de toegang wordt geblokkeerd en het gebruikers account is niet gemaakt tijdens een registratie traject. Als u deze melding wilt implementeren, geeft u een geschikte HTML/CSS-inhouds pagina op waarmee de gebruiker op de hoogte wordt gebracht en de relevante opties worden weer gegeven. Er is geen verdere actie vereist voor de toepassing voor nieuwe registraties.

## <a name="get-parental-consent"></a>Ouderlijke toestemming ophalen

Afhankelijk van de toepassings verordening moet de ouderlijke toestemming mogelijk worden verleend door een gebruiker die is geverifieerd als volwassene. Azure AD B2C biedt geen ervaring om de leeftijd van een persoon te controleren en vervolgens een geverifieerde volwassene toestemming te geven om ouders in te schrijven voor een kleine. Deze ervaring moet worden verschaft door de toepassing of een andere service provider.

Hier volgt een voor beeld van een gebruikers stroom voor het verzamelen van de ouderlijke toestemming:

1. Een [Microsoft Graph API](/graph/use-the-api) -bewerking identificeert de gebruiker als een kleine en retourneert de gebruikers gegevens naar de toepassing in de vorm van een niet-ondertekend JSON-token.

2. De toepassing verwerkt het JSON-token en toont een scherm voor het secundaire bericht, waarin wordt gemeld dat de ouderlijke toestemming is vereist en vraagt de toestemming van een bovenliggend item online.

3. Azure AD B2C toont een aanmeldings traject waarmee de gebruiker zich normaal kan aanmelden en een token kan uitgeven aan de toepassing die is ingesteld op include **legalAgeGroupClassification = "minorWithParentalConsent"**. De toepassing verzamelt het e-mail adres van het bovenliggende item en controleert of de bovenliggende site een volwassene is. Hiervoor wordt een vertrouwde bron gebruikt, zoals een nationaal ID-kantoor, een licentie verificatie of een creditcard bewijs. Als de verificatie is geslaagd, vraagt de toepassing de secundaire om zich aan te melden met behulp van de Azure AD B2C gebruikers stroom. Als toestemming is geweigerd (bijvoorbeeld als **legalAgeGroupClassification = "minorWithoutParentalConsent"**), retourneert Azure AD B2C een JSON-token (geen aanmelding) naar de toepassing om het toestemming proces opnieuw te starten. Het is optioneel om de gebruikers stroom aan te passen, zodat een kleine of een volwassene weer toegang kan krijgen tot een onderliggend account door een registratie code naar het e-mail adres van de secundaire of het e-mail adres van de volwassene op record te sturen.

4. De toepassing biedt een optie voor de secundaire om toestemming in te trekken.

5. Wanneer de secundaire of volwassene toestemming intrekt, kan de Microsoft Graph-API worden gebruikt om **consentProvidedForMinor** te wijzigen in **geweigerd**. De toepassing kan er ook voor kiezen om een secundaire te verwijderen waarvan de toestemming is ingetrokken. Het is optioneel om de gebruikers stroom aan te passen, zodat de geverifieerde secundaire (of bovenliggende site die gebruikmaakt van het account van de secundaire) toestemming kan intrekken. Azure AD B2C records **consentProvidedForMinor** als **geweigerd**.

Zie het [resource type](/graph/api/resources/user)van de gebruiker voor meer informatie over **legalAgeGroupClassification**, **consentProvidedForMinor** en **ageGroup**. Zie [aangepaste kenmerken gebruiken om informatie over uw consumenten te verzamelen](user-flow-custom-attributes.md)voor meer informatie over aangepaste kenmerken. Wanneer u uitgebreide kenmerken met behulp van de Microsoft Graph-API adresseert, moet u de lange versie van het kenmerk gebruiken, zoals *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Gegevens over geboorte datum en land/regio verzamelen

Toepassingen kunnen afhankelijk zijn van Azure AD B2C voor het verzamelen van de geboorte datum (DOB) en de land-/regiogegevens van alle gebruikers tijdens de registratie. Als deze informatie nog niet bestaat, kan de toepassing deze aanvragen bij de gebruiker tijdens het volgende verificatie proces (aanmelden). Gebruikers kunnen niet door gaan zonder hun DOB en land/regio gegevens op te geven. Azure AD B2C gebruikt de informatie om te bepalen of de persoon wordt beschouwd als een klein niveau volgens de normen van dat land/deze regio.

Een aangepaste gebruikers stroom kan informatie over DOB en land/regio verzamelen en Azure AD B2C claim transformatie gebruiken om de **ageGroup** te bepalen en het resultaat te behouden (of de informatie over de Dob en land/regio rechtstreeks te bewaren) in de map.

In de volgende stappen ziet u de logica die wordt gebruikt om **ageGroup** te berekenen op basis van de geboorte datum van de gebruiker:

1. Probeer het land/de regio te vinden op basis van de code in de lijst land/regio. Als het land/de regio niet wordt gevonden, keert u terug naar de **standaard waarde**.

2. Als het **MinorConsent** -knoop punt aanwezig is in het land/regio-element:

    a. Bereken de datum waarop de gebruiker moet zijn geboren om als volwassen te worden beschouwd. Als de huidige datum bijvoorbeeld 14 maart 2015 en **MinorConsent** is 18, mag de geboorte datum niet later zijn dan 14 maart 2000.

    b. De minimale geboorte datum vergelijken met de werkelijke geboorte datum. Als de minimale geboorte datum vóór de geboorte datum van de gebruiker valt, retourneert de berekening een **secundaire** waarde als de leeftijds groep.

3. Als het knoop punt **MinorNoConsentRequired** aanwezig is in het land/regio-element, herhaalt u stap 2a en 2b met de waarde van **MinorNoConsentRequired**. De uitvoer van 2b retourneert **MinorNoConsentRequired** als de minimale geboorte datum vóór de geboorte datum van de gebruiker ligt.

4. Als geen van beide berekeningen waar retourneert, wordt **volwassene** geretourneerd.

Als een toepassing op een betrouw bare manier DOB of land/regio-gegevens heeft verzameld, kan de toepassing de Graph API gebruiken om de gebruikers record bij te werken met deze gegevens. Bijvoorbeeld:

- Als een gebruiker is bekend als volwassene, werkt u het Directory kenmerk **ageGroup** bij met de waarde **volwassen**.
- Als een gebruiker een kleine waarde is, werkt u de Directory-kenmerk **ageGroup** met de waarden **Minor** en set **consentProvidedForMinor**, indien van toepassing.

## <a name="minor-calculation-rules"></a>Secundaire berekenings regels

De leeftijds beperking omvat twee leeftijds waarden: de leeftijd die niet langer wordt beschouwd als een kleine, en de leeftijd waarbij een kleine persoon toestemming moet geven over ouders. De volgende tabel bevat de leeftijds regels die worden gebruikt voor het definiëren van een kleine en kleine, vereiste toestemming.

| Land/regio | Naam van land/regio | Duur van de secundaire toestemming | Secundaire leeftijd |
| -------------- | ------------------- | ----------------- | --------- |
| Standaard | Geen | Geen | 18 |
| AE | Verenigde Arabische Emiraten | Geen | 21 |
| AT | Oostenrijk | 14 | 18 |
| BE | België | 14 | 18 |
| BG | Bulgarije | 16 | 18 |
| BH | Bahrein | Geen | 21 |
| CM | Kameroen | Geen | 21 |
| CY | Cyprus | 16 | 18 |
| CZ | Tsjechische Republiek | 16 | 18 |
| DE | Duitsland | 16 | 18 |
| DK | Denemarken | 16 | 18 |
| EE | Estland | 16 | 18 |
| EG | Egypte | Geen | 21 |
| ES | Spanje | 13 | 18 |
| FR | Frankrijk | 16 | 18 |
| GB | Verenigd Koninkrijk | 13 | 18 |
| GR | Griekenland | 16 | 18 |
| HR | Kroatië | 16 | 18 |
| HU | Hongarije | 16 | 18 |
| IE | Ierland | 13 | 18 |
| IT | Italië | 16 | 18 |
| KR | Zuid-Korea | 14 | 18 |
| LT | Litouwen | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Letland | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namibië | Geen | 21 |
| NL | Nederland | 16 | 18 |
| PL | Polen | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Roemenië | 16 | 18 |
| SE | Zweden | 13 | 18 |
| SG | Singapore | Geen | 21 |
| SI | Slovenië | 16 | 18 |
| SK | Slowakije | 16 | 18 |
| TD | Tsjaad | Geen | 21 |
| TH | Thailand | Geen | 20 |
| TW | Taiwan | Geen | 20 |
| VS | Verenigde Staten | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Gebruiksrecht overeenkomst vastleggen

Wanneer u uw toepassing ontwikkelt, legt u Norma liter gebruikers de acceptatie van gebruiks voorwaarden binnen hun toepassingen vast zonder enige secundaire deelname van de gebruikers lijst. Het is echter wel mogelijk om een Azure AD B2C gebruikers stroom te gebruiken voor het verzamelen van de gebruiks voorwaarden van een gebruiker, het beperken van de toegang als acceptatie niet wordt verleend en het afdwingen van toekomstige wijzigingen in de gebruiks voorwaarden, op basis van de datum van de laatste acceptatie en de datum van de meest recente versie van de gebruiks voorwaarden.

**Gebruiks voorwaarden** kunnen ook ' toestemming geven om gegevens met derden te delen ' bevatten. Afhankelijk van de lokale regelgeving en bedrijfs regels, kunt u de acceptatie van een gebruiker van beide voor waarden verzamelen, of u kunt de gebruiker toestaan één voor waarde te accepteren en niet de andere.

In de volgende stappen wordt beschreven hoe u gebruiks voorwaarden kunt beheren:

1. Registreer de acceptatie van de gebruiks voorwaarden en de datum van de acceptatie door gebruik te maken van de Graph API en uitgebreide kenmerken. U kunt dit doen door zowel ingebouwde als aangepaste gebruikers stromen te gebruiken. U wordt aangeraden de kenmerken **extension_termsOfUseConsentDateTime** en **extension_termsOfUseConsentVersion** te maken en te gebruiken.

2. Maak een vereist selectie vakje met het label ' Gebruiks voorwaarden accepteren ' en noteer het resultaat tijdens de aanmelding. U kunt dit doen door zowel ingebouwde als aangepaste gebruikers stromen te gebruiken.

3. Azure AD B2C slaat de gebruiksrecht overeenkomst en de acceptatie van de gebruiker op. U kunt de Graph API gebruiken om de status van een wille keurige gebruiker op te vragen, door te lezen van het uitbreidings kenmerk dat wordt gebruikt voor het registreren van het antwoord (bijvoorbeeld **termsOfUseTestUpdateDateTime**). U kunt dit doen door zowel ingebouwde als aangepaste gebruikers stromen te gebruiken.

4. Acceptatie van bijgewerkte gebruiks voorwaarden vereisen door de datum van aanvaar ding te vergelijken met de datum van de meest recente versie van de gebruiks voorwaarden. U kunt de datums alleen vergelijken met behulp van een aangepaste gebruikers stroom. Gebruik het uitgebreide kenmerk **extension_termsOfUseConsentDateTime** en vergelijk de waarde met de claim van **termsOfUseTextUpdateDateTime**. Als de acceptatie oud is, dwingt u een nieuwe acceptatie af door een zelfbevestigend scherm weer te geven. Anders blokkeert u de toegang door beleids logica te gebruiken.

5. Acceptatie van bijgewerkte gebruiks voorwaarden vereisen door het versie nummer van de acceptatie te vergelijken met het meest recente geaccepteerde versie nummer. U kunt alleen versie nummers vergelijken met behulp van een aangepaste gebruikers stroom. Gebruik het uitgebreide kenmerk **extension_termsOfUseConsentDateTime** en vergelijk de waarde met de claim van **extension_termsOfUseConsentVersion**. Als de acceptatie oud is, dwingt u een nieuwe acceptatie af door een zelfbevestigend scherm weer te geven. Anders blokkeert u de toegang door beleids logica te gebruiken.

U kunt acceptatie van gebruiks voorwaarden vastleggen in de volgende scenario's:

- Er wordt een nieuwe gebruiker aangemeld. De gebruiks voorwaarden worden weer gegeven en het acceptatie resultaat wordt opgeslagen.
- Een gebruiker meldt zich aan wie eerder de meest recente of actieve gebruiks voorwaarden heeft geaccepteerd. De gebruiks voorwaarden worden niet weer gegeven.
- Een gebruiker meldt zich aan wie de meest recente of actieve gebruiks voorwaarden nog niet heeft geaccepteerd. De gebruiks voorwaarden worden weer gegeven en het acceptatie resultaat wordt opgeslagen.
- Een gebruiker meldt zich aan wie al een oudere versie van de gebruiks voorwaarden heeft geaccepteerd, die nu is bijgewerkt naar de nieuwste versie. De gebruiks voorwaarden worden weer gegeven en het acceptatie resultaat wordt opgeslagen.

In de volgende afbeelding ziet u de aanbevolen gebruikers stroom:

![Diagram van een stroom diagram met de aanbevolen stroom voor acceptatie gebruikers](./media/manage-user-access/user-flow.png)

Hier volgt een voor beeld van een gebruiks instemming op basis van datum in een claim. Als de `extension_termsOfUseConsentDateTime` claim ouder is dan `2025-01-15T00:00:00` , dwingt u een nieuwe acceptatie af door de `termsOfUseConsentRequired` Booleaanse claim te controleren en een zelfbevestigend scherm weer te geven. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Hier volgt een voor beeld van een op versies gebaseerde gebruiksrecht overeenkomst in een claim. Als de `extension_termsOfUseConsentVersion` claim niet gelijk is aan `V1` , dwingt u een nieuwe acceptatie af door de `termsOfUseConsentRequired` Booleaanse claim te controleren en een zelfbevestigend scherm weer te geven.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Volgende stappen

- De [leeftijds beperking inschakelen in azure AD B2C](age-gating.md).
- Zie [gebruikers gegevens beheren](manage-user-data.md)voor meer informatie over het verwijderen en exporteren van gebruikers gegevens.
- Voor een voor beeld van een aangepast beleid waarmee een prompt van het gebruik van de gebruiksrecht overeenkomst wordt geïmplementeerd, raadpleegt [u een aangepast beleid voor B2C IEF-aanmelden en aanmelden met de prompt ' gebruiksrecht overeenkomst '](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).