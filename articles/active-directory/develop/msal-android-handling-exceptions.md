---
title: Fouten en uitzonde ringen (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, voorwaardelijke toegang en claim problemen in MSAL Android-toepassingen.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761383"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Uitzonde ringen en fouten in MSAL voor Android afhandelen

Uitzonde ringen in de micro soft Authentication Library (MSAL) zijn bedoeld om app-ontwikkel aars te helpen bij het oplossen van hun toepassing. Uitzonderings berichten zijn niet gelokaliseerd.

Bij het verwerken van uitzonde ringen en fouten kunt u het uitzonderings type zelf en de fout code gebruiken om onderscheid te maken tussen uitzonde ringen.  Zie [verificatie-en autorisatie fout codes](reference-aadsts-error-codes.md)voor een lijst met fout codes.

Tijdens de aanmeldings procedure kunnen er fouten optreden met betrekking tot toestemmingen, voorwaardelijke toegang (MFA, Apparaatbeheer, op locatie gebaseerde beperkingen), het uitgeven en inwisselen van tokens en gebruikers eigenschappen.


|Fout klasse | Oorzaak/fout teken reeks| Omgaan met |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Het vernieuwings token dat wordt gebruikt voor het inwisselen van het toegangs token is ongeldig, verlopen of ingetrokken. Deze uitzonde ring kan zijn veroorzaakt door een wachtwoord wijziging. </li><li>`NO_TOKENS_FOUND`: Het toegangs token bestaat niet en er kan geen vernieuwings token worden gevonden voor het inwisselen van het toegangs token.</li> <li>Stap-omhoog vereist<ul><li>MFA</li><li>Ontbrekende claims</li></ul></li><li>Geblokkeerd door voorwaardelijke toegang (bijvoorbeeld installatie van [verificatie Broker](./msal-android-single-sign-on.md) vereist)</li><li>`NO_ACCOUNT_FOUND`: Er is geen account beschikbaar in de cache voor Silent Authentication.</li></ul> |Bel `acquireToken()` de gebruiker vragen om de gebruikers naam en het wacht woord in te voeren, en mogelijk toestemming te geven en multi factor Authentication uit te voeren.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: De gebruiker of server heeft niet alle bereiken geaccepteerd. De server kan een bereik afwijzen als het aangevraagde bereik niet wordt ondersteund, niet wordt herkend of niet wordt ondersteund voor een bepaald account. </li></ul>| De ontwikkelaar moet bepalen of u wilt door gaan met de verificatie met de verleende bereiken of het verificatie proces te beëindigen. Optie om de aanvraag voor het verkrijgen van tokens alleen opnieuw in te dienen voor de toegekende bereiken en om hints te bieden waarvoor machtigingen zijn verleend door het door geven `silentParametersForGrantedScopes` en aanroepen `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: In deze aanvraag ontbreekt een vereiste para meter, bevat een ongeldige para meter, bevat een para meter van meer dan een keer of is anders misvormd. </li><li>`SERVICE_NOT_AVAILABLE`: Geeft 500/503/506-fout codes aan omdat de service niet actief is. </li><li>`UNAUTHORIZED_REQUEST`: De client is niet gemachtigd om een autorisatie code aan te vragen.</li><li>`ACCESS_DENIED`: De resource-eigenaar of autorisatie server heeft de aanvraag geweigerd.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` validatie mislukt</li><li>`UNKNOWN_ERROR`: De aanvraag voor de server is mislukt, maar er is een fout opgetreden en `error_description` deze wordt geretourneerd door de service.</li><ul>| Deze uitzonderings klasse duidt op fouten bij het communiceren met de service, maar kan afkomstig zijn van de toestemming of Token-eind punten. MSAL leest de fout en error_description van de reactie van de server. Over het algemeen worden deze fouten opgelost door app-configuraties te herstellen in code of in de app-registratie Portal. Zelden een service-onderbreking kan deze waarschuwing activeren. Dit kan alleen worden verholpen door te wachten tot de service is hersteld.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Er zijn meerdere cache vermeldingen gevonden en de SDK kan het juiste toegangs-of vernieuwings token niet identificeren uit de cache. Deze uitzonde ring duidt doorgaans op een fout in de SDK voor het opslaan van tokens of de instantie is niet beschikbaar in de Silent-aanvraag en er zijn meerdere overeenkomende tokens gevonden. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Er is geen actief netwerk beschikbaar op het apparaat. </li><li>`JSON_PARSE_FAILURE`: Het parseren van de JSON-indeling door de SDK is mislukt.</li><li>`IO_ERROR`: `IOException` Er is een fout opgetreden bij het maken van een apparaat of het netwerk. </li><li>`MALFORMED_URL`: De URL heeft een ongeldige indeling. Waarschijnlijk veroorzaakt bij het maken van de verificatie aanvraag, de instantie of de omleidings-URI. </li><li>`UNSUPPORTED_ENCODING`: De versleuteling wordt niet ondersteund door het apparaat. </li><li>`NO_SUCH_ALGORITHM`: Het algoritme dat wordt gebruikt om de [PKCE](https://tools.ietf.org/html/rfc7636) -uitdaging te genereren, wordt niet ondersteund. </li><li>`INVALID_JWT`: `JWT` geretourneerd door de server is ongeldig of is leeg of ongeldig. </li><li>`STATE_MISMATCH`: Status van autorisatie antwoord komt niet overeen met de status in de autorisatie aanvraag. Voor autorisatie aanvragen verifieert de SDK de status die is geretourneerd door de omleiding en de versie die in de aanvraag is verzonden. </li><li>`UNSUPPORTED_URL`: Niet-ondersteunde URL, kan validatie van ADFS-autoriteit niet uitvoeren. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: De autoriteit wordt niet ondersteund voor validatie van de certificerings instantie. De SDK ondersteunt B2C-instanties, maar biedt geen ondersteuning voor B2C-certificerings validatie. Alleen bekende hosts worden ondersteund. </li><li>`CHROME_NOT_INSTALLED`: Chrome is niet geïnstalleerd op het apparaat. De SDK gebruikt het Chrome aangepaste tabblad voor autorisatie aanvragen, indien beschikbaar, en gaat terug naar de Chrome-browser. </li><li>`USER_MISMATCH`: De gebruiker die is opgegeven in de aanvraag voor het ophalen van tokens komt niet overeen met de gebruiker die is geretourneerd door de server.</li></ul>|Deze uitzonderings klasse vertegenwoordigt algemene fouten die lokaal zijn voor de-bibliotheek. Deze uitzonde ringen kunnen worden verwerkt door de aanvraag te corrigeren.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: De door de gebruiker geïnitieerde interactieve stroom en voorafgaand aan het ontvangen van de tokens terug ze heeft de aanvraag geannuleerd. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Er moet een instantie worden opgegeven voor `acquireTokenSilent` .</li></ul>|Deze fouten kunnen worden verholpen door de ontwikkelaar om argumenten te corrigeren en te zorgen voor interactieve authenticatie, voltooiings retour aanroep, bereiken en een account met een geldige ID.|


## <a name="catching-errors"></a>Fouten waarnemen

Het volgende code fragment toont een voor beeld van het opvangen van fouten in het geval van Silent- `acquireToken` aanroepen.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [logboek registratie in MSAL voor Android](msal-logging-android.md).