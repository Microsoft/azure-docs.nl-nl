---
title: Migratie handleiding voor ADAL naar MSAL voor Android | Azure
titleSuffix: Microsoft identity platform
description: Informatie over het migreren van uw Android-app voor Azure Active Directory Authentication Library (ADAL) naar de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: bf9b3a154e19fab08c46f9838f555e223f10e8a0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672284"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Migratie handleiding voor ADAL naar MSAL voor Android

Dit artikel bevat een overzicht van wijzigingen die u moet aanbrengen voor het migreren van een app die gebruikmaakt van de Azure Active Directory Authentication Library (ADAL) om de micro soft Authentication Library (MSAL) te gebruiken.

## <a name="difference-highlights"></a>Verschil hooglichten

ADAL werkt met het eind punt van Azure Active Directory v 1.0. De micro soft Authentication Library (MSAL) werkt met het micro soft Identity-platform, voorheen bekend als het Azure Active Directory v 2.0-eind punt. Het micro soft-identiteits platform wijkt af van Azure Active Directory v 1.0 in dat geval:

Steun
  - Organisatie-identiteit (Azure Active Directory)
  - Niet-organisatie-identiteiten, zoals Outlook.com, Xbox Live, enzovoort
  - (Alleen Azure AD B2C) Federatieve aanmelding met Google, Facebook, Twitter en Amazon

- Is standaarden compatibel met:
  - OAuth v 2.0
  - OpenID Connect Connect (OIDC)

De open bare API MSAL introduceert belang rijke wijzigingen, waaronder:

- Een nieuw model voor het verkrijgen van toegang tot tokens:
  - ADAL biedt toegang tot tokens via de `AuthenticationContext` , die de-server vertegenwoordigt. MSAL biedt toegang tot tokens via de `PublicClientApplication` , die de-client vertegenwoordigt. Client ontwikkelaars hoeven geen nieuw exemplaar te maken `PublicClientApplication` voor elke instantie die ze nodig hebben om te communiceren. Er is slechts één `PublicClientApplication` configuratie vereist.
  - Ondersteuning voor het aanvragen van toegangs tokens met behulp van scopes naast resource-id's.
  - Ondersteuning voor incrementele toestemming. Ontwikkel aars kunnen bereiken aanvragen wanneer de gebruiker meer en meer functionaliteit in de app opent, inclusief de functies die niet zijn opgenomen tijdens de registratie van de app.
  - Instanties worden niet meer gevalideerd tijdens runtime. In plaats daarvan declareert de ontwikkelaar tijdens de ontwikkeling een lijst met bekende instanties.
- Wijzigingen in token-API:
  - In ADAL `AcquireToken()` maakt u eerst een Silent-aanvraag. Als dat niet het geval is, wordt er een interactieve aanvraag gemaakt. Dit gedrag heeft tot gevolg dat sommige ontwikkel aars alleen vertrouwen op `AcquireToken` , waardoor de gebruiker onverwacht om referenties wordt gevraagd op tijdstippen. MSAL vereist dat ontwikkel aars opzettelijk zijn wanneer de gebruiker een prompt van de gebruikers interface ontvangt.
    - `AcquireTokenSilent` resulteert altijd in een Silent-aanvraag die slaagt of mislukt.
    - `AcquireToken` resulteert altijd in een aanvraag die de gebruiker via de gebruikers interface vraagt.
- MSAL ondersteunt aanmelden vanuit een standaard browser of een Inge sloten webweergave:
  - Standaard wordt de standaard browser op het apparaat gebruikt. Hiermee kan MSAL verificatie status (cookies) gebruiken die mogelijk al aanwezig is voor een of meer accounts die zijn aangemeld. Als er geen authenticatie status aanwezig is, wordt verificatie tijdens autorisatie via MSAL tot gevolg dat er voor het voor deel van andere webtoepassingen die in dezelfde browser zullen worden gebruikt, authenticatie status (cookies) wordt gemaakt.
- Nieuw uitzonderings model:
  - Uitzonde ringen definiëren duidelijk het type fout dat is opgetreden en wat de ontwikkelaar nodig heeft om het probleem op te lossen.
- MSAL ondersteunt parameter objecten voor `AcquireToken` en- `AcquireTokenSilent` aanroepen.
- MSAL ondersteunt declaratieve configuratie voor:
  - Client-ID, omleidings-URI.
  - Inge sloten versus standaard browser
  - Ca's
  - HTTP-instellingen, zoals lees-en verbindingstime-out

## <a name="your-app-registration-and-migration-to-msal"></a>Uw app-registratie en-migratie naar MSAL

U hoeft uw bestaande app-registratie niet te wijzigen om MSAL te gebruiken. Als u wilt profiteren van incrementele/progressieve toestemming, moet u mogelijk de registratie controleren om de specifieke bereiken te identificeren die u incrementeel wilt aanvragen. Meer informatie over bereiken en stapsgewijze toestemming volgt.

In de registratie van uw app in de portal ziet u een tabblad **API-machtigingen** . Dit geeft een lijst van de Api's en machtigingen (bereiken) die uw app momenteel heeft geconfigureerd om toegang aan te vragen. Er wordt ook een lijst met de bereik namen weer gegeven die zijn gekoppeld aan elke API-machtiging.

### <a name="user-consent"></a>Gebruikerstoestemming

Met ADAL en het Azure AD v1-eind punt, werd gebruikers toestemming verleend voor bronnen waarvan ze eigenaar zijn, gekregen bij het eerste gebruik. Met MSAL en het micro soft-identiteits platform kan toestemming worden gevraagd incrementeel. Incrementele toestemming is handig voor machtigingen die een gebruiker kan beschouwen als hoge bevoegdheid of anders kan worden gevraagd als er geen duidelijke uitleg is over waarom de machtiging is vereist. In ADAL is het mogelijk dat deze machtigingen hebben geresulteerd in de gebruiker bij het aanmelden bij uw app.

> [!TIP]
> We raden u aan om incrementele toestemming te gebruiken in scenario's waarin u aanvullende context moet opgeven voor uw gebruiker over waarom uw app toestemming nodig heeft.

### <a name="admin-consent"></a>toestemming van de beheerder

Beheerders van organisaties kunnen toestemming geven aan machtigingen die uw toepassing vereist namens alle leden van hun organisatie. Sommige organisaties kunnen beheerders alleen toestemming geven voor toepassingen. Toestemming van de beheerder vereist dat u alle API-machtigingen en scopes die door uw toepassing worden gebruikt, opneemt in de registratie van uw app.

> [!TIP]
> Hoewel u een bereik kunt aanvragen met behulp van MSAL voor iets dat niet is opgenomen in de app-registratie, wordt u aangeraden om de registratie van uw app bij te werken zodat alle resources en bereiken worden opgenomen waaraan een gebruiker ooit toestemming kan verlenen.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migreren van resource-Id's naar bereiken

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Verificatie en autorisatie aanvragen voor alle machtigingen bij het eerste gebruik

Als u momenteel ADAL gebruikt en niet incrementele toestemming hoeft te gebruiken, is de eenvoudigste manier om MSAL te gaan gebruiken om een aanvraag te maken `acquireToken` met behulp van het nieuwe `AcquireTokenParameter` object en de waarde voor de resource-id in te stellen.

> [!CAUTION]
> Het is niet mogelijk om beide bereiken en een resource-id in te stellen. Als u beide probeert in te stellen, resulteert dit in een `IllegalArgumentException` .

Dit leidt tot hetzelfde v1-gedrag dat u gebruikt. Alle machtigingen die in de app-registratie zijn aangevraagd, worden door de gebruiker aangevraagd tijdens de eerste interactie.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Machtigingen alleen verifiëren en aanvragen indien nodig

Als u de incrementele toestemming wilt benutten, maakt u een lijst met machtigingen (bereiken) die door uw app worden gebruikt vanuit de registratie van uw app en organiseert u deze in twee lijsten op basis van:

- De bereiken die u wilt aanvragen tijdens de eerste interactie van de gebruiker met uw app tijdens het aanmelden.
- De machtigingen die zijn gekoppeld aan een belang rijke functie van uw app, die u ook moet uitleggen aan de gebruiker.

Wanneer u de bereiken hebt georganiseerd, organiseert u elke lijst op basis waarvan de resource (API) waarvoor u een token wilt aanvragen. Evenals andere bereiken die de gebruiker op hetzelfde moment moet autoriseren.

Het object para meters waarmee uw aanvraag voor MSAL wordt ondersteund:

- `Scope`: De lijst met bereiken waarvoor u een autorisatie wilt aanvragen en een toegangs token wilt ontvangen.
- `ExtraScopesToConsent`: Een extra lijst met bereiken waarvoor u een autorisatie wilt aanvragen, terwijl u een toegangs token voor een andere bron aanvraagt. Met deze lijst met bereiken kunt u het aantal keren dat u de gebruikers autorisatie wilt aanvragen, minimaliseren. Dit betekent dat er minder gebruikers autorisatie of toestemming wordt gevraagd.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migreren van AuthenticationContext naar PublicClientApplications

### <a name="constructing-publicclientapplication"></a>PublicClientApplication maken

Wanneer u MSAL gebruikt, maakt u een exemplaar van `PublicClientApplication` . Dit object modelleert uw app-identiteit en wordt gebruikt om aanvragen te doen voor een of meer instanties. Met dit object configureert u uw client identiteit, omleidings-URI, standaard instantie, of u de browser van de apparaten versus de Inge sloten webweergave, het logboek niveau en meer wilt gebruiken.

U kunt dit object declaratief configureren met JSON, dat u opgeeft als een bestand of een archief als resource in uw APK.

Hoewel dit object geen Singleton is, wordt intern gebruikt `Executors` voor zowel interactieve als Silent-aanvragen.

### <a name="business-to-business"></a>Bedrijf naar bedrijf

In ADAL moet elke organisatie waarvoor u toegangs tokens aanvraagt een afzonderlijk exemplaar van de gebruiken `AuthenticationContext` . In MSAL is dit geen vereiste. U kunt de instantie opgeven van waaruit u een token wilt aanvragen als onderdeel van uw Silent of interactieve aanvraag.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migreren van certificerings instanties naar bekende instanties

MSAL heeft geen vlag waarmee de validatie van de certificerings instantie wordt in-of uitgeschakeld. Verificatie van de certificerings instantie is een functie in ADAL, en in de vroege releases van MSAL, waardoor uw code geen tokens van een mogelijk schadelijke autoriteit kan aanvragen. MSAL haalt nu een lijst op met de instanties die bekend zijn bij micro soft en voegt deze lijst samen met de instanties die u hebt opgegeven in uw configuratie.

> [!TIP]
> Als u een Azure Business to consumer (B2C)-gebruiker bent, betekent dit dat u de verificatie van de certificerings instantie niet meer hoeft uit te scha kelen. Neem in plaats daarvan al het ondersteunde Azure AD B2C beleid op als instanties in uw MSAL-configuratie.

Als u een instantie probeert te gebruiken die niet bekend is bij micro soft en niet is opgenomen in uw configuratie, krijgt u een `UnknownAuthorityException` .

### <a name="logging"></a>Logboekregistratie
U kunt logboek registratie nu als onderdeel van uw configuratie declaratief configureren, zoals:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migreren van user info naar account

In ADAL biedt het `AuthenticationResult` een `UserInfo` object dat wordt gebruikt voor het ophalen van informatie over het geverifieerde account. De term ' gebruiker ', die bedoeld is voor een mens of software-agent, werd toegepast op een manier waardoor het moeilijk is te communiceren dat sommige apps ondersteuning bieden voor één gebruiker (of een mede werker of software-agent) met meerdere accounts.

Overweeg een bank rekening. Mogelijk hebt u meer dan één account bij meer dan één financiële instelling. Wanneer u een account opent, worden aan u (de gebruiker) referenties verleend, zoals een ATM-kaart & pincode, die worden gebruikt voor toegang tot uw saldo, factuur betaling, enzovoort voor elk account. Deze referenties kunnen alleen worden gebruikt bij de financiële instelling die ze heeft uitgegeven.

Op vergelijk bare wijze, zoals accounts bij een financiële instelling, worden accounts in het micro soft-identiteits platform gebruikt met behulp van referenties. Deze referenties zijn geregistreerd bij, of uitgegeven door, micro soft. Of door micro soft namens een organisatie.

Als het micro soft Identity-platform verschilt van een financiële instelling, in dit analoog, is dat het micro soft Identity-platform een Framework biedt waarmee een gebruiker één account en de bijbehorende referenties kan gebruiken om toegang te krijgen tot resources die deel uitmaken van meerdere personen en organisaties. Zo kunt u een kaart gebruiken die is uitgegeven door een bank, maar ook een andere financiële instelling. Dit werkt omdat alle organisaties in kwestie gebruikmaken van het micro soft Identity-platform, zodat één account kan worden gebruikt in meerdere organisaties. Hier volgt een voorbeeld:

Sam werkt voor Contoso.com maar beheert virtuele Azure-machines die deel uitmaken van Fabrikam.com. Voor Sam voor het beheren van de virtuele machines van Fabrikam moet hij gemachtigd zijn om deze te openen. Deze toegang kan worden verleend door Sam-account toe te voegen aan Fabrikam.com en zijn account een rol te geven waarmee hij met de virtuele machines kan werken. Dit wordt gedaan met de Azure Portal.

Het toevoegen van het Contoso.com-account van Sam als lid van Fabrikam.com zou leiden tot het maken van een nieuwe record in fabrikam. com-Azure Active Directory voor Sam. De record van Sam in Azure Active Directory wordt een gebruikers object genoemd. In dit geval wijst dat gebruikers object terug naar het gebruikers object van Sam in Contoso.com. Het fabrikam-gebruikers object van Sam is de lokale weer gave van Sam, en wordt gebruikt voor het opslaan van informatie over het account dat is gekoppeld aan Sam in de context van Fabrikam.com. In Contoso.com is de titel van Sam Senior DevOps consultant. In Fabrikam is de titel van de SAM Contractor-Virtual machines. In Contoso.com is Sam niet verantwoordelijk voor het beheer van virtuele machines. In Fabrikam.com is dat zijn enige taak functie. Sam heeft nog steeds slechts één set referenties om bij te houden. Dit zijn de referenties die zijn uitgegeven door Contoso.com.

Zodra een geslaagde `acquireToken` aanroep is uitgevoerd, ziet u een verwijzing naar een `IAccount` object dat in latere aanvragen kan worden gebruikt `acquireTokenSilent` .

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Als u een app hebt die toegang heeft tot claims over een account van elk van de tenants waarin het account wordt weer gegeven, kunt u `IAccount` objecten casten naar `IMultiTenantAccount` . Deze interface biedt een kaart van `ITenantProfiles` , op basis van de Tenant-id, waarmee u toegang krijgt tot de claims die bij het account horen in elk van de tenants waarvan u een token hebt aangevraagd ten opzichte van het huidige account.

De claims in de hoofdmap van `IAccount` en `IMultiTenantAccount` bevatten altijd de claims van de thuis Tenant. Als u nog geen aanvraag voor een token in de thuis Tenant hebt gemaakt, is deze verzameling leeg.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="use-the-new-authenticationcallback"></a>De nieuwe AuthenticationCallback gebruiken

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migreren naar de nieuwe uitzonde ringen

In ADAL is er één uitzonderings type, `AuthenticationException` met een methode voor het ophalen van de `ADALError` Enum-waarde.
In MSAL is er een hiërarchie met uitzonde ringen en elke groep heeft een eigen set gerelateerde specifieke fout codes.

| Uitzondering                                        | Beschrijving                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Deze wordt gegenereerd als een of meer invoer argumenten ongeldig zijn.                 |
| `MsalClientException`                            | Wordt gegenereerd als de fout aan de kant van de client is.                                 |
| `MsalDeclinedScopeException`                     | Wordt gegenereerd als een of meer aangevraagde bereiken door de server zijn geweigerd. |
| `MsalException`                                  | Standaard ingeschakelde uitzonde ring veroorzaakt door MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Dit wordt gegenereerd als het beveiligings beleid van de resource is ingeschakeld voor de bron.         |
| `MsalServiceException`                           | Wordt gegenereerd als de fout aan de server zijde is.                                 |
| `MsalUiRequiredException`                        | Wordt gegenereerd als het token niet op de achtergrond kan worden vernieuwd.                    |
| `MsalUserCancelException`                        | Wordt gegenereerd als de gebruiker de verificatie stroom heeft geannuleerd.                |

### <a name="adalerror-to-msalexception-translation"></a>ADALError naar MsalException-vertaling

| Als u deze fouten ondervindt in ADAL...  | ... Deze MSAL-uitzonde ringen worden onderschept:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Geen equivalente ADALError* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Geen equivalente ADALError* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Geen equivalente ADALError* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Geen equivalente ADALError* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>Logboek registratie voor MSAL-logboek registratie ADAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
