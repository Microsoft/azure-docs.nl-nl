---
title: Kenmerken van gebruikers profielen in Azure Active Directory B2C
description: Meer informatie over de kenmerken van het gebruikers bron type die worden ondersteund door de Azure AD B2C Directory-gebruikers profiel. Meer informatie over ingebouwde kenmerken, uitbrei dingen en hoe kenmerken worden toegewezen aan Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31926653950594b986aca19e2db2877cd655ca24
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509594"
---
# <a name="user-profile-attributes"></a>Kenmerken van gebruikersprofiel

Uw Azure Active Directory (Azure AD) B2C Directory-gebruikers profiel wordt geleverd met een ingebouwde set kenmerken, zoals de naam, de voor waarde, de plaats, de post code en het telefoon nummer. U kunt het gebruikers profiel uitbreiden met uw eigen toepassings gegevens zonder dat hiervoor een extern gegevens archief nodig is. De meeste kenmerken die kunnen worden gebruikt met Azure AD B2C gebruikers profielen worden ook ondersteund door Microsoft Graph. In dit artikel worden ondersteunde Azure AD B2C kenmerken voor gebruikers profielen beschreven. Ook worden de kenmerken aangegeven die niet worden ondersteund door Microsoft Graph, evenals Microsoft Graph kenmerken die niet met Azure AD B2C mogen worden gebruikt.

> [!IMPORTANT]
> U moet geen ingebouwde of extensie kenmerken gebruiken om gevoelige persoonlijke gegevens op te slaan, zoals account referenties, sofi-identificatie nummers, kaart gegevens, financiële account gegevens, informatie over gezondheids zorg of gevoelige achtergrond informatie.

U kunt ook integreren met externe systemen. U kunt bijvoorbeeld Azure AD B2C gebruiken voor verificatie, maar deze delegeren aan een externe klant Relationship Management (CRM) of klant loyale Data Base als de gezaghebbende bron van klant gegevens. Zie de oplossing voor [externe profielen](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) voor meer informatie.

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
|alternativeSecurityId ([identiteiten](manage-user-accounts-graph-api.md#identities-property))|Tekenreeks|Eén gebruikers identiteit van de externe ID-provider.|Nee|Nee|Invoer, persistent, uitvoer|
|alternativeSecurityIds ([identiteiten](manage-user-accounts-graph-api.md#identities-property))|alternatieve securityId-verzameling|Een verzameling gebruikers identiteiten van externe ID-providers.|Nee|Nee|Persistent gemaakt, uitvoer|
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
|objectId        |Tekenreeks|Een Globally Unique Identifier (GUID) die de unieke id voor de gebruiker is. Voor beeld: 12345678-9abc-def0-1234-56789abcde. Alleen-lezen, onveranderbaar.|Alleen-lezen|Yes|Invoer, persistent, uitvoer|
|otherMails      |Tekenreeksverzameling|Een lijst met aanvullende e-mail adressen voor de gebruiker. Voor beeld: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ja (alternatief e-mail adres)|No|Persistent gemaakt, uitvoer|
|wachtwoord        |Tekenreeks|Het wacht woord voor het lokale account tijdens het maken van de gebruiker.|Nee|Nee|Persistente|
|passwordPolicies     |Tekenreeks|Het beleid van het wacht woord. Het is een teken reeks die bestaat uit een andere beleids naam, gescheiden door komma's. Bijvoorbeeld, DisablePasswordExpiration, DisableStrongPassword.|Nee|Nee|Persistent gemaakt, uitvoer|
|physicalDeliveryOfficeName (officeLocation)|Tekenreeks|De kantoor locatie in de bedrijfs plaats van de gebruiker. Maximale lengte van 128.|Ja|Nee|Persistent gemaakt, uitvoer|
|postalCode      |Tekenreeks|De post code voor het post adres van de gebruiker. De post code is specifiek voor het land/de regio van de gebruiker. In de Verenigde Staten van America bevat dit kenmerk de post code. Maximale lengte van 40.|Ja|Nee|Persistent gemaakt, uitvoer|
|preferredLanguage    |Tekenreeks|De voorkeurs taal voor de gebruiker. De ISO 639-1-code moet volgen. Voor beeld: nl-nl.|Nee|Nee|Persistent gemaakt, uitvoer|
|refreshTokensValidFromDateTime|DateTime|Vernieuwings tokens die vóór deze tijd zijn uitgegeven, zijn ongeldig en er wordt een fout melding weer gegeven wanneer toepassingen een ongeldig vernieuwings token gebruiken om een nieuw toegangs token op te halen. Als dit het geval is, moet de toepassing een nieuw vernieuwings token verkrijgen door een aanvraag naar het toestemming eind punt te maken. Alleen-lezen.|Nee|Nee|Uitvoer|
|signInNames ([identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|De unieke aanmeldings naam van de gebruiker van het lokale account van een wille keurig type in de Directory. Gebruik dit kenmerk om een gebruiker op te halen met een aanmeldings waarde zonder het lokale account type op te geven.|Nee|Nee|Invoer|
|signInNames. userName ([identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|De unieke gebruikers naam van de lokale account gebruiker in de Directory. Gebruik dit kenmerk om een gebruiker te maken of op te halen met een specifieke gebruikers naam voor aanmelden. Als u deze para meter in PersistedClaims opgeeft, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|signInNames. phoneNumber ([identiteiten](manage-user-accounts-graph-api.md#identities-property)) |Tekenreeks|Het unieke telefoon nummer van de lokale account gebruiker in de Directory. Gebruik dit kenmerk om een gebruiker met een specifiek aanmeldings nummer te maken of op te halen. Als u dit kenmerk opgeeft in PersistedClaims tijdens een patch bewerking, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|signInNames. emailAddress ([identiteiten](manage-user-accounts-graph-api.md#identities-property))|Tekenreeks|Het unieke e-mail adres van de lokale account gebruiker in de Directory. Gebruik deze om een gebruiker met een specifiek e-mail adres voor aanmelden te maken of op te halen. Als u dit kenmerk opgeeft in PersistedClaims tijdens een patch bewerking, worden andere typen signInNames verwijderd. Als u een nieuw type signInNames wilt toevoegen, moet u ook bestaande signInNames persistent maken.|Nee|Nee|Invoer, persistent, uitvoer|
|staat           |Tekenreeks|De staat of provincie in het adres van de gebruiker. Maximale lengte van 128.|Ja|Ja|Persistent gemaakt, uitvoer|
|streetAddress   |Tekenreeks|Het adres van de bedrijfs locatie van de gebruiker. Maximale lengte van 1024.|Ja|Ja|Persistent gemaakt, uitvoer|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Tekenreeks|Het secundaire telefoon nummer van de gebruiker die wordt gebruikt voor multi-factor Authentication.|Ja|Nee|Persistent gemaakt, uitvoer|
|strongAuthenticationEmailAddress<sup>1</sup>|Tekenreeks|Het SMTP-adres voor de gebruiker. Voor beeld: " bob@contoso.com " dit kenmerk wordt gebruikt voor aanmelding met gebruikers naam beleid, om het e-mail adres van de gebruiker op te slaan. Het e-mail adres wordt vervolgens gebruikt in een stroom voor het opnieuw instellen van een wacht woord.|Ja|Nee|Persistent gemaakt, uitvoer|
|strongAuthenticationPhoneNumber<sup>2</sup>|Tekenreeks|Het primaire telefoon nummer van de gebruiker die wordt gebruikt voor multi-factor Authentication.|Ja|Nee|Persistent gemaakt, uitvoer|
|surname         |Tekenreeks|De voor naam van de gebruiker (familie naam of achternaam). Maximale lengte van 64.|Ja|Ja|Persistent gemaakt, uitvoer|
|telephoneNumber (eerste vermelding van businessPhones)|Tekenreeks|Het primaire telefoon nummer van de bedrijfs plaats van de gebruiker.|Ja|Nee|Persistent gemaakt, uitvoer|
|userPrincipalName    |Tekenreeks|De UPN (user Principal name) van de gebruiker. De UPN is een aanmeldings naam voor Internet-stijl voor de gebruiker op basis van Internet Standard RFC 822. Het domein moet aanwezig zijn in de verzameling van geverifieerde domeinen van de Tenant. Deze eigenschap is vereist wanneer een account wordt gemaakt. Onveranderbare.|Nee|Nee|Invoer, persistent, uitvoer|
|usageLocation   |Tekenreeks|Vereist voor gebruikers aan wie licenties moeten worden toegewezen als gevolg van wettelijke vereisten om te controleren of de services beschikbaar zijn in landen/regio's. Geen Null-waarden. Een land/regio code van twee letters (ISO-standaard 3166). Voor beelden: "US", "JP" en "GB".|Ja|Nee|Persistent gemaakt, uitvoer|
|userType        |Tekenreeks|Een teken reeks waarde die kan worden gebruikt voor het classificeren van gebruikers typen in uw Directory. Waarde moet lid zijn. Alleen-lezen.|Alleen-lezen|No|Persistent gemaakt, uitvoer|
|userState (externalUserState)<sup>3</sup>|Tekenreeks|Alleen voor Azure AD B2B-account geeft aan of de uitnodiging wordt PendingAcceptance of geaccepteerd.|Nee|Nee|Persistent gemaakt, uitvoer|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Toont de tijds tempel voor de laatste wijziging van de eigenschap UserState.|Nee|Nee|Persistent gemaakt, uitvoer|

<sup>1 </sup> Niet ondersteund door Microsoft Graph<br><sup>2 </sup> Zie voor meer informatie [MFA-telefoon nummer kenmerk](#mfa-phone-number-attribute)<br><sup>3 </sup> Mag niet worden gebruikt met Azure AD B2C

## <a name="mfa-phone-number-attribute"></a>Kenmerk MFA-telefoon nummer

Wanneer u een telefoon gebruikt voor multi-factor Authentication (MFA), wordt de mobiele telefoon gebruikt voor het verifiëren van de identiteit van de gebruiker. Als u een nieuw telefoon nummer wilt [toevoegen](https://docs.microsoft.com/graph/api/authentication-post-phonemethods) via programma code, het telefoon nummer [bijwerken](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update), [ophalen](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)of [verwijderen](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete) , gebruikt u MS Graph API [methode voor telefonische verificatie](https://docs.microsoft.com/graph/api/resources/phoneauthenticationmethod).

In Azure AD B2C [aangepast beleid](custom-policy-overview.md), het telefoon nummer is beschikbaar via `strongAuthenticationPhoneNumber` claim type.

## <a name="extension-attributes"></a>Extensie kenmerken

U moet vaak uw eigen kenmerken maken, zoals in de volgende gevallen:

- Een klant gerichte toepassing moet persistent blijven voor een kenmerk zoals **LoyaltyNumber**.
- Een id-provider heeft een unieke gebruikers-id, zoals **uniqueUserGUID** , die moeten worden opgeslagen.
- Een aangepaste gebruikers traject moet persistent worden gemaakt voor een status van een gebruiker, zoals **migrationStatus**.

Azure AD B2C breidt de set kenmerken uit die zijn opgeslagen op elk gebruikers account. Extensie kenmerken [breiden het schema](/graph/extensibility-overview#schema-extensions) van de gebruikers objecten in de map uit. De extensie kenmerken kunnen alleen worden geregistreerd voor een toepassings object, hoewel ze mogelijk gegevens voor een gebruiker bevatten. Het kenmerk extension is gekoppeld aan de toepassing met de naam B2C-Extensions-app. Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C om gebruikers gegevens op te slaan. U kunt deze toepassing vinden onder Azure Active Directory App-registraties.

> [!NOTE]
> - Er kunnen Maxi maal 100 extensie kenmerken naar elk gebruikers account worden geschreven.
> - Als de B2C-Extensions-App-toepassing wordt verwijderd, worden deze extensie kenmerken verwijderd uit alle gebruikers samen met alle gegevens die ze bevatten.
> - Als een extensie kenmerk wordt verwijderd door de toepassing, wordt het verwijderd uit alle gebruikers accounts en worden de waarden verwijderd.
> - De onderliggende naam van het uitbreidings kenmerk wordt gegenereerd met de notatie ' Extension_ ' + toepassings-ID + ' _ ' en kenmerk naam. Als u bijvoorbeeld een extensie kenmerk LoyaltyNumber maakt en de B2C-uitbrei dingen-app-toepassings-ID 831374b3-bd50-41bf-aa54-263ec9e050fc is, wordt de naam van het onderliggende extensie kenmerk: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. U gebruikt de onderliggende naam wanneer u Graph API query's uitvoert om gebruikers accounts te maken of bij te werken.

De volgende gegevens typen worden ondersteund bij het definiëren van een eigenschap in een schema-uitbrei ding:

|Type eigenschap |Opmerkingen  |
|--------------|---------|
|Booleaans    | Mogelijke waarden: **True** of **False**. |
|DateTime   | Moet worden opgegeven in ISO 8601-indeling. Worden opgeslagen in UTC.   |
|Geheel getal    | 32-bits waarde.               |
|Tekenreeks     | Maxi maal 256 tekens.     |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over extensie kenmerken:
- [Schema-uitbreidingen](/graph/extensibility-overview#schema-extensions)
- [Aangepaste kenmerken definiëren](user-flow-custom-attributes.md)