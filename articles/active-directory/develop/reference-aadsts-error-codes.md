---
title: Fout codes voor verificatie van Azure AD-&
description: Meer informatie over de AADSTS-fout codes die worden geretourneerd door de Azure AD Security Token Service (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 02/01/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b948de6ad9e07a650df98ef38104c02462ab532d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428028"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Verificatie- en autorisatiefoutcodes in Azure AD

Zoeken naar informatie over de AADSTS-fout codes die worden geretourneerd door de Azure Active Directory (de Azure AD) Security Token Service (STS)? Lees dit document om AADSTS-fout beschrijvingen, fixes en enkele voorgestelde tijdelijke oplossingen te vinden.

> [!NOTE]
> Deze informatie is voorlopig en kan worden gewijzigd. Hebt u een vraag of kunt u niet vinden wat u zoekt? Maak een GitHub-probleem of Raadpleeg de [ondersteunings-en Help-opties voor ontwikkel aars](./developer-support-help-options.md) voor meer informatie over andere manieren om hulp en ondersteuning te krijgen.
>
> Deze documentatie wordt verstrekt voor ontwikkel aars-en beheer richtlijnen, maar mag niet worden gebruikt door de client zelf. Fout codes kunnen op elk gewenst moment worden gewijzigd, zodat er meer gedetailleerde fout berichten worden weer geven die zijn bedoeld om de ontwikkelaar te helpen bij het bouwen van de toepassing. Apps die afhankelijk zijn van tekst of fout code nummers, worden na verloop van tijd gesplitst.

## <a name="handling-error-codes-in-your-application"></a>Fout codes in uw toepassing verwerken

De [OAuth 2.0 spec](https://tools.ietf.org/html/rfc6749#section-5.2) biedt richt lijnen voor het afhandelen van fouten tijdens de verificatie met behulp `error` van het deel van het fout bericht. 

Hier volgt een voor beeld van een fout bericht:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parameter         | Beschrijving    |
|-------------------|----------------|
| `error`       | Een teken reeks voor fout code die kan worden gebruikt voor het classificeren van typen fouten die optreden en die moeten worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. Gebruik dit veld nooit om te reageren op een fout in de code. |
| `error_codes` | Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren.  |
| `timestamp`   | Het tijdstip waarop de fout is opgetreden. |
| `trace_id`    | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |
| `error_uri` |  Een koppeling naar de zoek pagina voor fouten met aanvullende informatie over de fout.  Dit geldt alleen voor het gebruik van ontwikkel aars, maar niet voor gebruikers.  Alleen aanwezig als het systeem voor het opzoeken van fouten aanvullende informatie over de fout bevat. er is geen aanvullende informatie beschikbaar.|

Het `error` veld heeft verschillende mogelijke waarden: Controleer de koppelingen van de protocol documentatie en de OAuth 2,0-specificaties voor meer informatie over specifieke fouten (bijvoorbeeld `authorization_pending` in de [code stroom](v2-oauth2-device-code.md)van het apparaat) en hoe u deze kunt reageren.  Hier vindt u enkele veelvoorkomende items:

| Foutcode         | Beschrijving        | Client actie    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protocol fout, zoals een ontbrekende vereiste para meter. | Corrigeer en verzend de aanvraag opnieuw.|
| `invalid_grant`    | Sommige van de verificatie materiaal (auth-code, vernieuwings token, toegangs token, PKCE Challenge) is ongeldig, niet-geparseerd, ontbreekt of anderszins onbruikbaar | Probeer een nieuwe aanvraag naar het `/authorize` eind punt uit om een nieuwe autorisatie code op te halen.  Overweeg het gebruik van de protocollen te controleren en te valideren. |
| `unauthorized_client` | De geverifieerde client is niet gemachtigd om dit type autorisatie toekenning te gebruiken. | Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `invalid_client` | Client verificatie is mislukt.  | De referenties van de client zijn niet geldig. De toepassings beheerder werkt de referenties bij om deze te herstellen.   |
| `unsupported_grant_type` | De autorisatie server biedt geen ondersteuning voor het type autorisatie toekenning. | Wijzig het toekennings type in de aanvraag. Dit type fout moet alleen tijdens de ontwikkeling optreden en tijdens de eerste test worden gedetecteerd. |
| `invalid_resource` | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. | Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD.  Tijdens de ontwikkeling duidt dit meestal op een onjuiste installatie Tenant of een intypen in de naam van de scope die wordt aangevraagd. |
| `interaction_required` | De aanvraag vereist een gebruikers interactie. Zo is een extra verificatie stap vereist. | Voer de aanvraag opnieuw uit met dezelfde bron, zodat de gebruiker de benodigde uitdagingen kan volt ooien.  |
| `temporarily_unavailable` | De server is tijdelijk niet actief om de aanvraag af te handelen. | Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |

## <a name="lookup-current-error-code-information"></a>Informatie over actuele fout code opzoeken
Fout codes en berichten zijn onderhevig aan wijzigingen.  Voor de meest recente informatie raadpleegt [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) u de pagina om AADSTS-fout beschrijvingen, fixes en enkele voorgestelde tijdelijke oplossingen te vinden.  

Als u bijvoorbeeld de fout code ' AADSTS50058 ' hebt ontvangen, voert u een zoek opdracht uit [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) voor ' 50058 '.  U kunt ook rechtstreeks een koppeling naar een specifieke fout maken door het fout code nummer toe te voegen aan de URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>AADSTS-fout codes

| Fout | Beschrijving |
|---|---|
| AADSTS16000 | SelectUserAccount: dit is een interrupt veroorzaakt door Azure AD, wat resulteert in de gebruikers interface, waarmee de gebruiker kan kiezen uit meerdere geldige SSO-sessies. Deze fout is tamelijk gebruikelijk en kan worden geretourneerd naar de toepassing als deze `prompt=none` is opgegeven. |
| AADSTS16001 | UserAccountSelectionInvalid: deze fout wordt weer geven als de gebruiker op een tegel klikt die de logica voor het selecteren van de sessie heeft geweigerd. Als u deze fout hebt geactiveerd, kan de gebruiker met behulp van een bijgewerkte lijst met tegels/sessies herstellen door een ander account te kiezen. Deze fout kan optreden vanwege een code defect of een race voorwaarde. |
| AADSTS16002 | AppSessionSelectionInvalid-er is niet voldaan aan de vereiste voor de app-opgegeven SID.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant: geeft aan dat de gebruiker niet expliciet aan de Tenant is toegevoegd. |
| AADSTS17003 | CredentialKeyProvisioningFailed-Azure AD kan de gebruikers sleutel niet inrichten. |
| AADSTS20001 | WsFedSignInResponseError: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS20012 | WsFedMessageInvalid: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS20033 | FedMetadataInvalidTenantName: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40008 | OAuth2IdPUnretryableServerError: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40010 | OAuth2IdPRetryableServerError: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError: er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS50000 | TokenIssuanceError: er is een probleem met de aanmeldings service. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om dit probleem op te lossen. |
| AADSTS50001 | InvalidResource: de resource is uitgeschakeld of bestaat niet. Controleer de code van de app om te controleren of u de exacte resource-URL hebt opgegeven voor de resource die u wilt openen.  |
| AADSTS50002 | NotAllowedTenant-aanmelden is mislukt vanwege een beperkte proxy toegang op de Tenant. Als het uw eigen tenantbeleid is, kunt u de instellingen voor de beperkte tenant wijzigen om dit probleem op te lossen. |
| AADSTS500021 | De toegang tot de Tenant {Tenant} is geweigerd. AADSTS500021 geeft aan dat de functie voor Tenant beperking is geconfigureerd en dat de gebruiker toegang probeert te krijgen tot een Tenant die niet voor komt in de lijst met toegestane tenants die in de header is opgegeven `Restrict-Access-To-Tenant` . Zie [Tenant beperkingen gebruiken voor het beheren van toegang tot SaaS-Cloud toepassingen](/azure/active-directory/manage-apps/tenant-restrictions)voor meer informatie.|
| AADSTS50003 | MissingSigningKey-aanmelden is mislukt vanwege een ontbrekende handtekening sleutel of certificaat. Dit kan zijn omdat er geen handtekening sleutel is geconfigureerd in de app. Bekijk de oplossingen die worden beschreven op [.. /Manage-apps/Application-Sign-in-problem-Federated-SSO-Gallery.MD # certificaat-of-sleutel-niet geconfigureerd](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured). Als u nog steeds problemen ziet, neemt u contact op met de eigenaar van de app of een app-beheerder. |
| AADSTS50005 | DevicePolicyError: gebruiker heeft geprobeerd zich aan te melden bij een apparaat vanaf een platform dat momenteel niet wordt ondersteund via het beleid voor voorwaardelijke toegang. |
| AADSTS50006 | InvalidSignature-handtekening verificatie is mislukt vanwege een ongeldige hand tekening. |
| AADSTS50007 | PartnerEncryptionCertificateMissing-het partner versleutelings certificaat is niet gevonden voor deze app. [Open een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) bij micro soft om dit probleem te verhelpen. |
| AADSTS50008 | InvalidSamlToken: de SAML-verklaring ontbreekt of is onjuist geconfigureerd in het token. Neem contact op met uw federatieprovider. |
| AADSTS50010 | De validatie van de AudienceUriValidationFailed-doel groep voor de app is mislukt omdat er geen token doelgroepen zijn geconfigureerd. |
| AADSTS50011 | InvalidReplyTo: het antwoord adres ontbreekt, is onjuist geconfigureerd of komt niet overeen met de antwoord adressen die voor de app zijn geconfigureerd.  Als oplossing zorgt u ervoor dat dit ontbrekende antwoord adres wordt toegevoegd aan de Azure Active Directory-toepassing of iemand met de machtigingen voor het beheren van uw toepassing in Active Directory dit voor u doen.|
| AADSTS50012 | AuthenticationFailed-verificatie is om een van de volgende redenen mislukt:<ul><li>De onderwerpnaam van het handtekening certificaat is niet geautoriseerd</li><li>Er is geen overeenkomend beleid voor vertrouwde instanties gevonden voor de naam van de geautoriseerde houder</li><li>De certificaat keten is ongeldig</li><li>Het handtekening certificaat is niet geldig</li><li>Het beleid is niet geconfigureerd op de Tenant</li><li>Vinger afdruk van het handtekening certificaat is niet geautoriseerd</li><li>Client bevestiging bevat een ongeldige hand tekening</li></ul> |
| AADSTS50013 | InvalidAssertion: de bevestiging is ongeldig vanwege verschillende redenen: de uitgever van het token komt niet overeen met de API-versie binnen het geldige tijds bereik-verlopen. het token voor het vernieuwen in de bevestiging is geen primair vernieuwings token. |
| AADSTS50014 | GuestUserInPendingState: de inwisseling van de gebruiker heeft de status in behandeling. Het gast gebruikers account is nog niet volledig gemaakt. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState: de gebruiker moet een juridische leeftijds groep toestemming geven. |
| AADSTS50017 | CertificateValidationFailed-certificerings validatie mislukt, redenen om de volgende redenen:<ul><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Verwacht CrlSegment kan niet worden gevonden</li><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Delta CRL-distributiepunt is geconfigureerd zonder een bijbehorend CRL-distributiepunt</li><li>Kan geen geldige CRL-segmenten ophalen vanwege een time-outprobleem</li><li>Kan CRL niet downloaden</li></ul>Neem contact op met de beheerder van de tenant. |
| AADSTS50020 | UserUnauthorized: gebruikers zijn niet gemachtigd om dit eind punt aan te roepen. |
| AADSTS50027 | InvalidJwtToken-ongeldig JWT-token om de volgende redenen:<ul><li>bevat geen nonceclaim, subclaim</li><li>onderwerp-id komt niet overeen</li><li>duplicaatclaim in idToken-claims</li><li>onverwachte certificaatverlener</li><li>onverwachte doelgroep</li><li>niet binnen geldig tijdsbereik </li><li>indeling van het token is niet juist</li><li>Handtekeningverificatie van extern ID-token van de verlener is mislukt.</li></ul> |
| AADSTS50029 | Ongeldige URI: domeinnaam bevat ongeldige tekens. Neem contact op met de beheerder van de tenant. |
| AADSTS50032 | WeakRsaKey: geeft aan dat de foutieve gebruiker probeert een zwakke RSA-sleutel te gebruiken. |
| AADSTS50033 | RetryableError: geeft aan dat er een tijdelijke fout is die niet aan de database bewerkingen is gerelateerd. |
| AADSTS50034 | UserAccountNotFound: als u zich wilt aanmelden bij deze toepassing, moet het account worden toegevoegd aan de Directory. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-het zout dat vereist is voor het genereren van een Pairwise Identifier ontbreekt in principe. Neem contact op met de beheerder van de tenant. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer-komt niet overeen met de uitgever van het certificaat in de client bevestiging. Neem contact op met de beheerder van de tenant. |
| AADSTS50049 | NoSuchInstanceForDiscovery-onbekend of ongeldig exemplaar. |
| AADSTS50050 | MalformedDiscoveryRequest-de aanvraag heeft een ongeldige indeling. |
| AADSTS50053 | IdsLocked: het account is vergrendeld omdat de gebruiker te vaak heeft geprobeerd zich aan te melden met een onjuiste gebruikers-ID of een onjuist wacht woord. |
| AADSTS50055 | InvalidPasswordExpiredPassword-het wacht woord is verlopen. |
| AADSTS50056 | Ongeldig of leeg wacht woord: het wacht woord bestaat niet in het Archief voor deze gebruiker. |
| AADSTS50057 | UserDisabled-het gebruikers account is uitgeschakeld. Het account is uitgeschakeld door een gebruiker met beheerdersrechten. |
| AADSTS50058 | UserInformationNotProvided: Dit betekent dat een gebruiker niet is aangemeld. Dit is een veelvoorkomende fout die wordt verwacht wanneer een gebruiker niet is geverifieerd en nog niet is aangemeld.</br>Als deze fout wordt aangemoedigd in een SSO-context waarin de gebruiker zich eerder heeft aangemeld, betekent dit dat de SSO-sessie niet is gevonden of ongeldig is.</br>Deze fout kan worden geretourneerd naar de toepassing als prompt = none is opgegeven. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided: door Tenant geïdentificeerde gegevens zijn niet gevonden in de aanvraag of geïmpliceerd door de verstrekte referenties. De gebruiker kan contact opnemen met de Tenant beheerder om het probleem op te lossen. |
| AADSTS50061 | SignoutInvalidRequest-de afmeldings aanvraag is ongeldig. |
| AADSTS50064 | CredentialAuthenticationError: referentie validatie voor gebruikers naam of wacht woord is mislukt. |
| AADSTS50068 | SignoutInitiatorNotParticipant-afmelden is mislukt. De app die zich heeft afgemeld, is geen deel nemer van de huidige sessie. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-afmelden is mislukt. De aanvraag voor het afmelden heeft een naam-id opgegeven die niet overeenkomt met de bestaande sessie (s). |
| AADSTS50071 | SignoutMessageExpired-de afmeldings aanvraag is verlopen. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt: de gebruiker moet zich inschrijven voor de tweede factor Authentication (interactief). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt: sterke verificatie is vereist en de gebruiker heeft de MFA-Challenge niet door gegeven. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired: als gevolg van een configuratie wijziging die door de beheerder is aangebracht, of omdat u naar een nieuwe locatie hebt verplaatst, moet de gebruiker multi-factor Authentication gebruiken om toegang te krijgen tot de resource. Probeer het opnieuw met een nieuwe aanvraag voor het machtigen van de resource. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired: als gevolg van een configuratie wijziging die door de beheerder is aangebracht, of omdat de gebruiker is verplaatst naar een nieuwe locatie, is de gebruiker verplicht om multi-factor Authentication te gebruiken. |
| AADSTS50085 | Vernieuwingstoken vereist een sociale IDP-aanmelding. Laat gebruikers proberen zich opnieuw aan te melden met gebruikersnaam en wachtwoord |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError-de service is tijdelijk niet beschikbaar. Probeer het opnieuw. |
| AADSTS50089 | Stroomtoken verlopen, verificatie is mislukt. Laat de gebruiker opnieuw proberen zich aan te melden met gebruikers naam en wacht woord. |
| AADSTS50097 | DeviceAuthenticationRequired: de verificatie van het apparaat is vereist. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized-de JWT-hand tekening is ongeldig. |
| AADSTS50105 | EntitlementGrantsNotFound-de aangemelde gebruiker is niet toegewezen aan een rol voor de aangemelde app. Wijs de gebruiker toe aan de app. Meer informatie:[.. /Manage-apps/Application-Sign-in-problem-Federated-SSO-Gallery.MD # gebruiker-not-assigned-a-Role](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri-het aangevraagde Federatie realm-object bestaat niet. Neem contact op met de beheerder van de tenant. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat: probleem met JWT-header. Neem contact op met de beheerder van de tenant. |
| AADSTS50124 | De ClaimsTransformationInvalidInputParameter-claim transformatie bevat een ongeldige invoer parameter. Neem contact op met de tenantbeheerder om het beleid bij te werken. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt-aanmelden is onderbroken vanwege een wachtwoord herstel-of wachtwoord registratie vermelding. |
| AADSTS50126 | InvalidUserNameOrPassword-fout bij het valideren van referenties vanwege een ongeldige gebruikers naam of wacht woord. |
| AADSTS50127 | BrokerAppNotInstalled: de gebruiker moet een Broker-app installeren om toegang te krijgen tot deze inhoud. |
| AADSTS50128 | Ongeldige domein naam: er zijn geen Tenant gegevens gevonden in de aanvraag of geïmpliceerd door de verstrekte referenties. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined-werk plek koppelen is vereist voor het registreren van het apparaat. |
| AADSTS50131 | ConditionalAccessFailed: geeft diverse problemen met voorwaardelijke toegang, zoals een slechte status van Windows-apparaten, op die zijn geblokkeerd vanwege verdachte activiteiten, toegangs beleid of beslissingen over beveiligings beleid. |
| AADSTS50132 | SsoArtifactInvalidOrExpired-de sessie is niet geldig vanwege een verlopen wacht woord of een recente wachtwoord wijziging. |
| AADSTS50133 | SsoArtifactRevoked-de sessie is niet geldig vanwege een verlopen wacht woord of een recente wachtwoord wijziging. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter: onjuist Data Center. Als u een aanvraag wilt toestaan die is gestart door een app in de OAuth 2,0-apparaat stroom, moet de geautoriseerde partij zich in hetzelfde Data Center bevinden waar de oorspronkelijke aanvraag zich bevindt. |
| AADSTS50135 | PasswordChangeCompromisedPassword-wachtwoord wijziging is vereist vanwege het account risico. |
| AADSTS50136 | RedirectMsaSessionToApp: er is één MSA-sessie gedetecteerd. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken-de sessie is ongeldig vanwege een ontbrekend extern vernieuwings token. |
| AADSTS50140 | KmsiInterrupt-deze fout is opgetreden vanwege een ' Keep Sign signing '-interrupt wanneer de gebruiker zich aanmeldt. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en een foutcode voor meer informatie. |
| AADSTS50143 | Sessie komt niet overeen: sessie is ongeldig omdat de gebruikertenant niet overeenkomt met de domeinhint als gevolg van andere resource. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en een foutcode voor meer informatie. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword: het Active Directory wacht woord van de gebruiker is verlopen. Genereer een nieuw wacht woord voor de gebruiker of laat de gebruiker het wacht woord opnieuw instellen met het hulp programma Self-service reset. |
| AADSTS50146 | MissingCustomSigningKey-deze app moet worden geconfigureerd met een app-specifieke handtekening sleutel. De toepassing is niet met een dergelijke sleutel geconfigureerd, of de sleutel is verlopen of nog niet geldig. |
| AADSTS50147 | MissingCodeChallenge-de grootte van de code vraag parameter is niet geldig. |
| AADSTS50155 | DeviceAuthenticationFailed is mislukt voor deze gebruiker. |
| AADSTS50158 | Er is niet voldaan aan de ExternalSecurityChallenge-externe beveiligings uitdaging. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration: claims verzonden door externe provider is niet genoeg of ontbrekende claim aangevraagd bij externe provider. |
| AADSTS50166 | ExternalClaimsProviderThrottled-kan de aanvraag niet verzenden naar de claim provider. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired: de client kan een SSO-token verkrijgen via de uitbrei ding voor Windows 10-accounts, maar het token is niet gevonden in de aanvraag of het opgegeven token is verlopen. |
| AADSTS50169 | InvalidRequestBadRealm-de realm is geen geconfigureerde realm van de huidige service naam ruimte. |
| AADSTS50170 | MissingExternalClaimsProviderMapping-de toewijzing van externe besturings elementen ontbreekt. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers: externe uitdaging wordt niet ondersteund voor passthrough-gebruikers. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers: sessie beheer wordt niet ondersteund voor passthrough-gebruikers. |
| AADSTS50180 | Geïntegreerde Windows-verificatie is vereist voor WindowsIntegratedAuthMissing. Schakel de tenant in voor een naadloze eenmalige aanmelding (SSO). |
| AADSTS50187 | DeviceInformationNotProvided: de service kan de verificatie van het apparaat niet uitvoeren. |
| AADSTS50196 | LoopDetected-er is een client-lus gedetecteerd. Controleer de logica van de app om te controleren of de tokens in de cache zijn geïmplementeerd en of de fout condities correct worden afgehandeld.  De app heeft te veel van dezelfde aanvraag in te korte tijd gedaan, wat aangeeft dat het een fout status heeft of abusively aanvraag tokens. |
| AADSTS50197 | ConflictingIdentities-de gebruiker is niet gevonden. Probeer opnieuw aan te melden. |
| AADSTS50199 | CmsiInterrupt: om veiligheids redenen is gebruikers bevestiging vereist voor deze aanvraag.  Omdat dit een interaction_required-fout is, moet de client interactieve verificatie uitvoeren.  Dit gebeurt omdat een webweergave van het systeem is gebruikt voor het aanvragen van een token voor een systeem eigen toepassing. de gebruiker moet daarom worden gevraagd of dit eigenlijk de app is waarmee ze zich kunnen aanmelden. Om deze vraag te voor komen, moet de omleidings-URI deel uitmaken van de volgende lijst met veilige onderdelen: <br />http://<br />https://<br />msauth://(alleen iOS)<br />msauthv2://(alleen iOS)<br />Chrome-extensie://(alleen voor desktop Chrome-browser) |
| AADSTS51000 | RequiredFeatureNotEnabled: de functie is uitgeschakeld. |
| AADSTS51001 | De DomainHintMustbePresent-domein Hint moet aanwezig zijn met een lokale beveiligings-id of een on-premises UPN. |
| AADSTS51004 | UserAccountNotInDirectory: het gebruikers account bestaat niet in de Directory. |
| AADSTS51005 | TemporaryRedirect: gelijk aan HTTP-status 307, wat aangeeft dat de aangevraagde informatie zich bevindt in de URI die is opgegeven in de locatie-header. Wanneer deze status wordt weer gegeven, volgt u de locatie header die is gekoppeld aan het antwoord. Wanneer de oorspronkelijke aanvraag methode is gepost, gebruikt de omgeleide aanvraag ook de POST-methode. |
| AADSTS51006 | Geïntegreerde Windows-verificatie is vereist voor ForceReauthDueToInsufficientAuth. Er is een gebruiker aangemeld met een sessie token waarvoor de geïntegreerde Windows-authenticatie claim ontbreekt. Vraag de gebruiker zich opnieuw aan te melden. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn: de gebruiker heeft geen toestemming gegeven voor toegang tot LinkedIn-resources. |
| AADSTS53000 | DeviceNotCompliant-beleid voor voorwaardelijke toegang vereist een compatibel apparaat en het apparaat is niet compatibel. De gebruiker moet het apparaat inschrijven bij een goedgekeurde MDM-provider zoals Intune. |
| AADSTS53001 | DeviceNotDomainJoined-beleid voor voorwaardelijke toegang vereist een apparaat dat lid is van een domein en het apparaat is niet gekoppeld aan een domein. Laat de gebruiker een apparaat gebruiken dat lid is van een domein. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp: de app die wordt gebruikt, is geen goedgekeurde app voor voorwaardelijke toegang. De gebruiker moet een van de apps uit de lijst met goedgekeurde apps gebruiken om toegang te krijgen. |
| AADSTS53003 | BlockedByConditionalAccess-toegang is geblokkeerd door beleid voor voorwaardelijke toegang. Het toegangs beleid staat geen token uitgifte toe. |
| AADSTS53004 | ProofUpBlockedDueToRisk: de gebruiker moet het registratie proces voor multi-factor Authentication volt ooien voordat deze inhoud kan worden geopend. Gebruiker moet zich registreren voor multi-factor authentication. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist-de gebruiker of beheerder heeft niet ingestemd met het gebruik van de toepassing met ID X. Verzend een interactieve autorisatie aanvraag voor deze gebruiker en resource. |
| AADSTS65004 | UserDeclinedConsent: de gebruiker heeft geen toestemming gegeven om toegang te krijgen tot de app. Laat de gebruiker zich opnieuw aanmelden en toestemming geven voor de app|
| AADSTS65005 | MisconfiguredApplication-de vereiste resource toegangs lijst van de app bevat geen apps die kunnen worden gedetecteerd door de resource of de client-app heeft toegang aangevraagd tot de resource, die niet is opgegeven in de vereiste resource toegangs lijst of de grafiek service heeft een ongeldige aanvraag of bron niet gevonden. Als de app SAML ondersteunt, is het mogelijk dat u de app met de verkeerde id (entiteit) hebt geconfigureerd. Probeer de oplossing die wordt vermeld voor SAML uit met behulp van de onderstaande koppeling: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | De app heeft toegang nodig tot een service `(\"{name}\")` waarvoor uw organisatie `\"{organization}\"` geen abonnement heeft of is ingeschakeld. Neem contact op met uw IT-beheerder om de configuratie van uw service abonnementen te controleren. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant-verificatie is mislukt. Het vernieuwings token is niet geldig. De fout kan de volgende oorzaken hebben:<ul><li>De header voor token binding is leeg</li><li>Hash van token binding komt niet overeen</li></ul> |
| AADSTS70001 | UnauthorizedClient-de toepassing is uitgeschakeld. |
| AADSTS70002 | InvalidClient-fout bij het valideren van de referenties. De opgegeven client_secret komt niet overeen met de verwachte waarde voor deze client. Corrigeer de client_secret en probeer het opnieuw. Zie voor meer informatie [de autorisatie code gebruiken om een toegangs token aan te vragen](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType-de app heeft een niet-ondersteund toekennings type geretourneerd. |
| AADSTS70004 | InvalidRedirectUri: de app heeft een ongeldige omleidings-URI geretourneerd. Het omleidingsadres dat is gespecificeerd door de client komt niet overeen met een geconfigureerd adres of een adres op de OIDC-goedkeuringslijst. |
| AADSTS70005 | UnsupportedResponseType: de app heeft een niet-ondersteund antwoord type geretourneerd om de volgende redenen:<ul><li>het antwoord type token is niet ingeschakeld voor de app</li><li>reactietype 'id_token' vereist 'OpenID'-bereik - bevat een niet-ondersteunde OAuth-parameterwaarde in de gecodeerde wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode: de app heeft een niet-ondersteunde waarde geretourneerd `response_mode` bij het aanvragen van een token.  |
| AADSTS70008 | ExpiredOrRevokedGrant-het vernieuwings token is verlopen vanwege inactiviteit. Het token is uitgegeven op XXX en is gedurende een bepaalde periode inactief. |
| AADSTS70011 | InvalidScope-het bereik dat door de app is aangevraagd, is ongeldig. |
| AADSTS70012 | MsaServerError: er is een server fout opgetreden tijdens het verifiëren van een MSA-gebruiker (Consumer). Probeer het opnieuw. Als dit probleem blijft bestaan, [opent u een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | Fout met AuthorizationPending-OAuth 2,0-apparaat stroom. Autorisatie is in behandeling. Het apparaat probeert de aanvraag opnieuw te pollen. |
| AADSTS70018 | BadVerificationCode-ongeldige verificatie code omdat gebruikers de verkeerde gebruikers code hebben getypt voor de code stroom van het apparaat. Autorisatie is niet goedgekeurd. |
| AADSTS70019 | CodeExpired-verificatie code verlopen. Laat de gebruiker de aanmelding opnieuw proberen. |
| AADSTS75001 | BindingSerializationError: er is een fout opgetreden tijdens het binden van SAML-berichten. |
| AADSTS75003 | UnsupportedBindingError: de app heeft een fout geretourneerd die is gerelateerd aan een niet-ondersteunde binding (SAML-protocol antwoord kan niet worden verzonden via bindingen die geen HTTP POST zijn). |
| AADSTS75005 | Saml2MessageInvalid: Azure AD biedt geen ondersteuning voor de SAML-aanvraag die door de app voor SSO wordt verzonden. |
| AADSTS7500514 | Er is geen ondersteund type SAML-respons gevonden. De ondersteunde antwoord typen zijn ' Response ' (in XML-naam ruimte ' urn: Oasis: names: TC: SAML: 2.0: Protocol ') of ' Assertion ' (in XML-naam ruimte ' urn: Oasis: names: TC: SAML: ' bewering '). Toepassings fout: de ontwikkelaar zal deze fout afhandelen.|
| AADSTS75008 | RequestDeniedError-de aanvraag van de app is geweigerd omdat de SAML-aanvraag een onverwachte bestemming heeft. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims: de verificatie methode waarmee de gebruiker die is geverifieerd met de service, niet overeenkomt met de aangevraagde verificatie methode. |
| AADSTS75016 | De Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2-verificatie aanvraag heeft een ongeldige NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable: de verificatie agent kan geen verbinding maken met Active Directory. Zorg ervoor dat de agent servers lid zijn van hetzelfde AD-forest als de gebruikers waarvan de wacht woorden moeten worden gevalideerd en dat ze verbinding kunnen maken met Active Directory. |
| AADSTS80002 | Er is een time-out opgetreden voor de OnPremisePasswordValidatorRequestTimedout-wachtwoord validatie. Zorg ervoor dat Active Directory beschikbaar is en reageert op aanvragen van de agents. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException: er is een onbekende fout opgetreden tijdens het verwerken van de reactie van de verificatie agent. Voer de aanvraag opnieuw uit. Als het probleem blijft optreden, [opent u een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie over de fout weer te geven. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem: de verificatie agent kan het wacht woord van de gebruiker niet valideren. Raadpleeg de logboeken van de agent voor meer informatie en controleer of Active Directory werkt zoals verwacht. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException-de verificatie agent kan het wacht woord niet ontsleutelen. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours: de gebruikers hebben geprobeerd zich aan te melden buiten het toegestane aantal uren (dit is opgegeven in AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew: de authenticatie poging kan niet worden voltooid vanwege een tijd verschil tussen de computer waarop de verificatie agent en AD worden uitgevoerd. Problemen met de tijd synchronisatie oplossen. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-Kerberos-verificatie poging is mislukt. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported: het verificatie pakket wordt niet ondersteund. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader-er is geen autorisatie-header gevonden. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn-de Tenant is niet ingeschakeld voor naadloze SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat: kan het Kerberos-ticket van de gebruiker niet valideren. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-naadloze eenmalige aanmelding is mislukt omdat het Kerberos-ticket van de gebruiker is verlopen of ongeldig is. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed: kan geen gebruikers object vinden op basis van de informatie in het Kerberos-ticket van de gebruiker. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn: de gebruiker die zich probeert aan te melden bij Azure AD wijkt af van de gebruiker die is aangemeld bij het apparaat. |
| AADSTS90002 | InvalidTenantName-de naam van de Tenant is niet gevonden in het gegevens archief. Controleer of u de juiste Tenant-ID hebt. |
| AADSTS90004 | InvalidRequestFormat-de aanvraag is niet juist opgemaakt. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements-kan de aanvraag niet volt ooien. De aanvraag is ongeldig omdat de id en aanmeldings Hint niet samen kunnen worden gebruikt.  |
| AADSTS90006 | ExternalServerRetryableError-de service is tijdelijk niet beschikbaar.|
| AADSTS90007 | InvalidSessionId-ongeldige aanvraag. De door gegeven sessie-ID kan niet worden geparseerd. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission: de gebruiker of beheerder heeft geen toestemming gegeven voor het gebruik van de toepassing. De toepassing vereist ten minste toegang tot Azure AD door de machtiging voor aanmelden en gebruikers profiel lezen op te geven. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier: de toepassing vraagt een token voor zichzelf aan. Dit scenario wordt alleen ondersteund als de bron die is opgegeven, de op GUID gebaseerde toepassings-ID gebruikt. |
| AADSTS90010 | NotSupported-kan het algoritme niet maken. |
| AADSTS90012 | RequestTimeout: er is een time-out opgetreden voor de aanvraag. |
| AADSTS90013 | InvalidUserInput-de invoer van de gebruiker is ongeldig. |
| AADSTS90014 | MissingRequiredField: deze fout code kan in verschillende gevallen worden weer gegeven wanneer een verwacht veld niet aanwezig is in de referentie. |
| AADSTS90015 | QueryStringTooLong-de query reeks is te lang. |
| AADSTS90016 | MissingRequiredClaim-het toegangs token is niet geldig. De vereiste claim ontbreekt. |
| AADSTS90019 | MissingTenantRealm-Azure AD kan de Tenant-id niet bepalen op basis van de aanvraag. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat: de indeling van de principal-naam is ongeldig of voldoet niet aan de verwachte `name[/host][@realm]` indeling. De principal-naam is vereist, host en realm zijn optioneel en kunnen worden ingesteld op null. |
| AADSTS90023 | InvalidRequest-de aanvraag voor de verificatie service is niet geldig. |
| AADSTS9002313 | InvalidRequest-aanvraag heeft een onjuiste indeling of is ongeldig. -Het probleem doet zich voor omdat er iets mis is met de aanvraag voor een bepaald eind punt. De suggestie voor dit probleem is het verkrijgen van een Fiddler tracering van de fout die zich voordoet en om te zien of de aanvraag daad werkelijk op de juiste wijze is ingedeeld of niet. |
| AADSTS90024 | RequestBudgetExceededError: er is een tijdelijke fout opgetreden. Probeer het opnieuw. |
| AADSTS90033 | MsodsServiceUnavailable: de micro soft online directory service (MSODS) is niet beschikbaar. |
| AADSTS90036 | MsodsServiceUnretryableFailure: er is een onverwachte, niet-herstel bare fout opgetreden in de WCF-service die door MSODS wordt gehost. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie te krijgen over de fout. |
| AADSTS90038 | NationalCloudTenantRedirection-de opgegeven Tenant Y behoort tot de nationale Cloud X. Het huidige Cloud exemplaar ' Z ' heeft geen relatie met X. Er wordt een fout in de Cloud omleiding geretourneerd. |
| AADSTS90043 | NationalCloudAuthCodeRedirection: de functie is uitgeschakeld. |
| AADSTS90051 | InvalidNationalCloudId-de nationale Cloud-id bevat een ongeldige Cloud-id. |
| AADSTS90055 | TenantThrottlingError: er zijn te veel binnenkomende aanvragen. Deze uitzonde ring wordt gegenereerd voor geblokkeerde tenants. |
| AADSTS90056 | BadResourceRequest: als u de code voor een toegangs token wilt inwisselen, moet de app een POST-aanvraag verzenden naar het `/token` eind punt. Voordat u dit doet, moet u ook een autorisatie code opgeven en deze verzenden in de POST-aanvraag naar het `/token` eind punt. Raadpleeg dit artikel voor een overzicht van de OAuth 2,0-autorisatie code stroom: [.. /azuread-dev/v1-protocols-OAuth-code.MD](../azuread-dev/v1-protocols-oauth-code.md). De gebruiker naar het `/authorize` eind punt sturen, waardoor een authorization_code wordt geretourneerd. Door een aanvraag naar het `/token` eind punt te posten, krijgt de gebruiker het toegangs token. Meld u aan bij de Azure Portal en controleer **App-registraties >-eind punten** om te bevestigen dat de twee eind punten correct zijn geconfigureerd. |
| AADSTS90072 | PassThroughUserMfaError: het externe account waarmee de gebruiker zich aanmeldt, bestaat niet op de Tenant waarin ze zijn aangemeld; de gebruiker kan dus niet voldoen aan de MFA-vereisten voor de Tenant. Deze fout kan ook optreden als de gebruikers zijn gesynchroniseerd, maar het kenmerk ImmutableID (source anchor) tussen Active Directory en Azure AD niet overeenkomt. Het account moet eerst worden toegevoegd als een externe gebruiker in de Tenant. Meld u af en meld u aan met een ander Azure AD-gebruikers account. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid: er is een fout opgetreden toen de service probeerde een WS-Federation bericht te verwerken. Het bericht is ongeldig. |
| AADSTS90082 | OrgIdWsFederationNotSupported: het geselecteerde verificatie beleid voor de aanvraag wordt momenteel niet ondersteund. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed-gast accounts zijn niet toegestaan voor deze site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed: de service kan geen token verlenen omdat het bedrijfs object nog niet is ingericht. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired-het gebruikers DA-token is verlopen. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed: er is een fout opgetreden tijdens het maken van het WS-Federation bericht van de URI. |
| AADSTS90090 | GraphRetryableError-de service is tijdelijk niet beschikbaar. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized-Graph heeft een niet-toegestane fout code voor de aanvraag geretourneerd. |
| AADSTS90094 | AdminConsentRequired-toestemming van de beheerder is vereist. |
| AADSTS900382 | De vertrouwelijke client wordt niet ondersteund in een cross-Cloud aanvraag. |
| AADSTS90099 | De toepassing {appId} ({appName}) is niet geautoriseerd in de Tenant {Tenant}. Toepassingen moeten worden gemachtigd om toegang te krijgen tot de Tenant van de klant voordat de beheerder van de partner deze kan gebruiken. Geef vooraf toestemming of voer de juiste partner centrum-API uit om de toepassing te autoriseren. |
| AADSTS90100 | InvalidRequestParameter-de para meter is leeg of ongeldig. |
| AADSTS901002 | AADSTS901002: de aanvraag parameter ' resource ' wordt niet ondersteund. |
| AADSTS90101 | InvalidEmailAddress: de opgegeven gegevens zijn geen geldig e-mail adres. Het e-mail adres moet de indeling hebben `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter-de waarde moet een geldig absolute URI zijn. |
| AADSTS90107 | InvalidXml-de aanvraag is niet geldig. Zorg ervoor dat uw gegevens geen ongeldige tekens bevatten.|
| AADSTS90114 | InvalidExpiryDate: de verloop tijd van het bulk token dat een verlopen token wordt uitgegeven. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode-de gebruikers code is null of leeg.|
| AADSTS90120 | InvalidDeviceFlowRequest-de aanvraag is al goedgekeurd of geweigerd. |
| AADSTS90121 | InvalidEmptyRequest-ongeldige lege aanvraag.|
| AADSTS90123 | IdentityProviderAccessDenied: het token kan niet worden verleend omdat de identiteit of claim uitgifte provider de aanvraag heeft geweigerd. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported: de resource wordt niet ondersteund voor de `/common` `/consumers` eind punten. Gebruik `/organizations` in plaats daarvan het of Tenant-specifieke eind punt. |
| AADSTS90125 | DebugModeEnrollTenantNotFound: de gebruiker bevindt zich niet in het systeem. Controleer of u de juiste gebruikers naam hebt opgegeven. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred-het gebruikers type wordt niet ondersteund voor dit eind punt. Het systeem kan de Tenant van de gebruiker niet afleiden van de gebruikers naam. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported: de toepassing wordt niet ondersteund via de `/common` of- `/consumers` eind punten. Gebruik `/organizations` in plaats daarvan het of Tenant-specifieke eind punt. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated: er is een niet-herstel bare fout opgetreden.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound-de gebruikers-principal heeft de NGC-ID-sleutel niet geconfigureerd. |
| AADSTS130005 | Het controleren van de NgcInvalidSignature-NGC-sleutel handtekening is mislukt.|
| AADSTS130006 | NgcTransportKeyNotFound-de NGC-transport sleutel is niet geconfigureerd op het apparaat. |
| AADSTS130007 | NgcDeviceIsDisabled: het apparaat is uitgeschakeld. |
| AADSTS130008 | NgcDeviceIsNotFound-het apparaat waarnaar wordt verwezen door de NGC-sleutel is niet gevonden. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce-aanvraag nonce is niet gegeven. |
| AADSTS140001 | InvalidSessionKey-de sessie sleutel is niet geldig.|
| AADSTS165900 | InvalidApiRequest-ongeldige aanvraag. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion: de Chrome-webweergave-versie wordt niet ondersteund. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource-de bron is niet geconfigureerd voor het accepteren van alleen-tokens voor apparaten. |
| AADSTS240001 | BulkAADJTokenUnauthorized: de gebruiker is niet gemachtigd om apparaten te registreren in azure AD. |
| AADSTS240002 | RequiredClaimIsMissing: de id_token kan niet worden gebruikt als `urn:ietf:params:oauth:grant-type:jwt-bearer` toekenning.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-de Tenant beheerder heeft een beveiligings beleid geconfigureerd waarmee deze aanvraag wordt geblokkeerd. Controleer de beveiligingsbeleidsregels die zijn gedefinieerd op tenantniveau om na te gaan of uw aanvraag voldoet aan de beleidsvereisten. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest-de toepassing is niet gevonden in de map/Tenant. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de tenant of als er geen toestemming voor is verleend door een gebruiker in de tenant. U hebt mogelijk de id-waarde voor de toepassing onjuist geconfigureerd of uw verificatieaanvraag verzonden naar de verkeerde tenant. |
| AADSTS700020 | InteractionRequired: de toegangs toekenning vereist interactie. |
| AADSTS700022 | InvalidMultipleResourcesScope: de opgegeven waarde voor het bereik van de invoer parameter is niet geldig omdat deze meer dan één resource bevat. |
| AADSTS700023 | InvalidResourcelessScope: de opgegeven waarde voor het bereik van de invoer parameter is niet geldig bij het aanvragen van een toegangs token. |
| AADSTS7000215 | Er is een ongeldig client geheim gegeven. Ontwikkelaars fout: de app probeert zich aan te melden zonder de vereiste of juiste verificatie parameters.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided-de aangestuurde geheime sleutels van de client zijn verlopen. Ga naar de Azure Portal om nieuwe sleutels voor uw app te maken of overweeg de certificaat referenties te gebruiken voor extra beveiliging: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | De InvalidGrantRedeemAgainstWrongTenant-meegeleverde autorisatie code is bedoeld voor gebruik met andere tenants en daarom afgewezen. De OAuth2-autorisatie code moet worden ingewisseld voor dezelfde Tenant die is verkregen voor (/veelvoorkomende of/{tenant-ID} indien van toepassing) |
| AADSTS1000000 | UserNotBoundError-voor de BIND-API moet de Azure AD-gebruiker ook worden geverifieerd met een externe IDP, die nog niet heeft plaatsgevonden. |
| AADSTS1000002 | BindCompleteInterruptError: de binding is voltooid, maar de gebruiker moet hiervan op de hoogte zijn. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-de toepassing is uitgeschakeld. |
| AADSTS7000114| De toepassing appIdentifier mag geen toepassing namens-aanroepen maken.|
| AADSTS7500529 | De waarde ' SAMLId-GUID ' is geen geldige SAML-ID: Azure AD gebruikt dit kenmerk om het kenmerk InResponseTo van het geretourneerde antwoord in te vullen. De ID mag niet beginnen met een getal, dus een algemene strategie is om een teken reeks als ' id ' te laten voorafgaan door naar de teken reeks representatie van een GUID. Id6c1c178c166d486687be4aaf5e482730 is bijvoorbeeld een geldige ID. |

## <a name="next-steps"></a>Volgende stappen

* Hebt u een vraag of kunt u niet vinden wat u zoekt? Maak een GitHub-probleem of Raadpleeg de [ondersteunings-en Help-opties voor ontwikkel aars](./developer-support-help-options.md) voor meer informatie over andere manieren om hulp en ondersteuning te krijgen.