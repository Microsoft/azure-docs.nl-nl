---
title: Naslag informatie voor de fout code
titleSuffix: Azure AD B2C
description: Een lijst met de fout codes die kunnen worden geretourneerd door de Azure Active Directory B2C-service.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6ae806b7666d83652e6b82bac16d89f2f9ce7aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215430"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Fout codes: Azure Active Directory B2C

De volgende fouten kunnen worden geretourneerd door de Azure Active Directory B2C-service.

| Foutcode | Bericht |
| ---------- | ------- |
| `AADB2C90002` | De CORS-resource {0} heeft een 404 niet gevonden. |
| `AADB2C90006` | De omleidings-URI {0} die in de aanvraag is gegeven, is niet geregistreerd voor de client-id {1} . |
| `AADB2C90007` | De toepassing die is gekoppeld aan de client-id, {0} heeft geen geregistreerde omleidings-uri's. |
| `AADB2C90008` | De aanvraag bevat geen client-id-para meter. |
| `AADB2C90010` | De aanvraag bevat geen bereik parameter. |
| `AADB2C90011` | De client-id {0} die in de aanvraag is opgegeven, komt niet overeen met de client-id die is {1} geregistreerd in het beleid. |
| `AADB2C90012` | De scope {0} die in de aanvraag is gegeven, wordt niet ondersteund. |
| `AADB2C90013` | Het aangevraagde reactie type ' {0} ' dat is opgegeven in de aanvraag, wordt niet ondersteund. |
| `AADB2C90014` | De aangevraagde antwoord modus ' {0} ' die in de aanvraag is opgegeven, wordt niet ondersteund. |
| `AADB2C90016` | Het gevraagde client bevestigings type ' {0} ' komt niet overeen met het verwachte type ' {1} '. |
| `AADB2C90017` | De client bevestiging die in de aanvraag is gegeven, is ongeldig: {0} |
| `AADB2C90018` | De client-id {0} die in de aanvraag is opgegeven, is niet geregistreerd in de Tenant {1} . |
| `AADB2C90019` | De sleutel container met de id ' {0} ' in de Tenant ' {1} ' heeft geen geldige sleutel. Reden: {2} . |
| `AADB2C90021` | Het technische profiel {0} bestaat niet in het beleid {1} van de Tenant {2} . |
| `AADB2C90022` | Kan geen meta gegevens retour neren voor het beleid {0} in de Tenant {1} . |
| `AADB2C90023` | Het profiel {0} bevat niet de vereiste meta gegevens sleutel {1} . |
| `AADB2C90025` | Het profiel {0} in het beleid in de {1} Tenant {2} bevat niet de vereiste cryptografische sleutel {3} . |
| `AADB2C90027` | De basis referenties die zijn opgegeven voor, {0} zijn ongeldig. Controleer of de referenties juist zijn en of de resource toegang heeft gekregen. |
| `AADB2C90028` | Het opgegeven client certificaat voor ' {0} ' is ongeldig. Controleer of het certificaat juist is, een persoonlijke sleutel bevat en of toegang is verleend door de resource. |
| `AADB2C90031` | In het beleid is {0} geen standaard gebruikers traject opgegeven. Zorg ervoor dat het beleid of de bovenliggende items een standaard gebruikers traject opgeven als onderdeel van een Relying Party sectie. |
| `AADB2C90035` | De service is tijdelijk niet beschikbaar. Probeer het over enkele minuten opnieuw. |
| `AADB2C90036` | De aanvraag bevat geen URI waarmee de gebruiker wordt omgeleid om de afmelding te posten. Geef een URI op in het parameter veld post_logout_redirect_uri. |
| `AADB2C90037` | Er is een fout opgetreden tijdens het verwerken van de aanvraag. Neem contact op met de beheerder van de site die u wilt openen. |
| `AADB2C90039` | De aanvraag bevat een client bevestiging, maar in het gegeven beleid {0} in de Tenant {1} ontbreekt een Client_secret in RelyingPartyPolicy. |
| `AADB2C90040` | De gebruikers traject {0} bevat geen stap voor het verzenden van claims. |
| `AADB2C90043` | De prompt die in de aanvraag is opgenomen, bevat ongeldige waarden. ' None ', ' aanmelding, ' instemming ' of ' select_account ' wordt verwacht. |
| `AADB2C90044` | De claim {0} wordt niet ondersteund door de claim resolver {1} . |
| `AADB2C90046` | Er zijn problemen met het laden van uw huidige status. U kunt proberen om uw sessie vanaf het begin te starten. |
| `AADB2C90047` | De resource {0} bevat script fouten waardoor het niet kan worden geladen. |
| `AADB2C90048` | Er is een onverwerkte uitzonde ring opgetreden op de server. |
| `AADB2C90051` | Er zijn geen geschikte claim providers gevonden. |
| `AADB2C90052` | Ongeldige gebruikers naam of wacht woord. |
| `AADB2C90053` | Kan een gebruiker met de opgegeven referentie niet vinden. |
| `AADB2C90054` | Ongeldige gebruikers naam of wacht woord. |
| `AADB2C90055` | In het bereik {0} dat is opgegeven in de aanvraag, moet een resource worden opgegeven, zoals https://example.com/calendar.read . |
| `AADB2C90057` | De opgegeven toepassing is niet geconfigureerd om de OAuth-impliciete stroom toe te staan. |
| `AADB2C90058` | De opgegeven toepassing is niet geconfigureerd om open bare clients toe te staan. |
| `AADB2C99059` | De opgegeven aanvraag moet een code_challenge bevatten. Vereist voor apps met één pagina die gebruikmaken van de autorisatie code stroom.| 
| `AADB2C90067` | De omleidings-URI van de post afmelding {0} heeft een ongeldige indeling. Geef een op https gebaseerde URL op zoals ' https://example.com/return ' of voor native-clients gebruik de IETF native client URI ' urn: IETF: WG: OAuth: 2.0: OOB '. |
| `AADB2C90068` | De gegeven toepassing met de ID {0} ' ' is niet geldig voor deze service. Gebruik een toepassing die is gemaakt via de B2C-Portal en probeer het opnieuw. |
| `AADB2C90075` | De claim uitwisseling ' {0} ' die is opgegeven in stap ' {1} ' heeft een HTTP-fout melding met de code ' {2} ' en de reden ' {3} ' geretourneerd. |
| `AADB2C90077` | De gebruiker heeft geen bestaande sessie en de aanvraag prompt parameter heeft de waarde ' {0} '. |
| `AADB2C90079` | Clients moeten een client_secret verzenden bij het inwisselen van een vertrouwelijke toekenning. |
| `AADB2C90080` | De verleende toekenning is verlopen. Voer opnieuw een verificatie uit en probeer het opnieuw. Huidige tijd: {0} , verleende toekennings tijd: {1} , Grant sliding window verval tijd: {2} . |
| `AADB2C90081` | De opgegeven client_secret komt niet overeen met de verwachte waarde voor deze client. Corrigeer de client_secret en probeer het opnieuw. |
| `AADB2C90083` | In de aanvraag ontbreekt de vereiste para meter: {0} . |
| `AADB2C90084` | Open bare clients mogen geen client_secret verzenden bij het inwisselen van een openbaar verkregen subsidie. |
| `AADB2C90085` | Er is een interne fout opgetreden in de service. Voer opnieuw een verificatie uit en probeer het opnieuw. |
| `AADB2C90086` | De opgegeven grant_type [ {0} ] wordt niet ondersteund. |
| `AADB2C90087` | De verstrekte toestemming is niet verleend voor deze versie van het protocol eindpunt. |
| `AADB2C90088` | De verstrekte toestemming is niet uitgegeven voor dit eind punt. Werkelijke waarde: {0} en verwachte waarde: {1} |
| `AADB2C90091` | Gebruikers annulering. |
| `AADB2C90092` | De gegeven toepassing met de ID ' {0} ' is uitgeschakeld voor de Tenant ' {1} '. Schakel de toepassing in en probeer het opnieuw. |
| `AADB2C90107` | De toepassing met de ID ' {0} ' kan geen ID-Token ophalen omdat het OpenID Connect-bereik niet is ingesteld in de aanvraag of omdat de toepassing hiervoor niet is geautoriseerd. |
| `AADB2C90108` | In de Orchestration-stap ' {0} ' wordt geen CpimIssuerTechnicalProfileReferenceId opgegeven wanneer er een wordt verwacht. |
| `AADB2C90110` | De bereik parameter moet ' OpenID Connect ' bevatten bij het aanvragen van een response_type dat ' id_token ' bevat. |
| `AADB2C90111` | Uw account is vergrendeld. Neem contact op met de ondersteunings medewerker om deze te ontgrendelen en probeer het opnieuw. |
| `AADB2C90114` | Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voor komen. Probeer het later opnieuw. |
| `AADB2C90115` | Bij het aanvragen van de response_type code moet de bereik parameter een resource-of client-ID voor toegangs tokens en ' OpenID Connect ' voor ID-tokens bevatten. Neem ook ' offline_access ' op voor vernieuwings tokens. |
| `AADB2C90117` | De scope {0} die in de aanvraag is gegeven, wordt niet ondersteund. |
| `AADB2C90118` | De gebruiker heeft het wacht woord verg eten. |
| `AADB2C90120` | De maximale leeftijds parameter {0} die in de aanvraag is opgegeven, is ongeldig. De maximale leeftijd moet een geheel getal tussen ' {1} ' en ' {2} ' zijn. |
| `AADB2C90122` | De invoer voor ' {0} ' die in de aanvraag is ontvangen, is mislukt voor de validatie van de HTTP-aanvraag. Zorg ervoor dat de invoer geen tekens bevat, zoals < of &. |
| `AADB2C90128` | Het account dat is gekoppeld aan deze toekenning, bestaat niet meer. Voer opnieuw een verificatie uit en probeer het opnieuw. |
| `AADB2C90129` | De verleende toekenning is ingetrokken. Voer opnieuw een verificatie uit en probeer het opnieuw. |
| `AADB2C90145` | Er zijn geen niet-geverifieerde telefoon nummers gevonden en het beleid staat geen door de gebruiker ingevoerde waarde toe. |
| `AADB2C90146` | Het bereik ' {0} ' dat is opgegeven in de aanvraag, bevat meer dan één resource voor een toegangs token, wat niet wordt ondersteund. |
| `AADB2C90149` | Script {0} kan niet worden geladen. |
| `AADB2C90151` | De gebruiker heeft het maximum aantal voor pogingen voor multi-factor Authentication overschreden. |
| `AADB2C90152` | Een multi-factor poll-aanvraag heeft geen reactie ontvangen van de service. |
| `AADB2C90154` | Een aanvraag voor multi-factor verificatie kan geen sessie-id van de service ophalen. |
| `AADB2C90155` | Een aanvraag voor multi-factor verificatie is mislukt met de reden {0} . |
| `AADB2C90156` | Een aanvraag voor multi-factor validatie is mislukt met de reden {0} . |
| `AADB2C90157` | De gebruiker heeft het maximum aantal voor nieuwe pogingen voor een door uzelf bevestigde stap overschreden. |
| `AADB2C90158` | Een zelfbevestigende validatie aanvraag is mislukt met de reden {0} . |
| `AADB2C90159` | Een zelfbevestigende verificatie aanvraag is mislukt met de reden {0} . |
| `AADB2C90161` | Een zelf-bevestigde verzend reactie is mislukt met de reden {0} . |
| `AADB2C90165` | Het SAML-initialisatie bericht met de id ' {0} ' is niet gevonden in de status. |
| `AADB2C90168` | De HTTP-Redirect aanvraag bevat niet de vereiste para meter ' {0} ' voor een ondertekende aanvraag. |
| `AADB2C90178` | Het handtekening certificaat {0} heeft geen persoonlijke sleutel. |
| `AADB2C90182` | De opgegeven code_verifier komt niet overeen met de gekoppelde code_challenge |
| `AADB2C90183` | De opgegeven code_verifier is ongeldig |
| `AADB2C90184` | De opgegeven code_challenge_method wordt niet ondersteund. Ondersteunde waarden zijn Plain of S256 |
| `AADB2C90188` | Het SAML-technische profiel {0} bevat een PartnerEntity-URL van ' {1} ', maar het ophalen van de meta gegevens is mislukt met de reden ' {2} '. |
| `AADB2C90194` | Claim ' {0} ' die is opgegeven voor het Bearer-token is niet aanwezig in de beschik bare claims. Beschik bare claims {1} . |
| `AADB2C90205` | Deze toepassing heeft onvoldoende machtigingen voor deze WebResource om de bewerking uit te voeren. |
| `AADB2C90206` | Er is een time-out opgetreden bij het initialiseren van de client. |
| `AADB2C90208` | De gegeven id_token_hint para meter is verlopen. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90209` | De gegeven id_token_hint para meter bevat geen geaccepteerde doel groep. Geldige doelgroeps waarden: {0} . Geef een ander token op en probeer het opnieuw. |
| `AADB2C90210` | De gegeven id_token_hint para meter kan niet worden gevalideerd. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90211` | De aanvraag bevat een onvolledige status cookie. |
| `AADB2C90212` | De aanvraag bevat een ongeldige status cookie. |
| `AADB2C90220` | De sleutel container in de Tenant {0} met de opslag-ID {1} bestaat, maar bevat geen geldig certificaat. Het certificaat is mogelijk verlopen of uw certificaat kan in de toekomst actief worden (NBF). |
| `AADB2C90223` | Er is een fout opgetreden bij het opschonen van de CORS-resource. |
| `AADB2C90224` | De stroom voor de resource-eigenaar is niet ingeschakeld voor de toepassing. |
| `AADB2C90225` | De gebruikers naam of het wacht woord die in de aanvraag is opgegeven, zijn ongeldig. |
| `AADB2C90226` | De opgegeven token uitwisseling wordt alleen ondersteund via HTTP POST. |
| `AADB2C90232` | De gegeven id_token_hint para meter bevat geen geaccepteerde verlener. Geldige verleners: ' {0} '. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90233` | Er is een mislukte handtekening validatie voor de id_token_hint parameter gegeven. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90235` | De gegeven id_token is verlopen. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90237` | De door gegeven id_token bevat geen geldige doel groep. Geldige doelgroeps waarden: {0} . Geef een ander token op en probeer het opnieuw. |
| `AADB2C90238` | De verstrekte id_token bevat geen geldige certificaat verlener. Geldige uitgevers waarden: {0} . Geef een ander token op en probeer het opnieuw. |
| `AADB2C90239` | De gegeven id_token mislukte handtekening validatie. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90240` | De gegeven id_token is ongeldig en kan niet worden geparseerd. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90242` | Het SAML-technische profiel {0} bevat PARTNERENTITY CDATA dat niet kan worden geladen voor de reden {1} . |
| `AADB2C90243` | De client sleutel/het geheim van de IDP is niet juist geconfigureerd. |
| `AADB2C90244` | Er zijn op dit moment te veel aanvragen. Wacht enige tijd en probeer het opnieuw. |
| `AADB2C90248` | De stroom van de resource-eigenaar kan alleen worden gebruikt door toepassingen die zijn gemaakt via de B2C-beheer Portal. |
| `AADB2C90250` | Het algemene aanmeldings eindpunt wordt niet ondersteund. |
| `AADB2C90255` | De claim uitwisseling die is opgegeven in het technische profiel, {0} is niet voltooid zoals verwacht. U kunt proberen om uw sessie vanaf het begin te starten. |
| `AADB2C90261` | De claim uitwisseling ' {0} ' die is opgegeven in stap ' {1} ' heeft een HTTP-fout bericht geretourneerd dat niet kan worden geparseerd. |
| `AADB2C90272` | De para meter id_token_hint is niet opgegeven in de aanvraag. Geef een token op en probeer het opnieuw. |
| `AADB2C90273` | Er is een ongeldig antwoord ontvangen: {0} |
| `AADB2C90274` | De meta gegevens van de provider geven geen eenmalige afmeldings service op of de binding van het eind punt is niet een van de volgende ' urn: Oasis: names: TC: SAML: 2.0: bindingen: HTTP-redirect ' of ' urn: Oasis: names: TC: SAML: 2.0: bindingen: HTTP-POST '. |
| `AADB2C90276` | De aanvraag is niet consistent met de besturings instelling ' ' {0} : ' {1} ' in technicalProfile ' ' {2} voor het beleid ' {3} ' Tenant ' {4} '. |
| `AADB2C90277` | De Orchestration-stap ' {0} ' van de gebruikers traject ' {1} ' van het beleid ' {2} ' bevat geen verwijzing naar een inhouds definitie. |
| `AADB2C90279` | De opgegeven client-id {0} komt niet overeen met de client-id die de toekenning heeft uitgegeven. |
| `AADB2C90284` | De toepassing met de id ' {0} ' heeft geen toestemming verleend en kan niet worden gebruikt voor lokale accounts. |
| `AADB2C90285` | De toepassing met de id ' {0} ' is niet gevonden. |
| `AADB2C90288` | UserJourney met de id ' {0} ' waarnaar wordt verwezen in TechnicalProfile ' ' voor het vernieuwen van het {1} token voor de Tenant ' {2} ' bestaat niet in het beleid ' {3} ' of een van de basis beleidsregels. |
| `AADB2C90289` | Er is een fout opgetreden bij het verbinding maken met de ID-provider. Probeert u het later nog eens. |
| `AADB2C90296` | De toepassing is niet op de juiste wijze geconfigureerd. Neem contact op met de beheerder van de site die u wilt openen. |
| `AADB2C99005` | De aanvraag bevat een ongeldige bereik parameter die een ongeldig teken bevat {0} . |
| `AADB2C99006` | Azure AD B2C kan de extensie-app met de App-ID niet vinden {0} . Ga naar https://go.microsoft.com/fwlink/?linkid=851224 voor meer informatie. |
| `AADB2C99011` | De meta gegevens waarde {0} is niet opgegeven in TechnicalProfile {1} in het beleid {2} . |
| `AADB2C99013` | De opgegeven combi natie van grant_type [ {0} ] en token_type [ {1} ] wordt niet ondersteund. |
| `AADB2C99015` | Voor het profiel in het beleid in de {0} {1} Tenant {2} ontbreekt alle InputClaims die vereist zijn voor de referentie stroom van het wacht woord voor de resource-eigenaar. |
