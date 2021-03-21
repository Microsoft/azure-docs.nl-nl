---
title: Kenmerken van gebruikers profielen in Azure Active Directory B2C
description: Meer informatie over de kenmerken van het gebruikers bron type die worden ondersteund door de Azure AD B2C Directory-gebruikers profiel. Meer informatie over ingebouwde kenmerken, uitbrei dingen en hoe kenmerken worden toegewezen aan Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500526"
---
# <a name="user-profile-attributes"></a>Kenmerken van gebruikersprofiel

Uw Azure Active Directory (Azure AD) B2C Directory-gebruikers profiel wordt geleverd met een ingebouwde set kenmerken, zoals de naam, de voor waarde, de plaats, de post code en het telefoon nummer. U kunt het gebruikers profiel uitbreiden met uw eigen toepassings gegevens zonder dat hiervoor een extern gegevens archief nodig is. 

De meeste kenmerken die kunnen worden gebruikt met Azure AD B2C gebruikers profielen worden ook ondersteund door Microsoft Graph. In dit artikel worden ondersteunde Azure AD B2C kenmerken voor gebruikers profielen beschreven. Ook worden de kenmerken aangegeven die niet worden ondersteund door Microsoft Graph, evenals Microsoft Graph kenmerken die niet met Azure AD B2C mogen worden gebruikt.

> [!IMPORTANT]
> U moet geen ingebouwde of extensie kenmerken gebruiken om gevoelige persoonlijke gegevens op te slaan, zoals account referenties, sofi-identificatie nummers, kaart gegevens, financiële account gegevens, informatie over gezondheids zorg of gevoelige achtergrond informatie.

U kunt ook integreren met externe systemen. U kunt bijvoorbeeld Azure AD B2C gebruiken voor verificatie, maar deze delegeren aan een externe klant Relationship Management (CRM) of klant loyale Data Base als de gezaghebbende bron van klant gegevens. Zie de oplossing voor [externe profielen](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) voor meer informatie.

## <a name="azure-ad-user-resource-type"></a>Resource type van Azure AD-gebruiker

In de volgende tabel worden de kenmerken van het [bron type](/graph/api/resources/user) van de gebruiker weer gegeven die worden ondersteund door de Azure AD B2C Directory-gebruikers profiel. Het geeft de volgende informatie over elk kenmerk:

- De kenmerk naam die wordt gebruikt door Azure AD B2C (gevolgd door de naam van de Microsoft Graph tussen haakjes, indien anders)
- Gegevens type van kenmerk
- Kenmerk Beschrijving
- Als het kenmerk beschikbaar is in de Azure Portal
- Als het kenmerk kan worden gebruikt in een gebruikers stroom
- Als het kenmerk kan worden gebruikt in een aangepast beleid voor [Azure AD technische profielen](active-directory-technical-profile.md) en in welke sectie ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; of &lt; PersistedClaims &gt; )

|Naam     |Type     |Beschrijving|Azure Portal|Gebruikersstromen|Aangepast beleid|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Booleaans|Hiermee wordt aangegeven of het gebruikers account is in-of uitgeschakeld: **True** als het account is ingeschakeld, anders **False**.|Ja|Nee|Persistent gemaakt, uitvoer|
|ageGroup        |Tekenreeks|De leeftijds groep van de gebruiker. Mogelijke waarden: null, undefined, Minor, volwassene, NotAdult.|Ja|Nee|Persistent gemaakt, uitvoer|
|alternativeSecurityId ([identiteiten](#identities-attribute))|Tekenreeks|Eén gebruikers identiteit van de externe ID-provider.|Nee|Nee|Invoer, persistent, uitvoer|
|alternativeSecurityIds ([identiteiten](#identities-attribute))|alternatieve securityId-verzameling|Een verzameling gebruikers identiteiten van externe ID-providers.|Nee|Nee|Persistent gemaakt, uitvoer|
|city            |Tekenreeks|De plaats waarin de gebruiker zich bevindt. Maximale lengte van 128.|Ja|Ja|Persistent gemaakt, uitvoer|
|consentProvidedForMinor|Tekenreeks|Hiermee wordt aangegeven of de toestemming voor een secundaire is toegestaan. Toegestane waarden: null, verleend, geweigerd of notRequired.|Ja|Nee|Persistent gemaakt, uitvoer|
|country         |Tekenreeks|Het land/de regio waarin de gebruiker zich bevindt. Voor beeld: "US" of "UK". Maximale lengte van 128.|Ja|Ja|Persistent gemaakt, uitvoer|
|createdDateTime|DateTime|De datum waarop het gebruikers object is gemaakt. Alleen-lezen.|Nee|Nee|Persistent gemaakt, uitvoer|
|creationType    |Tekenreeks|Als het gebruikers account is gemaakt als een lokaal account voor een Azure Active Directory B2C Tenant, is de waarde LocalAccount of nameCoexistence. Alleen-lezen.|Nee|Nee|Persistent gemaakt, uitvoer|
|dateOfBirth     |Datum|Geboortedatum.|Nee|Nee|Persistent gemaakt, uitvoer|
|department      |Tekenreeks|De naam van de afdeling waarin de gebruiker werkt. Maximale lengte van 64.|Ja|Nee|Persistent gemaakt, uitvoer|
|displayName     |Tekenreeks|De weergave naam voor de gebruiker. Maximale lengte van 256.|Ja|Ja|Persistent gemaakt, uitvoer|
|facsimileTelephoneNumber<sup>1</sup>|Tekenreeks|Het telefoon nummer van de zakelijke faxmachine van de gebruiker.|Ja|Nee|Persistent gemaakt, uitvoer|
|givenName       |Tekenreeks|De opgegeven naam (voor naam) van de gebruiker. Maximale lengte van 64.|Ja|Ja|Persistent gemaakt, uitvoer|
|jobTitle        |Tekenreeks|De functie van de gebruiker. Maximale lengte van 128.|Ja|Ja|Persistent gemaakt, uitvoer|
|immutableId     |Tekenreeks|Een id die meestal wordt gebruikt voor gebruikers die zijn gemigreerd vanuit een on-premises Active Directory.|Nee|Nee|Persistent gemaakt, uitvoer|
|legalAgeGroupClassification|Tekenreeks|Groeps classificatie juridische leeftijd. Alleen-lezen en berekend op basis van de eigenschappen ageGroup en consentProvidedForMinor. Toegestane waarden: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult en volwassene.|Ja|Nee|Persistent gemaakt, uitvoer|
|legalCountry<sup>1</sup>  |Tekenreeks|Land/regio voor juridische doel einden.|Nee|Nee|Persistent gemaakt, uitvoer|
|mail            |Tekenreeks|Het SMTP-adres voor de gebruiker, bijvoorbeeld bob@contoso.com . Alleen-lezen.|Nee|Nee|Persistent gemaakt, uitvoer|
|mailNickName    |Tekenreeks|De e-mail alias voor de gebruiker. Maximale lengte van 64.|Nee|Nee|Persistent gemaakt, uitvoer|
|mobiel (mobilePhone) |Tekenreeks|Het primaire mobiele telefoon nummer voor de gebruiker. Maximale lengte van 64.|Ja|Nee|Persistent gemaakt, uitvoer|
|netId           |Tekenreeks|Net-ID.|Nee|Nee|Persistent gemaakt, uitvoer|
|objectId        |Tekenreeks|Een Globally Unique Identifier (GUID) die de unieke id voor de gebruiker is. Voor beeld: 12345678-9abc-def0-1234-56789abcde. Alleen-lezen, onveranderbaar.|Alleen-lezen|Ja|Invoer, persistent, uitvoer|
|otherMails      |Tekenreeksverzameling|Een lijst met andere e-mail adressen voor de gebruiker. Voor beeld: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ja (alternatief e-mail adres)|Nee|Persistent gemaakt, uitvoer|
|wachtwoord        |Tekenreeks|Het wacht woord voor het lokale account tijdens het maken van de gebruiker.|Nee|Nee|Persistente|
|passwordPolicies     |Tekenreeks|Het beleid van het wacht woord. Het is een teken reeks die bestaat uit een andere beleids naam, gescheiden door komma's. Bijvoorbeeld, DisablePasswordExpiration, DisableStrongPassword.|Nee|Nee|Persistent gemaakt, uitvoer|
|physicalDeliveryOfficeName (officeLocation)|Tekenreeks|De kantoor locatie in de bedrijfs plaats van de gebruiker. Maximale lengte van 128.|Ja|Nee|Persistent gemaakt, uitvoer|
|postalCode      |Tekenreeks|De post code voor het post adres van de gebruiker. De post code is specifiek voor het land/de regio van de gebruiker. In de Verenigde Staten van America bevat dit kenmerk de post code. Maximale lengte van 40.|Ja|Nee|Persistent gemaakt, uitvoer|
|preferredLanguage    |Tekenreeks|De voorkeurs taal voor de gebruiker. De voorkeurs taal is gebaseerd op RFC 4646. De naam is een combi natie van een ISO 639 2-letter kleine-cultuur code die is gekoppeld aan de taal en een ISO 3166 2-letter hoofd code subcultuur voor het land of de regio. Voor beeld: "en-US" of "es-ES".|Nee|Nee|Persistent gemaakt, uitvoer|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Vernieuwings tokens die vóór deze tijd zijn uitgegeven, zijn ongeldig en er wordt een fout melding weer gegeven wanneer toepassingen een ongeldig vernieuwings token gebruiken om een nieuw toegangs token op te halen. Als dit het geval is, moet de toepassing een nieuw vernieuwings token verkrijgen door een aanvraag naar het toestemming eind punt te maken. Alleen-lezen.|Nee|Nee|Uitvoer|
|signInNames ([identiteiten](#identities-attribute)) |Tekenreeks|De unieke aanmeldings naam van de gebruiker van het lokale account van een wille keurig type in de Directory. Gebruik dit kenmerk om een gebruiker op te halen met een aanmeldings waarde zonder het lokale account type op te geven.|Nee|Nee|Invoer|
|signInNames. userName ([identiteiten](#identities-attribute)) |Tekenreeks|De unieke gebruikers naam van de lokale account gebruiker in de Directory. Gebruik dit kenmerk om een gebruiker te maken of op te halen met een specifieke gebruikers naam voor aanmelden. Als u deze para meter in PersistedClaims opgeeft, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|signInNames. phoneNumber ([identiteiten](#identities-attribute)) |Tekenreeks|Het unieke telefoon nummer van de lokale account gebruiker in de Directory. Gebruik dit kenmerk om een gebruiker met een specifiek aanmeldings nummer te maken of op te halen. Als u dit kenmerk opgeeft in PersistedClaims tijdens een patch bewerking, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|signInNames. emailAddress ([identiteiten](#identities-attribute))|Tekenreeks|Het unieke e-mail adres van de lokale account gebruiker in de Directory. Gebruik deze om een gebruiker met een specifiek e-mail adres voor aanmelden te maken of op te halen. Als u dit kenmerk opgeeft in PersistedClaims tijdens een patch bewerking, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|staat           |Tekenreeks|De staat of provincie in het adres van de gebruiker. Maximale lengte van 128.|Ja|Ja|Persistent gemaakt, uitvoer|
|streetAddress   |Tekenreeks|Het adres van de bedrijfs locatie van de gebruiker. Maximale lengte van 1024.|Ja|Ja|Persistent gemaakt, uitvoer|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Tekenreeks|Het secundaire telefoon nummer van de gebruiker die wordt gebruikt voor multi-factor Authentication.|Ja|Nee|Persistent gemaakt, uitvoer|
|strongAuthenticationEmailAddress<sup>1</sup>|Tekenreeks|Het SMTP-adres voor de gebruiker. Voor beeld: " bob@contoso.com " dit kenmerk wordt gebruikt voor aanmelding met gebruikers naam beleid, om het e-mail adres van de gebruiker op te slaan. Het e-mail adres wordt vervolgens gebruikt in een stroom voor het opnieuw instellen van een wacht woord.|Ja|Nee|Persistent gemaakt, uitvoer|
|strongAuthenticationPhoneNumber<sup>2</sup>|Tekenreeks|Het primaire telefoon nummer van de gebruiker die wordt gebruikt voor multi-factor Authentication.|Ja|Nee|Persistent gemaakt, uitvoer|
|surname         |Tekenreeks|De voor naam van de gebruiker (familie naam of achternaam). Maximale lengte van 64.|Ja|Ja|Persistent gemaakt, uitvoer|
|telephoneNumber (eerste vermelding van businessPhones)|Tekenreeks|Het primaire telefoon nummer van de bedrijfs plaats van de gebruiker.|Ja|Nee|Persistent gemaakt, uitvoer|
|userPrincipalName    |Tekenreeks|De UPN (user Principal name) van de gebruiker. De UPN is een aanmeldings naam voor Internet-stijl voor de gebruiker op basis van Internet Standard RFC 822. Het domein moet aanwezig zijn in de verzameling van geverifieerde domeinen van de Tenant. Deze eigenschap is vereist wanneer een account wordt gemaakt. Onveranderbare.|Nee|Nee|Invoer, persistent, uitvoer|
|usageLocation   |Tekenreeks|Vereist voor gebruikers aan wie licenties moeten worden toegewezen als gevolg van wettelijke vereisten om te controleren of de services beschikbaar zijn in landen/regio's. Geen Null-waarden. Een land/regio code van twee letters (ISO-standaard 3166). Voor beelden: "US", "JP" en "GB".|Ja|Nee|Persistent gemaakt, uitvoer|
|userType        |Tekenreeks|Een teken reeks waarde die kan worden gebruikt voor het classificeren van gebruikers typen in uw Directory. Waarde moet lid zijn. Alleen-lezen.|Alleen-lezen|Nee|Persistent gemaakt, uitvoer|
|userState (externalUserState)<sup>3</sup>|Tekenreeks|Alleen voor Azure AD B2B-account geeft aan of de uitnodiging wordt PendingAcceptance of geaccepteerd.|Nee|Nee|Persistent gemaakt, uitvoer|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Toont de tijds tempel voor de laatste wijziging van de eigenschap UserState.|Nee|Nee|Persistent gemaakt, uitvoer|

<sup>1 </sup> Niet ondersteund door Microsoft Graph<br><sup>2 </sup> Zie voor meer informatie [MFA-telefoon nummer kenmerk](#mfa-phone-number-attribute)<br><sup>3 </sup> Mag niet worden gebruikt met Azure AD B2C

## <a name="display-name-attribute"></a>Weergave naam kenmerk

De `displayName` is de naam die moet worden weer gegeven in azure Portal gebruikers beheer voor de gebruiker en in het toegangs token Azure AD B2C wordt geretourneerd naar de toepassing. Deze eigenschap is vereist.

## <a name="identities-attribute"></a>Kenmerk Identities

Een klant account, een consument, een partner of een burger, kan worden gekoppeld aan deze identiteits typen:

- **Lokale** identiteit: de gebruikers naam en het wacht woord worden lokaal opgeslagen in de Azure AD B2C Directory. We verwijzen vaak naar deze identiteiten als 'lokale accounts'.
- **Federatieve** identiteit: ook wel een *sociale* of *ondernemings* account genoemd, wordt de identiteit van de gebruiker beheerd door een federatieve id-provider zoals Facebook, micro soft, ADFS of Sales Force.

Een gebruiker met een klant account kan zich aanmelden met meerdere identiteiten. Bijvoorbeeld gebruikers naam, e-mail adres, werk nemer-ID, sofi-ID en anderen. Eén account kan meerdere identiteiten, zowel lokaal als sociaal, hebben met hetzelfde wacht woord. 

In de Microsoft Graph-API worden lokale en federatieve identiteiten opgeslagen in het `identities` kenmerk gebruiker, dat van het type [objectIdentity](/graph/api/resources/objectidentity)is. De `identities` verzameling vertegenwoordigt een set met identiteiten die worden gebruikt om u aan te melden bij een gebruikers account. Met deze verzameling kan de gebruiker zich aanmelden bij het gebruikers account met een van de bijbehorende identiteiten. Het kenmerk Identities kan Maxi maal tien [objectIdentity](/graph/api/resources/objectidentity) -objecten bevatten. Elk object bevat de volgende eigenschappen:

| Naam   | Type |Beschrijving|
|:---------------|:--------|:----------|
|signInType|tekenreeks| Hiermee geeft u de aanmeldings typen voor gebruikers in uw Directory. Voor een lokaal account:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` of een ander type dat u wilt. Er moet een sociaal account worden ingesteld op  `federated` .|
|uitgever|tekenreeks|Hiermee geeft u de uitgever van de identiteit. Voor lokale accounts (waarbij **signInType** niet is `federated` ), is deze eigenschap de lokale standaard domein naam van de B2C-Tenant, bijvoorbeeld `contoso.onmicrosoft.com` . Voor sociale identiteit (waarbij **signInType** is  `federated` ), is de waarde de naam van de verlener, bijvoorbeeld `facebook.com`|
|issuerAssignedId|tekenreeks|Hiermee geeft u de unieke id op die door de uitgever aan de gebruiker is toegewezen. De combi natie van **verlener** en **issuerAssignedId** moet uniek zijn binnen uw Tenant. Als **signInType** is ingesteld op `emailAddress` of, vertegenwoordigt het lokale account `userName` de aanmeldings naam voor de gebruiker.<br>Wanneer **signInType** is ingesteld op: <ul><li>`emailAddress` (of begint met `emailAddress` like `emailAddress1` ) **issuerAssignedId** moet een geldig e-mail adres zijn</li><li>`userName` (of een andere waarde), **issuerAssignedId** moet een geldig [lokaal deel zijn van een e-mail adres](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** staat voor de unieke id van het federatieve account</li></ul>|

Het volgende kenmerk **Identities** , met een lokale account identiteit met een aanmeldings naam, een e-mail adres als aanmelding en een sociale identiteit. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Voor federatieve identiteiten, afhankelijk van de ID-provider, is het **issuerAssignedId** een unieke waarde voor een bepaalde gebruiker per toepassing of ontwikkelings account. Configureer het Azure AD B2C-beleid met dezelfde toepassings-ID die eerder is toegewezen door de sociale provider of een andere toepassing binnen hetzelfde ontwikkelings account. 

## <a name="password-profile-property"></a>Eigenschap voor wachtwoord profiel

Voor een lokale identiteit is het kenmerk **passwordProfile** vereist en bevat het wacht woord van de gebruiker. Het `forceChangePasswordNextSignIn` kenmerk geeft aan of een gebruiker het wacht woord opnieuw moet instellen bij de volgende aanmelding. [Stel geforceerde wachtwoord herstel stroom](force-password-reset.md)in om een geforceerde wachtwoord herstel af te handelen.

Voor een federatieve (sociale) identiteit is het **passwordProfile** -kenmerk niet vereist.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Kenmerk wachtwoord beleid

Het Azure AD B2C wachtwoord beleid (voor lokale accounts) is gebaseerd op het Azure Active Directory beleid voor [sterke wachtwoord sterkte](../active-directory/authentication/concept-sspr-policy.md) . Voor het beleid voor Azure AD B2C registreren of aanmelden en voor het opnieuw instellen van het wacht woord is de sterke wachtwoord sterkte vereist en wacht woorden niet verlopen.

Als de accounts die u wilt migreren, een zwakkere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte](../active-directory/authentication/concept-sspr-policy.md) die wordt afgedwongen door Azure AD B2C, kunt u de vereiste voor sterke wacht woorden uitschakelen in scenario's voor gebruikers migratie. Als u het standaard wachtwoord beleid wilt wijzigen, stelt `passwordPolicies` u het kenmerk in op `DisableStrongPassword` . U kunt de aanvraag voor het maken van een gebruiker bijvoorbeeld als volgt wijzigen:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Kenmerk MFA-telefoon nummer

Wanneer u een telefoon gebruikt voor multi-factor Authentication (MFA), wordt de mobiele telefoon gebruikt voor het verifiëren van de identiteit van de gebruiker. Als u een nieuw telefoon nummer wilt [toevoegen](/graph/api/authentication-post-phonemethods) via programma code, het telefoon nummer [bijwerken](/graph/api/b2cauthenticationmethodspolicy-update), [ophalen](/graph/api/b2cauthenticationmethodspolicy-get)of [verwijderen](/graph/api/phoneauthenticationmethod-delete) , gebruikt u MS Graph API [methode voor telefonische verificatie](/graph/api/resources/phoneauthenticationmethod).

In Azure AD B2C [aangepast beleid](custom-policy-overview.md), het telefoon nummer is beschikbaar via `strongAuthenticationPhoneNumber` claim type.

## <a name="extension-attributes"></a>Extensie kenmerken

Elke klant gerichte toepassing heeft unieke vereisten voor het verzamelen van de gegevens. Uw Azure AD B2C-Tenant wordt geleverd met een ingebouwde set informatie die is opgeslagen in eigenschappen, zoals de naam, de voor-en de post code. Met Azure AD B2C kunt u de set eigenschappen die is opgeslagen in elk klant account uitbreiden. Zie [gebruikers kenmerken toevoegen en gebruikers invoer aanpassen in azure Active Directory B2C](configure-user-input.md) voor meer informatie.

Extensie kenmerken [breiden het schema](/graph/extensibility-overview#schema-extensions) van de gebruikers objecten in de map uit. De extensie kenmerken kunnen alleen worden geregistreerd voor een toepassings object, hoewel ze mogelijk gegevens voor een gebruiker bevatten. Het kenmerk extension is gekoppeld aan de toepassing `b2c-extensions-app` . Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C om gebruikers gegevens op te slaan. U kunt deze toepassing vinden onder Azure Active Directory App-registraties.

> [!NOTE]
> - Er kunnen Maxi maal 100 extensie kenmerken naar elk gebruikers account worden geschreven.
> - Als de B2C-Extensions-App-toepassing wordt verwijderd, worden deze extensie kenmerken verwijderd uit alle gebruikers samen met alle gegevens die ze bevatten.
> - Als een extensie kenmerk wordt verwijderd door de toepassing, wordt het verwijderd uit alle gebruikers accounts en worden de waarden verwijderd.

Extensie kenmerken in de Graph API worden genoemd met behulp van de `extension_ApplicationClientID_AttributeName` -Conventie, waarbij de de `ApplicationClientID` **toepassing (client) ID** van de `b2c-extensions-app` toepassing (in **app-registraties**  >  **alle toepassingen** in de Azure Portal). Houd er rekening mee dat de ID van de **toepassing (client)** zoals deze wordt weer gegeven in de naam van het uitbreidings kenmerk geen afbreek streepjes bevat. Bijvoorbeeld:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

De volgende gegevens typen worden ondersteund bij het definiëren van een kenmerk in een schema-uitbrei ding:

|Type |Opmerkingen  |
|--------------|---------|
|Booleaans    | Mogelijke waarden: **True** of **False**. |
|DateTime   | Moet worden opgegeven in ISO 8601-indeling. Worden opgeslagen in UTC.   |
|Geheel getal    | 32-bits waarde.               |
|Tekenreeks     | Maxi maal 256 tekens.     |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over extensie kenmerken:

- [Schema-uitbreidingen](/graph/extensibility-overview#schema-extensions)
- [Aangepaste kenmerken definiëren](user-flow-custom-attributes.md)
