---
title: Directe Federatie met een id-provider voor B2B-Azure AD
description: Direct met een SAML-of WS-Fed-ID-provider communiceren zodat gasten zich kunnen aanmelden bij uw Azure AD-apps
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8ac540459ad79a8980542254cc15518959b5c0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552288"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Directe Federatie met AD FS en providers van derden voor gast gebruikers (preview-versie)

> [!NOTE]
>  Directe Federatie is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt beschreven hoe u directe Federatie kunt instellen met een andere organisatie voor B2B-samen werking. U kunt directe Federatie instellen met elke organisatie waarvan de ID-provider (IdP) het SAML 2,0-of WS-Fed-protocol ondersteunt.
Wanneer u directe Federatie instelt met de IdP van een partner, kunnen nieuwe gast gebruikers uit dat domein hun eigen door IdP beheerd organisatie account gebruiken om zich aan te melden bij uw Azure AD-Tenant en samen met u aan de slag te gaan. De gast gebruiker hoeft geen afzonderlijk Azure AD-account te maken.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Wanneer is een gast gebruiker geverifieerd met directe Federatie?
Nadat u directe Federatie hebt ingesteld met een organisatie, worden nieuwe gast gebruikers die u uitnodigt, geverifieerd met behulp van directe Federatie. Het is belang rijk te weten dat het instellen van directe Federatie de verificatie methode niet wijzigt voor gast gebruikers die al een uitnodiging van u hebben ingewisseld. Hier volgen enkele voorbeelden:
 - Als gast gebruikers al uitnodigingen van u hebben ingewisseld en u vervolgens direct Federation hebt ingesteld met hun organisatie, blijven deze gast gebruikers dezelfde verificatie methode gebruiken die ze hebben gebruikt voordat u directe Federatie hebt ingesteld.
 - Als u directe Federatie hebt ingesteld met een partner organisatie en gast gebruikers uitnodigt en vervolgens de partner organisatie later naar Azure AD verplaatst, blijven de gast gebruikers die al ingedeelde uitnodigingen hebben ingewisseld, gebruikmaken van directe Federatie, zolang het directe Federatie beleid in uw Tenant bestaat.
 - Als u directe Federatie met een partner organisatie verwijdert, kunnen gast gebruikers die momenteel direct Federation gebruiken, zich niet aanmelden.

In een van deze scenario's kunt u de verificatie methode van een gast gebruiker bijwerken door [de status van hun inwisseling opnieuw](reset-redemption-status.md)in te stellen.

Directe Federatie is gekoppeld aan domein naam ruimten, zoals contoso.com en fabrikam.com. Bij het tot stand brengen van een directe Federatie configuratie met AD FS of een IdP van derden koppelen organisaties een of meer domein naam ruimten aan deze id. 

## <a name="end-user-experience"></a>Ervaring voor de eindgebruiker 
Met directe Federatie melden gast gebruikers zich aan bij uw Azure AD-Tenant met behulp van hun eigen organisatie account. Wanneer ze toegang krijgen tot gedeelde bronnen en worden gevraagd om zich aan te melden, worden directe Federatie gebruikers omgeleid naar hun IdP. Nadat het aanmelden is geslaagd, worden deze geretourneerd naar Azure AD om toegang te krijgen tot resources. De vernieuwings tokens van de directe Federatie gebruikers zijn 12 uur geldig, de [standaard lengte voor het passthrough-vernieuwings token](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in azure AD. Als voor de federatieve IdP SSO is ingeschakeld, ondervindt de gebruiker SSO en wordt er geen aanmeldings prompt weer gegeven na de eerste verificatie.

## <a name="sign-in-endpoints"></a>Aanmeld-eind punten

Gebruikers met een directe Federatie gast kunnen zich nu aanmelden bij uw apps voor meerdere tenants of micro soft-toepassingen met behulp van een [gemeen schappelijk eind punt](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (met andere woorden, een algemene app-URL die uw Tenant context niet bevat). Tijdens het aanmeldings proces kiest de gast gebruiker de **aanmeldings opties** en selecteert vervolgens **Aanmelden bij een organisatie**. De gebruiker typt vervolgens de naam van uw organisatie en blijft aanmelden met behulp van hun eigen referenties.

Gebruikers met een directe Federatie gast kunnen ook toepassings eindpunten gebruiken die uw Tenant gegevens bevatten, bijvoorbeeld:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

U kunt ook directe Federatie-gast gebruikers een directe koppeling naar een toepassing of resource geven door bijvoorbeeld uw Tenant gegevens op te nemen `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Beperkingen

### <a name="dns-verified-domains-in-azure-ad"></a>Door DNS geverifieerde domeinen in azure AD
Het domein waarmee u wilt communiceren, mag ***niet*** door DNS worden geverifieerd in azure AD. U mag directe Federatie met niet-beheerde (e-mail berichten geverifieerde of ' virale ') Azure AD-tenants instellen, omdat deze niet zijn geverifieerd voor DNS.

### <a name="authentication-url"></a>Verificatie-URL
Directe Federatie is alleen toegestaan voor beleids regels waarbij het domein van de authenticatie-URL overeenkomt met het doel domein of waarbij de verificatie-URL een van deze toegestane id-providers is (deze lijst kan worden gewijzigd):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Wanneer u bijvoorbeeld directe Federatie voor **fabrikam.com** instelt, wordt de verificatie-URL `https://fabrikam.com/adfs` door gegeven aan de validatie. Er wordt bijvoorbeeld ook een host in hetzelfde domein door gegeven `https://sts.fabrikam.com/adfs` . De verificatie-URL `https://fabrikamconglomerate.com/adfs` of `https://fabrikam.com.uk/adfs` voor hetzelfde domein wordt echter niet door gegeven.

### <a name="signing-certificate-renewal"></a>Certificaat vernieuwing ondertekenen
Als u de meta gegevens-URL in de instellingen van de identiteits provider opgeeft, wordt het handtekening certificaat door Azure AD automatisch vernieuwd wanneer het verloopt. Als het certificaat echter om een of andere reden vóór de verloop tijd wordt geroteerd, of als u geen meta gegevens-URL opgeeft, kan Azure AD deze niet vernieuwen. In dit geval moet u het handtekening certificaat hand matig bijwerken.

### <a name="limit-on-federation-relationships"></a>Limiet voor Federatie relaties
Momenteel wordt een maximum van 1.000 Federatie relaties ondersteund. Deze limiet omvat zowel [interne](/powershell/module/msonline/set-msoldomainfederationsettings) als directe overkoepelende organisaties.

### <a name="limit-on-multiple-domains"></a>Limiet voor meerdere domeinen
Er wordt momenteel geen ondersteuning geboden voor directe Federatie met meerdere domeinen van dezelfde Tenant.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan ik directe Federatie instellen met een domein waarvoor een niet-beheerde Tenant (geverifieerd door een e-mail bericht) bestaat? 
Ja. Als het domein niet is geverifieerd en de Tenant geen [beheerder](../enterprise-users/domains-admin-takeover.md)heeft ondergaan, kunt u direct Federatie met dat domein instellen. Niet-beheerd of e-mail berichten-gecontroleerd, tenants worden gemaakt wanneer een gebruiker een B2B-uitnodiging inwisselt of een self-service registratie voor Azure AD uitvoert met behulp van een domein dat nog niet bestaat. U kunt direct Federation instellen met deze domeinen. Als u probeert om direct Federatie in te stellen met een domein met DNS-verificatie, in de Azure Portal of via Power shell, ziet u een fout melding.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Als voor de directe Federatie en e-mail eenmalige wachtwoord code verificatie is ingeschakeld, welke methode voor rang heeft?
Wanneer directe Federatie wordt ingesteld met een partner organisatie, heeft dit prioriteit boven de e-mail verificatie voor eenmalige authenticatie voor nieuwe gast gebruikers van die organisatie. Als een gast gebruiker een uitnodiging heeft ingewisseld met verificatie met eenmalige wachtwoord code voordat u direct Federatie instelt, blijven ze gebruikmaken van verificatie met een eenmalige wachtwoord code. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Worden er problemen met het aanmelden van direct Federation-adressen veroorzaakt door een gedeeltelijk gesynchroniseerde pacht?
Nee, de functie voor [eenmalige e-mail wachtwoord code](one-time-passcode.md) moet in dit scenario worden gebruikt. Een ' gedeeltelijk gesynchroniseerde pacht ' verwijst naar een Azure AD-Tenant van de partner, waarbij on-premises gebruikers identiteiten niet volledig worden gesynchroniseerd met de Cloud. Een gast waarvan de identiteit nog niet bestaat in de Cloud, maar die probeert uw B2B-uitnodiging in te wisselen, kan zich niet aanmelden. Met de functie voor eenmalige wachtwoord code kan deze gast zich aanmelden. De functie directe Federatie biedt een oplossing voor scenario's waarbij de gast een eigen IdP organisatie account heeft, maar de organisatie helemaal geen Azure AD-aanwezigheid heeft.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Zodra direct Federation is geconfigureerd met een organisatie, moet elke gast een individuele uitnodiging verzenden en inwisselen?
Door directe Federatie in te stellen, wordt de verificatie methode niet gewijzigd voor gast gebruikers die van u al een uitnodiging hebben ingewisseld. U kunt de verificatie methode van een gast gebruiker bijwerken door [de status van hun inwisseling opnieuw](reset-redemption-status.md)in te stellen.
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Stap 1: de ID-provider van de partner organisatie configureren
Eerst moet uw partner organisatie hun ID-provider configureren met de vereiste claims en Relying Party-vertrouwens relaties. 

> [!NOTE]
> Om te laten zien hoe u een id-provider voor directe Federatie kunt configureren, gebruiken we Active Directory Federation Services (AD FS) als voor beeld. Zie het artikel [direct Federatie configureren met AD FS](direct-federation-adfs.md), dat voor beelden bevat van het configureren van AD FS als een SAML 2,0-of WS-Fed-ID-provider in voor bereiding op directe Federatie.

### <a name="saml-20-configuration"></a>SAML 2,0-configuratie

Azure AD B2B kan worden geconfigureerd om te communiceren met id-providers die het SAML-protocol gebruiken met specifieke vereisten die hieronder worden weer gegeven. Zie  [een saml 2,0-ID-provider (IDP) gebruiken voor eenmalige aanmelding](../hybrid/how-to-connect-fed-saml-idp.md)voor meer informatie over het instellen van een vertrouwens relatie tussen uw SAML-ID-provider en Azure AD.  

> [!NOTE]
> Het doel domein voor directe Federatie mag niet worden geverifieerd door DNS op Azure AD. Het domein van de verificatie-URL moet overeenkomen met het doel domein of het moet het domein zijn van een toegestane ID-provider. Zie de sectie [beperkingen](#limitations) voor meer informatie. 

#### <a name="required-saml-20-attributes-and-claims"></a>Vereiste SAML 2,0-kenmerken en claims
In de volgende tabellen worden de vereisten voor specifieke kenmerken en claims weer gegeven die moeten worden geconfigureerd bij de ID-provider van derden. Als u directe Federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het SAML 2,0-antwoord van de ID-provider. Deze kenmerken kunnen worden geconfigureerd door te koppelen aan het XML-bestand van de online beveiligings token service of door ze hand matig in te voeren.

Vereiste kenmerken voor het SAML 2,0-antwoord van de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgevers-URI van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |


Vereiste claims voor het SAML 2,0-token dat is uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|NameID-indeling     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed configuratie 
Azure AD B2B kan worden geconfigureerd om te communiceren met id-providers die het WS-Fed-protocol gebruiken met enkele specifieke vereisten zoals hieronder wordt weer gegeven. Op dit moment zijn de twee WS-Fed providers getest op compatibiliteit met Azure AD AD FS en shibboleth. Zie voor meer informatie over het instellen van een Relying Party-vertrouwens relatie tussen een WS-Fed compatibele provider met Azure AD het document ' STS-integratie met WS-protocollen ' dat beschikbaar is in de [compatibiliteits documenten voor de Azure AD-identiteits provider](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Het doel domein voor directe Federatie mag niet worden geverifieerd door DNS op Azure AD. Het domein van de verificatie-URL moet overeenkomen met het doel domein of het domein van een toegestane ID-provider. Zie de sectie [beperkingen](#limitations) voor meer informatie. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Vereiste WS-Fed kenmerken en claims

In de volgende tabellen worden de vereisten voor specifieke kenmerken en claims weer gegeven die moeten worden geconfigureerd bij de WS-Fed-ID-provider van derden. Als u directe Federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het WS-Fed bericht van de identiteits provider. Deze kenmerken kunnen worden geconfigureerd door te koppelen aan het XML-bestand van de online beveiligings token service of door ze hand matig in te voeren.

Vereiste kenmerken in het WS-Fed bericht van de IdP:
 
|Kenmerk  |Waarde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgevers-URI van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |

Vereiste claims voor het WS-Fed token dat is uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Stap 2: direct Federatie configureren in azure AD 
Vervolgens configureert u Federatie met de ID-provider die is geconfigureerd in stap 1 van Azure AD. U kunt de Azure AD-portal of Power shell gebruiken. Het kan 5-10 minuten duren voordat het directe Federatie beleid van kracht wordt. Probeer tijdens deze periode niet een uitnodiging voor het directe Federatie domein in te wisselen. De volgende kenmerken zijn vereist:
- URI van de uitgever van de partner IdP
- Passief verificatie-eind punt van partner IdP (alleen https wordt ondersteund)
- Certificaat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Direct Federatie configureren in de Azure AD-Portal

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**  >  **alle id-providers**.
3. Selecteer **nieuwe SAML/WS-IDP**.

    ![Scherm afbeelding met de knop voor het toevoegen van een nieuwe SAML-of WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Selecteer op de **nieuwe IDP-pagina voor SAML/WS-** inschakeling onder **ID-provider protocol** **SAML** of **WS-voeder**.

    ![Scherm opname van de knop parseren op de pagina met het SAML-of WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Voer de domein naam van uw partner organisatie in, die de doel domeinnaam voor directe Federatie is
6. U kunt een meta gegevensbestand uploaden om details van meta gegevens in te vullen. Als u ervoor kiest om meta gegevens hand matig in te voeren, voert u de volgende gegevens in:
   - De domein naam van de partner IdP
   - Entiteit-ID van partner IdP
   - Passief eind punt van de partner IdP
   - Certificaat
   > [!NOTE]
   > Meta gegevens-URL is optioneel, maar wordt aanbevolen. Als u de meta gegevens-URL opgeeft, kan Azure AD het handtekening certificaat automatisch vernieuwen wanneer het verloopt. Als het certificaat om de een of andere reden vóór de verloop tijd wordt geroteerd of als u geen meta gegevens-URL opgeeft, kan Azure AD deze niet vernieuwen. In dit geval moet u het handtekening certificaat hand matig bijwerken.

7. Selecteer **Opslaan**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Direct Federatie configureren in azure AD met behulp van Power shell

1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Als u gedetailleerde stappen nodig hebt, bevat de Snelstartgids voor het toevoegen van een gast gebruiker de sectie [de meest recente AzureADPreview-module installeren](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
1. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account. 
2. Voer de volgende opdrachten uit en vervang de waarden uit het bestand met federatieve meta gegevens. Voor AD FS server en Okta is het Federatie bestand federationmetadata.xml, bijvoorbeeld: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Stap 3: direct Federatie testen in azure AD
Test nu uw directe Federatie-installatie door een nieuwe B2B-gast gebruiker uit te nodigen. Zie [Azure AD B2B-samenwerkings gebruikers toevoegen in de Azure Portal](add-users-administrator.md)voor meer informatie.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Een directe Federatie relatie Hoe kan ik bewerken?

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**.
3. **Alle id-providers** selecteren
4. Selecteer de provider onder **SAML/WS-id-providers**.
5. Werk de waarden bij in het detail venster van de identiteits provider.
6. Selecteer **Opslaan**.


## <a name="how-do-i-remove-direct-federation"></a>Direct Federatie Hoe kan ik verwijderen?
U kunt uw directe Federatie-instellingen verwijderen. Als u dit doet, kunnen gebruikers met een directe Federatie gast die hun uitnodigingen al hebben ingewisseld, zich niet aanmelden. Maar u kunt ze ook weer toegang geven tot uw resources door [de status van hun inwisseling](reset-redemption-status.md)opnieuw in te stellen. Directe Federatie verwijderen met een id-provider in de Azure AD-portal:

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **externe identiteiten**.
3. Selecteer **alle id-providers**.
4. Selecteer de ID-provider en selecteer vervolgens **verwijderen**. 
5. Selecteer **Ja** om het verwijderen te bevestigen. 

Directe Federatie met een id-provider verwijderen met behulp van Power shell:
1. Installeer de nieuwste versie van de Azure AD Power shell for Graph-module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
3. Meld u bij de aanmeldings prompt aan met het Managed Global Administrator-account. 
4. Voer de volgende opdracht in:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de uitbesteings [ervaring](redemption-experience.md) wanneer externe gebruikers zich aanmelden met verschillende id-providers.
