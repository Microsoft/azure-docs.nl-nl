---
title: UserJourneys | Microsoft Docs
description: Geef het UserJourneys-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05307fe2ad9e0a59fa11c30f2dc7154ba5076603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174662"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met gebruikersbelevingen worden expliciete paden opgegeven waarmee een op claims gebaseerde toepassing de gewenste claims voor een gebruiker kan verkrijgen. De gebruiker wordt door deze paden geleid om de claims op te halen die aan de Relying Party moeten worden gepresenteerd. Met andere woorden, de gebruikers trajecten definiëren de bedrijfs logica van wat een eind gebruiker doorloopt als het Azure AD B2C identiteits ervarings raamwerk de aanvraag verwerkt.

Deze gebruikers ritten kunnen worden beschouwd als beschik bare sjablonen om te voldoen aan de kern behoeften van de verschillende relying party's van de community. Gebruikers ritten vergemakkelijken de definitie van het Relying Party onderdeel van een beleid. Een beleid kan meerdere gebruikers trajecten definiëren. Elke gebruikers traject is een reeks Orchestration-stappen.

Als u de door het beleid ondersteunde gebruikers trajecten wilt definiëren, wordt er een **UserJourneys** -element toegevoegd onder het element op het hoogste niveau van het beleids bestand.

Het element **UserJourneys** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Een gebruikers traject waarmee alle benodigde constructies voor een volledige gebruikers stroom worden gedefinieerd. |

Het element **UserJourney** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id van een gebruikers traject die kan worden gebruikt om ernaar te verwijzen vanuit andere elementen in het beleid. Het **DefaultUserJourney** -element van het [Relying Party-beleid](relyingparty.md) verwijst naar dit kenmerk. |

Het **UserJourney** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Lijst met technische profielen voor autorisatie. | 
| OrchestrationSteps | 1: n | Een indelings reeks die moet worden gevolgd voor een geslaagde trans actie. Elke gebruikers traject bestaat uit een geordende lijst met Orchestration-stappen die in volg orde worden uitgevoerd. Als er een stap mislukt, mislukt de trans actie. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Stel dat een gebruiker een UserJourney heeft voltooid en een toegangs-of ID-token heeft verkregen. Voor het beheren van aanvullende resources, zoals het [User info-eind punt](userinfo-endpoint.md), moet de gebruiker worden geïdentificeerd. Om dit proces te starten, moet de gebruiker het toegangs token aanbieden dat eerder is uitgegeven als bewijs dat ze oorspronkelijk zijn geverifieerd door een geldig Azure AD B2C-beleid. Een geldig token voor de gebruiker moet altijd aanwezig zijn tijdens dit proces om ervoor te zorgen dat de gebruiker deze aanvraag mag doen. De technische profielen voor autorisatie valideren het inkomende token en halen claims uit het token op.

Het element **AuthorizationTechnicalProfiles** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Lijst met technische profielen voor autorisatie. | 

Het element **AuthorizationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Ja | De id van het technische profiel dat moet worden uitgevoerd. |

In het volgende voor beeld wordt een reis element van de gebruiker met autorisatie technische profielen weer gegeven:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

Een gebruikers traject wordt weer gegeven als een indelings reeks die moet worden gevolgd door een geslaagde trans actie. Als er een stap mislukt, mislukt de trans actie. Deze indelings stappen verwijzen naar zowel de bouw stenen als de claim providers die in het beleids bestand zijn toegestaan. Een indelings stap die verantwoordelijk is voor het weer geven of renderen van een gebruikers ervaring, heeft ook een verwijzing naar de bijbehorende inhouds definitie-id.

De Orchestration-stappen kunnen voorwaardelijk worden uitgevoerd op basis van de voor waarden die zijn gedefinieerd in het gedeelte van de Orchestration-stap. U kunt bijvoorbeeld alleen controleren of er een indelings stap wordt uitgevoerd als er een specifieke claim bestaat of als een claim gelijk is aan of niet aan de opgegeven waarde is.

Als u de geordende lijst met Orchestration-stappen wilt opgeven, wordt een **OrchestrationSteps** -element toegevoegd als onderdeel van het beleid. Dit element is vereist.

Het element **OrchestrationSteps** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Een geordende Orchestration-stap. |

Het **OrchestrationStep** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Order` | Ja | De volg orde van de Orchestration-stappen. |
| `Type` | Ja | Het type van de Orchestration-stap. Mogelijke waarden: <ul><li>**ClaimsProviderSelection** : geeft aan dat de indelings stap verschillende claim providers aan de gebruiker geeft om er een te selecteren.</li><li>**CombinedSignInAndSignUp** : geeft aan dat de indelings stap een gecombineerde aanmeldings pagina voor sociale providers en een lokale account bevat.</li><li>**ClaimsExchange** : geeft aan dat de Orchestration-stap claims met een claim provider uitwisselt.</li><li>**GetClaims** : Hiermee geeft u op dat met de indelings stap claim gegevens moeten worden verwerkt die via de configuratie van de Relying Party naar Azure AD B2C worden verzonden `InputClaims` .</li><li>**InvokeSubJourney** : geeft aan dat de Orchestration Step claims met een [subtraject](subjourneys.md) (in open bare preview) uitwisselt.</li><li>**SendClaims** : geeft aan dat de indelings stap de claims verzendt naar de Relying Party met een token dat is uitgegeven door een claim verlener.</li></ul> |
| ContentDefinitionReferenceId | Nee | De id van de [inhouds definitie](contentdefinitions.md) die aan deze Orchestration-stap is gekoppeld. Normaal gesp roken wordt de verwijzings-id van de inhouds definitie gedefinieerd in het zelfondertekende technische profiel. Er zijn echter enkele gevallen waarin Azure AD B2C iets zonder technisch profiel moet weer geven. Er zijn twee voor beelden: als het type indelings stap een van de volgende is `ClaimsProviderSelection`  `CombinedSignInAndSignUp` , moet Azure AD B2C de selectie van de identiteits provider weer geven zonder een technisch profiel. |
| CpimIssuerTechnicalProfileReferenceId | Nee | Het type van de Orchestration-stap is `SendClaims` . Met deze eigenschap wordt de technische profiel-id gedefinieerd van de claim provider die het token voor de Relying Party verleent.  Als dat niet het geval is, wordt er geen Relying Party token gemaakt. |

Het **OrchestrationStep** -element kan de volgende elementen bevatten:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Voor waarden | 0: n | Een lijst met voor waarden waaraan moet worden voldaan om de Orchestration-stap uit te voeren. |
| ClaimsProviderSelections | 0: n | Een lijst van claim provider selecties voor de Orchestration-stap. |
| ClaimsExchanges | 0: n | Een lijst met claim uitwisselingen voor de Orchestration-stap. |
| JourneyList | 0:1 | Een lijst met subtraject kandidaten voor de Orchestration-stap. |

### <a name="preconditions"></a>Voor waarden

Het element **voor waarden** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Precondition | 1: n | Afhankelijk van het technische profiel dat wordt gebruikt, wordt de client omgeleid volgens de selectie van de claim provider of wordt een server aanroep naar Exchange-claims. |


#### <a name="precondition"></a>Precondition

Orchestration-stappen kunnen voorwaardelijk worden uitgevoerd op basis van voor waarden die in de Orchestration-stap zijn gedefinieerd. Er zijn twee typen voor waarden:
 
- **Claims bestaan** : Hiermee geeft u op dat de acties moeten worden uitgevoerd als de opgegeven claims bestaan in de huidige claim verzameling van de gebruiker.
- **Claim is gelijk** aan-geeft aan dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. De controle voert een niet-hoofdletter gevoelige vergelijking uit. Bij het controleren van het type Booleaanse claim, gebruikt `True` of `False` .

Het element **voor waarde** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type controle of query dat moet worden uitgevoerd voor deze voor waarde. De waarde kan **ClaimsExist** zijn, waarmee wordt aangegeven dat de acties moeten worden uitgevoerd als de opgegeven claims bestaan in de huidige claimset van de gebruiker of **ClaimEquals**, waarmee wordt aangegeven dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. |
| `ExecuteActionsIf` | Ja | Gebruik a `true` of `false` test om te bepalen of de acties in de voor waarde moeten worden uitgevoerd. |

De elementen in de **voor waarde** bevatten de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Waarde | 1:2 | De id van een claim type. De claim is al gedefinieerd in de sectie claim schema in het beleids bestand of het bovenliggende beleids bestand. Wanneer de voor waarde van het type is `ClaimEquals` , bevat een tweede `Value` element de waarde die moet worden gecontroleerd. |
| Bewerking | 1:1 | De actie die moet worden uitgevoerd als de voor waarde wordt gecontroleerd binnen een indelings stap waar. Als de waarde `Action` is ingesteld op `SkipThisOrchestrationStep` , moet de gekoppelde `OrchestrationStep` niet worden uitgevoerd. |

#### <a name="preconditions-examples"></a>Voor beelden van voor waarden

Met de volgende voor waarden wordt gecontroleerd of de objectId van de gebruiker bestaat. In de gebruikers reis heeft de gebruiker geselecteerd om zich aan te melden met een lokaal account. Als de objectId bestaat, slaat u deze Orchestration-stap over.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Met de volgende voor waarden wordt gecontroleerd of de gebruiker zich heeft aangemeld met een sociaal account. Er is een poging gedaan om het gebruikers account in de Directory te vinden. Als de gebruiker zich aanmeldt of zich aanmeldt met een lokaal account, slaat u deze Orchestration-stap over.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Voor waarden kunnen meerdere voor waarden controleren. In het volgende voor beeld wordt gecontroleerd of ' objectId ' of ' e-mail ' bestaat. Als de eerste voor waarde waar is, slaat de traject over naar de volgende Orchestration-stap.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>Claim provider selecteren

Met de selectie van de identiteits provider kunnen gebruikers een actie selecteren in een lijst met opties. De selectie van de identiteits provider bestaat uit twee stappen: 

1. **Knoppen** : deze begint met het type `ClaimsProviderSelection` of `CombinedSignInAndSignUp` bevat een lijst met opties waaruit een gebruiker kan kiezen. De volg orde van de opties in het `ClaimsProviderSelections` element bepaalt de volg orde van de knoppen die aan de gebruiker worden gepresenteerd.
2. **Acties** : gevolgd door type `ClaimsExchange` . De ClaimsExchange bevat een lijst met acties. De actie is een verwijzing naar een technisch profiel, zoals [OAuth2](oauth2-technical-profile.md), [OpenID Connect Connect](openid-connect-technical-profile.md), [claims Transform](claims-transformation-technical-profile.md)of [zelfbevestigend](self-asserted-technical-profile.md). Wanneer een gebruiker op een van de knoppen klikt, wordt de bijbehorende actie uitgevoerd.

Het element **ClaimsProviderSelections** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1: n | Geeft de lijst met claim providers die kunnen worden geselecteerd.|

Het **ClaimsProviderSelections** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DisplayOption| Nee | Hiermee bepaalt u het gedrag van een geval waarbij één claim provider selectie beschikbaar is. Mogelijke waarden: `DoNotShowSingleProvider` (standaard), de gebruiker wordt direct omgeleid naar de provider voor federatieve identiteiten. Of `ShowSingleProvider` Azure AD B2C geeft de aanmeldings pagina weer met de selectie van de enkelvoudige ID-provider. Als u dit kenmerk wilt gebruiken, moet de versie van de [inhouds definitie](page-layout.md) gelijk zijn aan `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` of hoger.|

Het **ClaimsProviderSelection** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nee | De id van de claim uitwisseling, die wordt uitgevoerd in de volgende Orchestration-stap van de claim provider selectie. Dit kenmerk of het kenmerk ValidationClaimsExchangeId moet worden opgegeven, maar niet beide. |
| ValidationClaimsExchangeId | Nee | De id van de claim uitwisseling, die wordt uitgevoerd in de huidige Orchestration-stap om de claim provider selectie te valideren. Dit kenmerk of het kenmerk TargetClaimsExchangeId moet worden opgegeven, maar niet beide. |

### <a name="claims-provider-selection-example"></a>Voor beeld van claim provider selectie

In de volgende indelings stap kan de gebruiker kiezen om u aan te melden met Facebook, LinkedIn, Twitter, Google of een lokaal account. Als de gebruiker een van de sociale id-providers selecteert, wordt de tweede Orchestration-stap uitgevoerd met de geselecteerde claim uitwisseling die is opgegeven in het- `TargetClaimsExchangeId` kenmerk. Met de tweede Orchestration-stap wordt de gebruiker omgeleid naar de ID-provider voor sociale netwerken om het aanmeldings proces te volt ooien. Als de gebruiker ervoor kiest om zich aan te melden met het lokale account, blijft Azure AD B2C dezelfde indelings stap (dezelfde registratie pagina of aanmeldings pagina) en wordt de tweede Orchestration-stap overgeslagen.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Het element **ClaimsExchanges** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1: n | Afhankelijk van het gebruikte technische profiel wordt de client omgeleid op basis van de ClaimsProviderSelection die is geselecteerd, of wordt een server aanroep naar Exchange-claims. |

Het **ClaimsExchange** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id van de Exchange-stap voor claims. De id wordt gebruikt om te verwijzen naar de claim uitwisseling van een selectie stap van een claim provider in het beleid. |
| TechnicalProfileReferenceId | Ja | De id van het technische profiel dat moet worden uitgevoerd. |

## <a name="journeylist"></a>JourneyList

Het element **JourneyList** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Komt | 1:1 | Een verwijzing naar een subtraject die moet worden aangeroepen. |

### <a name="candidate"></a>Komt

Het element **kandidaat** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Ja | De id van de [subtraject](subjourneys.md) die moet worden uitgevoerd. |
