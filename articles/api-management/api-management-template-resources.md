---
title: Resources voor Azure API Management-sjabloon | Microsoft Docs
description: Meer informatie over de typen resources die beschikbaar zijn voor gebruik in ontwikkelaars Portal sjablonen in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84689984"
---
# <a name="azure-api-management-template-resources"></a>Resources van Azure API Management-sjabloon
Azure API Management biedt de volgende typen resources voor gebruik in de sjablonen voor de ontwikkelaars Portal.  
  
-   [Teken reeks resources](#strings)  
  
-   [Glyph-resources](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Teken reeks resources  
 API Management biedt een uitgebreide set teken reeks bronnen voor gebruik in de ontwikkelaars Portal. Deze resources worden gelokaliseerd in alle talen die door API Management worden ondersteund. In de standaardset sjablonen worden deze resources gebruikt voor paginakop teksten, labels en constanten die worden weer gegeven in de ontwikkelaars Portal. Als u een teken reeks bron in uw sjablonen wilt gebruiken, geeft u het voor voegsel van de resource teken reeks op, gevolgd door de naam van de teken reeks, zoals in het volgende voor beeld.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Het volgende voor beeld is afkomstig uit de sjabloon producten lijst en geeft **producten** aan de bovenkant van de pagina weer.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
De volgende lokalisatie opties worden ondersteund:

| Landinstelling    | Taal               |
|-----------|------------------------|
| en      | Engelstalig              |
| Support      | Tsjechisch              |
| normalis      | Deutsch              |
| s      | Spaans              |
| nl      | Frans             |
| hu      | Magyar               |
| ,      | Italiano             |
| "ja-JP"   | Japans                |
| ko      | Koreaans                |
| nl      | Nederlands           |
| pl      | Poolse               |
| "pt-br"   | "Português (Brazilië)"   |
| "pt-pt"   | "Português (Portugal)" |
| ru      | Russisch              |
| SV      | Svenska              |
| formulieren      | Turks               |
| "zh-Hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Raadpleeg de volgende tabellen voor de teken reeks bronnen die beschikbaar zijn voor gebruik in de sjablonen voor de ontwikkelaars Portal. Gebruik de tabel naam als voor voegsel voor de teken reeks resources in die tabel.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentatie](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleApis|API's|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Voor beeld van toepassing|  
|WebApplicationsRequirementsHeader|Vereisten|  
|WebApplicationsScreenshotAlt|Schermopname|  
|WebApplicationsScreenshotsHeader|Schermopnamen|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Weet u zeker dat u de toepassing wilt verwijderen?|  
|WebDevelopersAppNotPublished|Niet gepubliceerd|  
|WebDevelopersAppNotSubmitted|Niet verzonden|  
|WebDevelopersAppTableCategoryHeader|Categorie|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Staat|  
|WebDevelopersEditLink|Bewerken|  
|WebDevelopersRegisterAppLink|Toepassing registreren|  
|WebDevelopersRemoveLink|Verwijderen|  
|WebDevelopersSubmitLink|Verzenden|  
|WebDevelopersYourApplicationsHeader|Uw toepassingen|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebApplicationsHeader|Toepassingen|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Name|Tekst|  
|----------|----------|  
|NoItemsToDisplay|Geen resultaten gevonden.|  
|GeneralExceptionMessage|Er is iets geen recht. Dit kan een tijdelijke fout of een bug zijn. Probeer het opnieuw.|  
|GeneralJsonExceptionMessage|Er is iets geen recht. Dit kan een tijdelijke fout of een bug zijn. Laad de pagina opnieuw en probeer het opnieuw.|  
|ConfirmationMessageUnsavedChanges|Er zijn een aantal niet-opgeslagen wijzigingen. Weet u zeker dat u de wijzigingen wilt annuleren en wilt negeren?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Hoofd tekst van de HTTP-aanvraag is te groot.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelCancel|Annuleren|  
|ButtonLabelSave|Opslaan|  
|GeneralExceptionMessage|Er is iets geen recht. Dit kan een tijdelijke fout of een bug zijn. Probeer het opnieuw.|  
|NoItemsToDisplay|Er zijn geen items om weer te geven.|  
|PagerButtonLabelFirst|Eerste|  
|PagerButtonLabelLast|Laatste|  
|PagerButtonLabelNext|Volgende|  
|PagerButtonLabelPrevious|Vorige|  
|PagerLabelPageNOfM|Pagina {0} van {1}|  
|PasswordTooShort|Het wacht woord is te kort|  
|EmailAsPassword|Gebruik uw e-mail adres niet als uw wacht woord|  
|PasswordSameAsUserName|Uw wacht woord mag niet uw gebruikers naam bevatten|  
|PasswordTwoCharacterClasses|Verschillende teken klassen gebruiken|  
|PasswordTooManyRepetitions|Te veel herhalingen|  
|PasswordSequenceFound|Uw wacht woord bevat reeksen|  
|PagerLabelPageSize|Paginagrootte|  
|CurtainLabelLoading|Laden...|  
|TablePlaceholderNothingToDisplay|Er zijn geen gegevens voor de geselecteerde periode en het bereik|  
|ButtonLabelClose|Sluiten|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Documentatie  
  
|Name|Tekst|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Ongeldige header ' {0} '|  
|WebDocumentationInvalidRequestErrorMessage|Ongeldige aanvraag-URL|  
|TextboxLabelAccessToken|Toegangs token *|  
|DropdownOptionPrimaryKeyFormat|Basis{0}|  
|DropdownOptionSecondaryKeyFormat|Primaire{0}|  
|WebDocumentationSubscriptionKeyText|Uw abonnements sleutel|  
|WebDocumentationTemplatesAddHeaders|Vereiste HTTP-headers toevoegen|  
|WebDocumentationTemplatesBasicAuthSample|Basis verificatie voorbeeld|  
|WebDocumentationTemplatesCurlForBasicAuth|voor het gebruik van basis autorisatie:--gebruiker {username}: {password}|  
|WebDocumentationTemplatesCurlValuesForPath|Geef waarden op voor para meters voor het pad (weer gegeven als {...}), uw abonnements sleutel en waarden voor query parameters|  
|WebDocumentationTemplatesDeveloperKey|Uw abonnements sleutel opgeven|  
|WebDocumentationTemplatesJavaApache|In dit voor beeld wordt de Apache HTTP-client van HTTP-onderdelen gebruikt (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Geef indien nodig waarden op voor optionele para meters|  
|WebDocumentationTemplatesPhpPackage|In dit voor beeld wordt het HTTP_Request2-pakket gebruikt. (voor meer informatie: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Geef waarden op voor Path-para meters (weer gegeven als {...}) en indien nodig de hoofd tekst van de aanvraag|  
|WebDocumentationTemplatesRequestBody|Aanvraag tekst opgeven|  
|WebDocumentationTemplatesRequiredParams|Geef waarden op voor de volgende vereiste para meters|  
|WebDocumentationTemplatesValuesForPath|Geef waarden op voor para meters voor het pad (weer gegeven als {...})|  
|OAuth2AuthorizationEndpointDescription|Het autorisatie-eind punt wordt gebruikt om te communiceren met de resource-eigenaar en krijgt een autorisatie machtiging.|  
|OAuth2AuthorizationEndpointName|Autorisatie-eind punt|  
|OAuth2TokenEndpointDescription|Het token-eind punt wordt door de client gebruikt voor het verkrijgen van een toegangs token door het verlenen of vernieuwen van het token te presen teren.|  
|OAuth2TokenEndpointName|Token eindpunt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p de \>         client initieert de stroom door de gebruikers agent van de resource-eigenaar naar het autorisatie-eind punt te sturen.  De client bevat de client-id, het aangevraagde bereik, de lokale status en een omleidings-URI waarnaar de autorisatie server de gebruikers agent weer verzendt zodra toegang is verleend (of geweigerd).     </p \>     <p \>         de autorisatie server verifieert de resource-eigenaar (via de gebruikers agent) en bepaalt of de resource-eigenaar de toegangs aanvraag van de client verleent of weigert.     </p \>     <p \>         aangenomen dat de resource-eigenaar toegang verleent, stuurt de autorisatie server de gebruikers agent terug naar de client met behulp van de omleidings-URI die eerder is gegeven (in de aanvraag of tijdens de client registratie).  De omleidings-URI bevat een autorisatie code en een lokale status die eerder door de client is geleverd.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     als de gebruiker de toegangs aanvraag van als de aanvraag ongeldig is, wordt de client op de hoogte gesteld door de volgende para meters toe te voegen aan de omleiding: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Autorisatie aanvraag|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p \>         de client-app moet de gebruiker naar het autorisatie-eind punt sturen om het OAuth-proces te initiëren.          Bij het autorisatie-eind punt verifieert de gebruiker de toegang tot de app en verleent of weigert deze.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     aangenomen dat de resource-eigenaar toegang verleent, stuurt de autorisatie server de gebruikers agent terug naar de client met behulp van de omleidings-URI die eerder is vermeld (in de aanvraag of tijdens de client registratie).  De omleidings-URI bevat een autorisatie code en een lokale status die eerder door de client is geleverd. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p \>  de client vraagt om een toegangs token van het token eind punt van de autorisatie server door de autorisatie code op te nemen die in de vorige stap is ontvangen.  Bij het maken van de aanvraag verifieert de client bij de autorisatie server.  De client bevat de omleidings-URI die wordt gebruikt om de autorisatie code voor verificatie te verkrijgen. </p \> <p \>     de autorisatie server verifieert de client, valideert de autorisatie code en zorgt ervoor dat de ontvangen omleidings-URI overeenkomt met de URI die wordt gebruikt om de client om te leiden in stap (C).  Als dit het geval is, reageert de autorisatie server terug met een toegangs token en eventueel een vernieuwings token. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p \>     als de client verificatie van de aanvraag is mislukt of ongeldig is, reageert de autorisatie server met een HTTP 400 (ongeldige aanvraag) status code (tenzij anders opgegeven) en bevat de volgende para meters met het antwoord. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p \>   maakt de client een aanvraag voor het token-eind punt door de volgende para meters te verzenden met de indeling ' Application/x-www-form-urlencoded ' met een teken codering van UTF-8 in de entiteit van de HTTP-aanvraag. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p \>  geeft de autorisatie server een toegangs token en een optioneel vernieuwings token uit en wordt het antwoord gemaakt door de volgende para meters toe te voegen aan de entiteit-body van het HTTP-antwoord met een 200 (OK)-status code. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p \>  de client verifieert met de autorisatie server en vraagt een toegangs token op uit het eind punt van de token. </p \> <p \>  de autorisatie server verifieert de client, en als dit geldig is, wordt een toegangs token verleend. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>     als de aanvraag client verificatie mislukt of ongeldig is, reageert de autorisatie server met een HTTP 400 (ongeldige aanvraag) status code (tenzij anders opgegeven) en bevat de volgende para meters met het antwoord. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>   maakt de client een aanvraag voor het eind punt van het token door de volgende para meters toe te voegen met de indeling ' Application/x-www-form-urlencoded ' met een teken codering van UTF-8 in de entiteit van de HTTP-aanvraag. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>  als de aanvraag voor het toegangs token geldig en goedgekeurd is, geeft de autorisatie server een toegangs token en een optioneel vernieuwings token uit en wordt het antwoord gemaakt door de volgende para meters toe te voegen aan de entiteits hoofdtekst van het HTTP-antwoord met een 200 (OK)-status code. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p de \>   client initieert de stroom door de gebruikers agent van de resource-eigenaar naar het autorisatie-eind punt te sturen.  De client bevat de client-id, het aangevraagde bereik, de lokale status en een omleidings-URI waarnaar de autorisatie server de gebruikers agent weer verzendt zodra toegang is verleend (of geweigerd). </p \> <p \>        de autorisatie server verifieert de resource-eigenaar (via de gebruikers agent) en bepaalt of de resource-eigenaar de toegangs aanvraag van de client verleent of weigert. </p \> <p \>        aangenomen dat de resource-eigenaar toegang verleent, stuurt de autorisatie server de gebruikers agent terug naar de client met behulp van de omleidings-URI die u eerder hebt ingesteld.  De omleidings-URI bevat het toegangs token in het URI-fragment. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     als de eigenaar van de resource de toegangs aanvraag weigert of als de aanvraag om andere redenen dan een ontbrekende of ongeldige omleidings-URI is mislukt, informeert de autorisatie server de client door de volgende para meters toe te voegen aan het fragment onderdeel van de omleidings-URI met de indeling ' Application/x-www-form-urlencoded '. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p \>     de client-app moet de gebruiker naar het autorisatie-eind punt sturen om het OAuth-proces te initiëren.      Bij het autorisatie-eind punt verifieert de gebruiker de toegang tot de app en verleent of weigert deze. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     als de resource-eigenaar de toegangs aanvraag verleent, geeft de autorisatie server een toegangs token uit en wordt het aan de client geleverd door de volgende para meters toe te voegen aan het fragment onderdeel van de omleidings-URI met de indeling ' Application/x-www-form-urlencoded '. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|De autorisatie code stroom is geoptimaliseerd voor clients die de vertrouwelijkheid van hun referenties kunnen onderhouden (zoals webserver toepassingen die zijn geïmplementeerd met behulp van PHP, Java, Python, Ruby, ASP.NET, enzovoort).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Autorisatie code verlenen|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|De stroom van client referenties is geschikt in gevallen waarin de client (uw toepassing) toegang tot de beveiligde bronnen aanvraagt onder het besturings element. De client wordt beschouwd als eigenaar van een resource, dus er is geen interactie tussen de eind gebruiker vereist.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Verleende client referenties|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Impliciete stroom is geoptimaliseerd voor clients die niet in staat zijn om de vertrouwelijkheid van hun referenties te onderhouden die bekend zijn bij het werken met een bepaalde omleidings-URI. Deze clients worden meestal geïmplementeerd in een browser met behulp van een script taal zoals Java script.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Impliciete toekenning|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|De gegevens stroom van het wacht woord voor de resource-eigenaar is geschikt wanneer de resource-eigenaar een vertrouwens relatie heeft met de client (uw toepassing), zoals het besturings systeem van het apparaat of een toepassing met veel bevoegdheden. Deze stroom is geschikt voor clients die de referenties van de resource-eigenaar kunnen verkrijgen (gebruikers naam en wacht woord, meestal met behulp van een interactief formulier).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Toewijzing van wacht woord van resource-eigenaar|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p \>   de eigenaar van de resource biedt de client de gebruikers naam en het wacht woord. </p \> <p \> de client vraagt om een toegangs token van het token eind punt van de autorisatie server door de referenties op te nemen die zijn ontvangen van de resource-eigenaar.  Bij het maken van de aanvraag verifieert de client bij de autorisatie server. </p \> <p \>     de autorisatie server verifieert de client en valideert de referenties van de resource-eigenaar, en indien geldig een toegangs token. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>  als de aanvraag client verificatie mislukt of ongeldig is, reageert de autorisatie server met een HTTP 400 (ongeldige aanvraag) status code (tenzij anders opgegeven) en bevat de volgende para meters met het antwoord. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>    maakt de client een aanvraag voor het eind punt van het token door de volgende para meters toe te voegen met de indeling ' Application/x-www-form-urlencoded ' met een teken codering van UTF-8 in de entiteit van de HTTP-aanvraag. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>   als de aanvraag voor het toegangs token geldig en goedgekeurd is, geeft de autorisatie server een toegangs token en een optioneel vernieuwings token uit en wordt het antwoord gemaakt door de volgende para meters toe te voegen aan de entiteits hoofdtekst van het HTTP-antwoord met een 200 (OK)-status code. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Toegangs token aanvraag|  
|OAuth2Step_AuthorizationRequest_Name|Autorisatie aanvraag|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Vereist. Het toegangs token dat is uitgegeven door de autorisatie server.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Vereist. Het toegangs token dat is uitgegeven door de autorisatie server.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Vereist. Het toegangs token dat is uitgegeven door de autorisatie server.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Vereist. Het toegangs token dat is uitgegeven door de autorisatie server.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Vereist. Client-id.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|VEREIST als de client niet wordt geverifieerd met de autorisatie server.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Vereist. De client-id.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Vereist. De autorisatie code die is gegenereerd door de autorisatie server.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Vereist. De autorisatie code die van de autorisatie server is ontvangen.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Beschrijving. Menselijk Lees bare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Beschrijving. Menselijk Lees bare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Beschrijving. Menselijk Lees bare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Beschrijving. Menselijk Lees bare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Beschrijving. Menselijk Lees bare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Beschrijving. Een URI die een door een mens lees bare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Beschrijving. Een URI die een door een mens lees bare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Beschrijving. Een URI die een door een mens lees bare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Beschrijving. Een URI die een door een mens lees bare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Beschrijving. Een URI die een door een mens lees bare webpagina identificeert met informatie over de fout.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Vereist. Een enkele ASCII-fout code van de volgende: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Vereist. Een enkele ASCII-fout code van de volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Vereist. Een enkele ASCII-fout code van de volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Vereist. Een enkele ASCII-fout code van de volgende: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Vereist. Een enkele ASCII-fout code van de volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Aanbevelingen. De levens duur in seconden van het toegangs token.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Aanbevelingen. De levens duur in seconden van het toegangs token.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Aanbevelingen. De levens duur in seconden van het toegangs token.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Aanbevelingen. De levens duur in seconden van het toegangs token.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Vereist. De waarde moet worden ingesteld op authorization_code.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Vereist. De waarde moet worden ingesteld op client_credentials.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. De waarde moet worden ingesteld op ' wacht woord '.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. Het wacht woord voor de resource-eigenaar.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Beschrijving. De URI van het omleidings eindpunt moet een absolute URI zijn.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|VEREIST als de para meter "redirect_uri" is opgenomen in de autorisatie aanvraag en de waarden ervan identiek moeten zijn.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Beschrijving. De URI van het omleidings eindpunt moet een absolute URI zijn.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Beschrijving. Het vernieuwings token dat kan worden gebruikt om nieuwe toegangs tokens te verkrijgen.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Beschrijving. Het vernieuwings token dat kan worden gebruikt om nieuwe toegangs tokens te verkrijgen.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Beschrijving. Het vernieuwings token dat kan worden gebruikt om nieuwe toegangs tokens te verkrijgen.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Vereist. De waarde moet worden ingesteld op ' code '.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Vereist. De waarde moet worden ingesteld op token.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Beschrijving. Het bereik van de toegangs aanvraag.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPTIONEEL als deze identiek is aan het bereik dat door de client is aangevraagd; anders vereist.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Beschrijving. Het bereik van de toegangs aanvraag.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPTIONEEL, indien identiek aan het bereik dat door de client is aangevraagd; anders vereist.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Beschrijving. Het bereik van de toegangs aanvraag.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPTIONEEL als deze identiek is aan het bereik dat door de client is aangevraagd; anders vereist.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Beschrijving. Het bereik van de toegangs aanvraag.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPTIONEEL, indien identiek aan het bereik dat door de client is aangevraagd; anders vereist.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|VEREIST als de para meter ' state ' aanwezig is in de client autorisatie aanvraag.  De exacte waarde die van de client is ontvangen.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Aanbevelingen. Een ondoorzichtige waarde die door de client wordt gebruikt om de status van de aanvraag en de retour aanroep bij te houden.  De autorisatie server bevat deze waarde bij het omleiden van de gebruiker-Agent naar de client.  De para meter moet worden gebruikt voor het voor komen van aanvraag vervalsing op meerdere sites.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|VEREIST als de para meter ' state ' aanwezig is in de client autorisatie aanvraag.  De exacte waarde die van de client is ontvangen.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|VEREIST als de para meter ' state ' aanwezig is in de client autorisatie aanvraag.  De exacte waarde die van de client is ontvangen.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Aanbevelingen. Een ondoorzichtige waarde die door de client wordt gebruikt om de status van de aanvraag en de retour aanroep bij te houden.  De autorisatie server bevat deze waarde bij het omleiden van de gebruiker-Agent naar de client.  De para meter moet worden gebruikt voor het voor komen van aanvraag vervalsing op meerdere sites.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|VEREIST als de para meter ' state ' aanwezig is in de client autorisatie aanvraag.  De exacte waarde die van de client is ontvangen.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Vereist. Het type van het token dat is uitgegeven.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Vereist. Het type van het token dat is uitgegeven.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Vereist. Het type van het token dat is uitgegeven.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Vereist. Het type van het token dat is uitgegeven.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. De gebruikers naam van de resource-eigenaar.|  
|OAuth2UnsupportedTokenType|Het token type ' {0} ' wordt niet ondersteund.|  
|OAuth2InvalidState|Ongeldig antwoord van de autorisatie server|  
|OAuth2GrantType_AuthorizationCode|Autorisatiecode|  
|OAuth2GrantType_Implicit|Impliciet|  
|OAuth2GrantType_ClientCredentials|Clientreferenties|  
|OAuth2GrantType_ResourceOwnerPassword|Wacht woord van resource-eigenaar|  
|WebDocumentation302Code|302 gevonden|  
|WebDocumentation400Code|400 (ongeldige aanvraag)|  
|OAuth2SendingMethod_AuthHeader|Autorisatie-header|  
|OAuth2SendingMethod_QueryParam|Queryparameter|  
|OAuth2AuthorizationServerGeneralException|Er is een fout opgetreden tijdens het autoriseren van de toegang via {0}|  
|OAuth2AuthorizationServerCommunicationException|Er kan geen HTTP-verbinding tot stand worden gebracht met de autorisatie server of het bestand is onverwacht gesloten.|  
|WebDocumentationOAuth2GeneralErrorMessage|Er is een onverwachte fout opgetreden.|  
|AuthorizationServerCommunicationException|Er is een communicatie fout opgetreden voor de autorisatie server. Neem contact op met de beheerder.|  
|TextblockSubscriptionKeyHeaderDescription|De abonnements sleutel waarmee u toegang krijgt tot deze API. Gevonden in uw <een href = '/Developer- \> profiel</a \> .|  
|TextblockOAuthHeaderDescription|Het OAuth 2,0-toegangs token dat is verkregen van <i \> {0}</i \> . Ondersteunde toekennings typen: <ik \> {1}</i \> .|  
|TextblockContentTypeHeaderDescription|Het media type van de hoofd tekst die naar de API is verzonden.|  
|ErrorMessageApiNotAccessible|De API die u probeert aan te roepen, is op dit moment niet toegankelijk. Neem contact op met de API-Uitgever <een href = "/issues" \> hier</a \> .|  
|ErrorMessageApiTimedout|De API die u probeert aan te roepen, duurt langer dan normaal om het antwoord terug te krijgen. Neem contact op met de API-Uitgever <een href = "/issues" \> hier</a \> .|  
|BadRequestParameterExpected|de para meter ' ' ' {0} wordt verwacht '|  
|TooltipTextDoubleClickToSelectAll|Dubbel klik om alles te selecteren.|  
|TooltipTextHideRevealSecret|Weer geven/verbergen|  
|ButtonLinkOpenConsole|Probeer het nu|  
|SectionHeadingRequestBody|Aanvraagbody|  
|SectionHeadingRequestParameters|Aanvraag parameters|  
|SectionHeadingRequestUrl|Aanvraag-URL|  
|SectionHeadingResponse|Antwoord|  
|SectionHeadingRequestHeaders|Aanvraagheaders|  
|FormLabelSubtextOptional|optioneel|  
|SectionHeadingCodeSamples|Codevoorbeelden|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect Connect ID-token verkregen van <i \> {0}</i \> . Ondersteunde toekennings typen: <ik \> {1}</i \> .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Tekst|  
|----------|----------|  
|LinkLabelBack|terug|  
|LinkLabelHomePage|Start pagina|  
|LinkLabelSendUsEmail|Stuur ons een e-mail|  
|PageTitleError|Er is een probleem opgetreden bij het uitvoeren van de aangevraagde pagina|  
|TextblockPotentialCauseIntermittentIssue|Dit kan een onregelmatige gegevens toegangs probleem zijn dat al is verwijderd.|  
|TextblockPotentialCauseOldLink|De koppeling waarop u hebt geklikt, is mogelijk oud en verwijst niet meer naar de juiste locatie.|  
|TextblockPotentialCauseTechnicalProblem|Er is mogelijk een technisch probleem op onze kant.|  
|TextblockPotentialSolutionRefresh|Vernieuw de pagina.|  
|TextblockPotentialSolutionStartOver|Begin met onze {0} .|  
|TextblockPotentialSolutionTryAgain|Ga naar {0} de bewerking en probeer het opnieuw.|  
|TextReportProblem|{0} Er wordt gekeken wat er mis is gegaan en we kijken zo snel mogelijk.|  
|TitlePotentialCause|Mogelijke oorzaak|  
|TitlePotentialSolution|Dit kan een tijdelijk probleem zijn, maar een aantal dingen die u kunt proberen|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebIssuesIndexTitle|Problemen|  
|WebIssuesNoActiveSubscriptions|U hebt geen actieve abonnementen. U moet zich abonneren op een product om een probleem te melden.|  
|WebIssuesNotSignin|U bent niet aangemeld. Meld {0} een probleem of post een opmerking.|  
|WebIssuesReportIssueButton|Probleem melden|  
|WebIssuesSignIn|aanmelden|  
|WebIssuesStatusReportedBy|Status: {0} &#124; gerapporteerd door {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|Tekst|  
|----------|----------|  
|LinkLabelHomePage|Start pagina|  
|LinkLabelSendUsEmail|Stuur ons een e-mail|  
|PageTitleNotFound|Kan de gezochte pagina niet vinden|  
|TextblockPotentialCauseMisspelledUrl|Mogelijk hebt u de URL verkeerd gespeld als u deze in hebt getypt.|  
|TextblockPotentialCauseOldLink|De koppeling waarop u hebt geklikt, is mogelijk oud en verwijst niet meer naar de juiste locatie.|  
|TextblockPotentialSolutionRetype|Typ de URL opnieuw.|  
|TextblockPotentialSolutionStartOver|Begin met onze {0} .|  
|TextReportProblem|{0} Er wordt gekeken wat er mis is gegaan en we kijken zo snel mogelijk.|  
|TitlePotentialCause|Mogelijke oorzaak|  
|TitlePotentialSolution|Mogelijke oplossing|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebProductsAgreement|Door u te abonneren op {0} het product, gaat u akkoord met de `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` .|  
|WebProductsLegalTermsLink|Gebruiksvoorwaarden|  
|WebProductsSubscribeButton|Abonneren|  
|WebProductsUsageLimitsHeader|Gebruiksbeperkingen|  
|WebProductsYouAreNotSubscribed|U bent geabonneerd op dit product.|  
|WebProductsYouRequestedSubscription|U hebt een abonnement op dit product aangevraagd.|  
|ErrorYouNeedToAgreeWithLegalTerms|U moet akkoord gaan met de gebruiks voorwaarden voordat u kunt door gaan.|  
|ButtonLabelAddSubscription|Abonnement toevoegen|  
|LinkLabelChangeSubscriptionName|wijzigen|  
|ButtonLabelConfirm|Bevestigen|  
|TextblockMultipleSubscriptionsCount|U hebt {0} abonnementen op dit product:|  
|TextblockSingleSubscriptionsCount|U hebt een {0} abonnement op dit product:|  
|TextblockSingleApisCount|Dit product bevat {0} API:|  
|TextblockMultipleApisCount|Dit product bevat {0} api's:|  
|TextblockHeaderSubscribe|Abonneren op product|  
|TextblockSubscriptionDescription|Er wordt als volgt een nieuw abonnement gemaakt:|  
|TextblockSubscriptionLimitReached|De limiet voor abonnementen is bereikt.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleProducts|Producten|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|Tekst|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Aanmelden is op dit moment uitgeschakeld door de Administrators.|  
|TextboxExternalIdentitiesSigninInvitation|U kunt zich ook aanmelden met|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Meld u aan met:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Name|Tekst|  
|----------|----------|  
|PrincipalNotFound|Principal is niet gevonden of de hand tekening is ongeldig|  
|ErrorSsoAuthenticationFailed|SSO-verificatie is mislukt|  
|ErrorSsoAuthenticationFailedDetailed|Er is een ongeldig token gegeven of de hand tekening kan niet worden geverifieerd.|  
|ErrorSsoTokenInvalid|SSO-token is ongeldig|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail adres is {0} al geregistreerd|  
|ValidationErrorSpecificEmailInvalid|E-mail adres {0} is ongeldig|  
|ValidationErrorPasswordInvalid|Het wacht woord is ongeldig. Corrigeer de fouten en probeer het opnieuw.|  
|PropertyTooShort|{0} is te kort|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Het e-mailadres is ongeldig.|  
|ValidationMessageNewPasswordConfirmationRequired|Bevestig het nieuwe wacht woord|  
|ValidationErrorPasswordConfirmationRequired|Wacht woord voor bevestigen is leeg|  
|WebAuthenticationEmailChangeNotice|E-mail met wijzigings bevestiging is aan {0} . Volg de instructies in de IT om het nieuwe e-mail adres te bevestigen. Als het e-mail adres in de volgende paar minuten niet in uw postvak wordt ontvangen, controleert u de map Ongewenste e-mail.|  
|WebAuthenticationEmailChangeNoticeHeader|De wijzigings aanvraag voor de e-mail is verwerkt|  
|WebAuthenticationEmailChangeNoticeTitle|Wijziging van e-mail aangevraagd|  
|WebAuthenticationEmailHasBeenRevertedNotice|U hebt al een e-mail adres. De aanvraag is teruggezet|  
|ValidationErrorEmailAlreadyExists|E-mail adres bestaat al|  
|ValidationErrorEmailInvalid|Ongeldig e-mail adres|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|Het e-mailadres is vereist.|  
|WebAuthenticationErrorNoticeHeader|Fout|  
|WebAuthenticationFieldLengthErrorMessage|{0} moet een maximum lengte van {1}|  
|TextboxLabelEmailFirstName|Voornaam|  
|ValidationErrorFirstNameRequired|De voor naam is vereist.|  
|ValidationErrorFirstNameInvalid|Ongeldige voor naam|  
|NoticeInvalidInvitationToken|Bevestigings koppelingen zijn slechts 48 uur geldig. Als u zich nog steeds binnen deze periode bevindt, controleert u of de koppeling juist is. Als uw koppeling is verlopen, herhaalt u de bewerking die u probeert te bevestigen.|  
|NoticeHeaderInvalidInvitationToken|Ongeldig uitnodigings token|  
|NoticeTitleInvalidInvitationToken|Bevestigings fout|  
|WebAuthenticationLastNameInvalidErrorMessage|Ongeldige achternaam|  
|TextboxLabelEmailLastName|Achternaam|  
|ValidationErrorLastNameRequired|De achternaam is vereist.|  
|WebAuthenticationLinkExpiredNotice|De bevestigings koppeling die naar u is verzonden, is verlopen. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|De koppeling voor het opnieuw instellen van het wacht woord is ongeldig of verlopen.|  
|WebAuthenticationLinkExpiredNoticeTitle|Koppeling verzonden|  
|WebAuthenticationNewPasswordLabel|Nieuw wachtwoord|  
|ValidationMessageNewPasswordRequired|Het nieuwe wacht woord is vereist.|  
|TextboxLabelNotificationsSenderEmail|E-mail adres afzender|  
|TextboxLabelOrganizationName|Organisatienaam|  
|WebAuthenticationOrganizationRequiredErrorMessage|De naam van de organisatie is leeg|  
|WebAuthenticationPasswordChangedNotice|Het wacht woord is bijgewerkt|  
|WebAuthenticationPasswordChangedNoticeTitle|Wacht woord bijgewerkt|  
|WebAuthenticationPasswordCompareErrorMessage|Wacht woorden komen niet overeen|  
|WebAuthenticationPasswordConfirmLabel|Wachtwoord bevestigen|  
|ValidationErrorPasswordInvalidDetailed|Het wacht woord is te zwak.|  
|WebAuthenticationPasswordLabel|Wachtwoord|  
|ValidationErrorPasswordRequired|Het wachtwoord is vereist.|  
|WebAuthenticationPasswordResetSendNotice|E-mail bevestiging voor wacht woord wijzigen is aan {0} . Volg de instructies in het e-mail bericht om door te gaan met het wijzigen van het wacht woord.|  
|WebAuthenticationPasswordResetSendNoticeHeader|De aanvraag voor het opnieuw instellen van het wacht woord is verwerkt|  
|WebAuthenticationPasswordResetSendNoticeTitle|Wacht woord opnieuw instellen aangevraagd|  
|WebAuthenticationRequestNotFoundNotice|Kan de aanvraag niet vinden|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-mail adres afzender is leeg|  
|WebAuthenticationSigninPasswordLabel|Bevestig de wijziging door een wacht woord in te voeren|  
|WebAuthenticationSignupConfirmNotice|E-mail over registratie bevestiging is onderweg {0} . <BR/ \> Volg de instructies in het e-mail bericht om uw account te activeren. <BR/ \> als het e-mail bericht binnen enkele minuten niet in uw postvak wordt ontvangen, controleert u de map Ongewenste e-mail.|  
|WebAuthenticationSignupConfirmNoticeHeader|Uw account is gemaakt|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Het e-mail adres voor registratie bevestiging is opnieuw verzonden|  
|WebAuthenticationSignupConfirmNoticeTitle|Account gemaakt|  
|WebAuthenticationTokenRequiredErrorMessage|Het token is leeg|  
|WebAuthenticationUserAlreadyRegisteredNotice|Het lijkt erop dat er al een gebruiker met deze e-mail in het systeem is geregistreerd. Als u uw wacht woord bent verg eten, kunt u het herstellen of contact opnemen met ons ondersteunings team.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|De gebruiker is al geregistreerd|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Al geregistreerd|  
|ButtonLabelChangePassword|Wachtwoord wijzigen|  
|ButtonLabelChangeAccountInfo|Account gegevens wijzigen|  
|ButtonLabelCloseAccount|Account sluiten|  
|WebAuthenticationInvalidCaptchaErrorMessage|De ingevoerde tekst komt niet overeen met de tekst in de afbeelding. Probeer het opnieuw.|  
|ValidationErrorCredentialsInvalid|E-mail adres of wacht woord is ongeldig. Corrigeer de fouten en probeer het opnieuw.|  
|WebAuthenticationRequestIsNotValid|Aanvraag is niet geldig|  
|WebAuthenticationUserIsNotConfirm|Bevestig uw registratie voordat u zich aanmeldt.|  
|WebAuthenticationInvalidEmailFormated|E-mail adres is ongeldig: {0}|  
|WebAuthenticationUserNotFound|Gebruiker is niet gevonden|  
|WebAuthenticationTenantNotRegistered|Uw account behoort tot een Azure Active Directory-Tenant die niet is gemachtigd voor toegang tot deze portal.|  
|WebAuthenticationAuthenticationFailed|De verificatie is mislukt.|  
|WebAuthenticationGooglePlusNotEnabled|De verificatie is mislukt. Als u de toepassing hebt geautoriseerd, neemt u contact op met de beheerder om te controleren of de Google-verificatie correct is geconfigureerd.|  
|ValidationErrorAllowedTenantIsRequired|De toegestane Tenant is vereist|  
|ValidationErrorTenantIsNotValid|De Azure Active Directory Tenant ' {0} ' is niet geldig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Meld u aan met uw {0} account|  
|WebAuthenticationUserLimitNotice|Deze service heeft het maximum aantal toegestane gebruikers bereikt. Voer een `<a href="mailto:{0}"\>contact the administrator</a\>` upgrade van de service uit en schakel de gebruikers registratie opnieuw in.|  
|WebAuthenticationUserLimitNoticeHeader|Gebruikers registratie is uitgeschakeld|  
|WebAuthenticationUserLimitNoticeTitle|Gebruikers registratie is uitgeschakeld|  
|WebAuthenticationUserRegistrationDisabledNotice|De registratie van gebruikers is uitgeschakeld door de beheerder. Meld u aan met een externe ID-provider.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Gebruikers registratie is uitgeschakeld|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Gebruikers registratie is uitgeschakeld|  
|WebAuthenticationSignupPendingConfirmationNotice|Voordat we het maken van uw account kunnen volt ooien, moeten we uw e-mail adres verifiëren. Er is een e-mail bericht verzonden naar {0} . Volg de instructies in het e-mail bericht om uw account te activeren. Als het e-mail bericht niet binnen de komende minuten wordt ontvangen, controleert u de map Ongewenste e-mail.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Er is een niet-bevestigd account voor het e-mail adres gevonden {0} . U moet uw e-mail adres verifiëren om het maken van uw account te volt ooien. Er is een e-mail bericht verzonden naar {0} . Volg de instructies in het e-mail bericht om uw account te activeren. Als het e-mail bericht niet binnen de komende minuten wordt ontvangen, controleert u de map Ongewenste e-mail|  
|WebAuthenticationSignupConfirmationAlmostDone|Bijna klaar|  
|WebAuthenticationSignupConfirmationEmailSent|Er is een e-mail bericht verzonden naar {0} . Volg de instructies in het e-mail bericht om uw account te activeren. Als het e-mail bericht niet binnen de komende minuten wordt ontvangen, controleert u de map Ongewenste e-mail.|  
|WebAuthenticationEmailSentNotificationMessage|Het e-mail bericht is verzonden naar {0}|  
|WebAuthenticationNoAadTenantConfigured|Er is geen Azure Active Directory-Tenant geconfigureerd voor de service.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Ik ga akkoord met de `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` .|  
|TextblockUserRegistrationTermsProvided|Controleer `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Gebruiksvoorwaarden|  
|ValidationMessageConsentNotAccepted|U moet akkoord gaan met de gebruiks voorwaarden voordat u kunt door gaan.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Bent u uw wachtwoord vergeten?|  
|WebAuthenticationIfAdministrator|Als u een beheerder bent, moet u zich aanmelden `<a href="{0}"\>here</a\>` .|  
|WebAuthenticationNotAMember|Nog geen lid? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Mij onthouden op deze computer|  
|WebAuthenticationSigininWithPassword|Meld u aan met uw gebruikers naam en wacht woord|  
|WebAuthenticationSigninTitle|Aanmelden|  
|WebAuthenticationSignUpNow|Nu registreren|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleSignup|Aanmelden|  
|WebAuthenticationAlreadyAMember|Bent u al lid?|  
|WebAuthenticationCreateNewAccount|Een nieuw API Management-account maken|  
|WebAuthenticationSigninNow|Nu aanmelden|  
|ButtonLabelSignup|Aanmelden|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|Tekst|  
|----------|----------|  
|SubscriptionCancelConfirmation|Weet u zeker dat u dit abonnement wilt annuleren?|  
|SubscriptionRenewConfirmation|Weet u zeker dat u dit abonnement wilt vernieuwen?|  
|WebDevelopersManageSubscriptions|Abonnementen beheren|  
|WebDevelopersPrimaryKey|Primaire sleutel|  
|WebDevelopersRegenerateLink|Opnieuw genereren|  
|WebDevelopersSecondaryKey|Secundaire sleutel|  
|ButtonLabelShowKey|Weergeven|  
|ButtonLabelRenewSubscription|Verlengen|  
|WebDevelopersSubscriptionRequested|Aangevraagd op {0}|  
|WebDevelopersSubscriptionRequestedState|Aangevraagd|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Staat|  
|WebDevelopersUsageStatisticsLink|Analyse rapporten|  
|WebDevelopersYourSubscriptions|Uw abonnementen|  
|SubscriptionPropertyLabelRequestedDate|Aangevraagd op|  
|SubscriptionPropertyLabelStartedDate|Gestart op|  
|PageTitleRenameSubscription|Naam van abonnement wijzigen|  
|SubscriptionPropertyLabelName|Abonnementsnaam|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|Tekst|  
|----------|----------|  
|SectionHeadingCloseAccount|Wilt u uw account sluiten?|  
|PageTitleDeveloperProfile|Profiel|  
|ButtonLabelHideKey|Verbergen|  
|ButtonLabelRegenerateKey|Opnieuw genereren|  
|InformationMessageKeyWasRegenerated|Weet u zeker dat u deze sleutel opnieuw wilt genereren?|  
|ButtonLabelShowKey|Weergeven|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profiel bijwerken|  
|PageTitleUpdateProfile|Account gegevens bijwerken|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> Gebruikers  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Account gegevens wijzigen|  
|ButtonLabelChangePassword|Wachtwoord wijzigen|  
|ButtonLabelCloseAccount|Account sluiten|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Voornaam|  
|TextboxLabelEmailLastName|Achternaam|  
|TextboxLabelNotificationsSenderEmail|E-mail adres afzender|  
|TextboxLabelOrganizationName|Organisatienaam|  
|SubscriptionStateActive|Actief|  
|SubscriptionStateCancelled|Geannuleerd|  
|SubscriptionStateExpired|Verlopen|  
|SubscriptionStateRejected|Afgewezen|  
|SubscriptionStateRequested|Aangevraagd|  
|SubscriptionStateSuspended|Onderbroken|  
|DefaultSubscriptionNameTemplate|{0}  prijs|  
|SubscriptionNameTemplate|Ontwikkelaars toegang #{0}|  
|TextboxLabelSubscriptionName|Abonnementsnaam|  
|ValidationMessageSubscriptionNameRequired|De naam van het abonnement mag niet leeg zijn.|  
|ApiManagementUserLimitReached|Deze service heeft het maximum aantal toegestane gebruikers bereikt. Voer een upgrade uit naar een hogere prijs categorie.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Glyph-resources  
 API Management ontwikkelaars Portal sjablonen kunnen de glyphs van [Glyphicons uit Boots trap](https://getbootstrap.com/components/#glyphicons)gebruiken. Deze reeks glyphs bevat meer dan 250 glyphs in de lettertype notatie van de [Glyphicon](https://glyphicons.com/) Halflings ingesteld. Als u een glyph uit deze set wilt gebruiken, gebruikt u de volgende syntaxis.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Zie [Glyphicons van Boots trap](https://getbootstrap.com/components/#glyphicons)voor de volledige lijst met glyphs.

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
