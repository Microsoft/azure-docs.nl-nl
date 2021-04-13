---
title: Directe federatie met een id-provider voor B2B - Azure AD
description: Rechtstreeks federeren met een SAML- of WS-Fed-id-provider zodat gasten zich kunnen aanmelden bij uw Azure AD-apps
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
ms.openlocfilehash: 830119a5b3a7781e8b12e3d4df870f539a2cd63a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364903"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Directe federatie met AD FS en externe providers voor gastgebruikers (preview)

> [!NOTE]
>  Directe federatie is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt beschreven hoe u directe federatie met een andere organisatie in kunt stellen voor B2B-samenwerking. U kunt directe federatie instellen met elke organisatie waarvan de id-provider (IdP) het SAML 2.0- of WS-Fed-protocol ondersteunt.
Wanneer u directe federatie met de IdP van een partner in stelt, kunnen nieuwe gastgebruikers uit dat domein hun eigen door IdP beheerd organisatieaccount gebruiken om zich aan te melden bij uw Azure AD-tenant en met u samen te werken. Het is niet nodig dat de gastgebruiker een afzonderlijk Azure AD-account maakt.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Wanneer wordt een gastgebruiker geverifieerd met directe federatie?
Nadat u directe federatie met een organisatie hebt ingesteld, worden alle nieuwe gastgebruikers die u uitnodigt, geverifieerd met behulp van directe federatie. Het is belangrijk te weten dat het instellen van directe federatie de verificatiemethode niet wijzigt voor gastgebruikers die al een uitnodiging van u hebben ingewisseld. Hier volgen enkele voorbeelden:
 - Als gastgebruikers al uitnodigingen van u hebben ingewisseld en u vervolgens directe federatie met hun organisatie hebt ingesteld, blijven die gastgebruikers dezelfde verificatiemethode gebruiken die ze hebben gebruikt voordat u directe federatie in instellen.
 - Als u directe federatie met een partnerorganisatie in stelt en gastgebruikers uitnodigt, en vervolgens de partnerorganisatie later over stappen naar Azure AD, blijven de gastgebruikers die al uitnodigingen hebben ingewisseld, directe federatie gebruiken, zolang het directe federatiebeleid in uw tenant bestaat.
 - Als u directe federatie met een partnerorganisatie verwijdert, kunnen gastgebruikers die momenteel gebruikmaken van directe federatie, zich niet aanmelden.

In elk van deze scenario's kunt u de verificatiemethode van een gastgebruiker bijwerken door de [inwisselstatus opnieuw in te instellen.](reset-redemption-status.md)

Directe federatie is gekoppeld aan domeinnaamruimten, zoals contoso.com en fabrikam.com. Bij het instellen van een directe federatieconfiguratie met AD FS id-netwerkprovider of een id-netwerkprovider van derden, koppelen organisaties een of meer domeinnaamruimten aan deze IdP's. 

## <a name="end-user-experience"></a>Ervaring voor de eindgebruiker 
Met directe federatie melden gastgebruikers zich aan bij uw Azure AD-tenant met hun eigen organisatieaccount. Wanneer ze gedeelde resources openen en worden gevraagd zich aan te melden, worden directe federatiegebruikers omgeleid naar hun IdP. Nadat het aanmelden is geslaagd, worden ze teruggestuurd naar Azure AD om toegang te krijgen tot resources. De vernieuwingstokens van directe federatiegebruikers zijn 12 uur geldig, de standaardlengte voor het [vernieuwingstoken voor passthrough](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) in Azure AD. Als voor de federatie-IdP eenmalige aanmelding is ingeschakeld, krijgt de gebruiker eenmalige aanmelding te zien en wordt er na de eerste verificatie geen aanmeldingsprompt meer te zien.

## <a name="sign-in-endpoints"></a>Aanmeldings-eindpunten

Directe federatie guest users can now sign in to your multi-tenant or Microsoft first-party apps by using a [common endpoint](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (oftewel een algemene app-URL die uw tenantcontext niet bevat). Tijdens het aanmeldingsproces kiest de gastgebruiker **aanmeldingsopties** en selecteert vervolgens **Aanmelden bij een organisatie.** De gebruiker typen vervolgens de naam van uw organisatie en blijft zich aanmelden met zijn eigen referenties.

Directe federatie guest users can also use application endpoints that include your tenant information, bijvoorbeeld:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

U kunt ook directe federatie-gastgebruikers een directe koppeling naar een toepassing of resource geven door uw tenantgegevens op te nemen, bijvoorbeeld `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Beperkingen

### <a name="dns-verified-domains-in-azure-ad"></a>Met DNS geverifieerde domeinen in Azure AD
Het domein dat u wilt federeren met mag ***niet*** worden geverifieerd met DNS in Azure AD. U kunt directe federatie instellen met niet-door DNS geverifieerde (via e-mail geverifieerde of 'virale') Azure AD-tenants omdat deze niet zijn geverifieerd via DNS.

### <a name="authentication-url"></a>Verificatie-URL
Directe federatie is alleen toegestaan voor beleidsregels waarbij het domein van de verificatie-URL overeenkomt met het doeldomein of waarbij de verificatie-URL een van deze toegestane id-providers is (deze lijst kan worden gewijzigd):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Wanneer u bijvoorbeeld directe federatie instelt **voor fabrikam.com,** wordt de verificatie-URL `https://fabrikam.com/adfs` door de validatie geslaagd. Een host in hetzelfde domein geeft ook door, bijvoorbeeld `https://sts.fabrikam.com/adfs` . De verificatie-URL `https://fabrikamconglomerate.com/adfs` `https://fabrikam.com.uk/adfs` of voor hetzelfde domein wordt echter niet door geven.

### <a name="signing-certificate-renewal"></a>Verlenging van handtekeningcertificaat
Als u de metagegevens-URL opgeeft in de instellingen van de id-provider, vernieuwt Azure AD het handtekeningcertificaat automatisch wanneer het verloopt. Als het certificaat echter om een bepaalde reden vóór de verlooptijd wordt geruleerd of als u geen metagegevens-URL op geeft, kan het certificaat niet worden vernieuwd door Azure AD. In dit geval moet u het handtekeningcertificaat handmatig bijwerken.

### <a name="limit-on-federation-relationships"></a>Limiet voor federatierelaties
Momenteel worden maximaal 1000 federatierelaties ondersteund. Deze limiet omvat zowel [interne federaties](/powershell/module/msonline/set-msoldomainfederationsettings) als directe federaties.

### <a name="limit-on-multiple-domains"></a>Beperken tot meerdere domeinen
We bieden momenteel geen ondersteuning voor directe federatie met meerdere domeinen van dezelfde tenant.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan ik directe federatie instellen met een domein waarvoor een niet-beherende (via e-mail geverifieerde) tenant bestaat? 
Ja. Als het domein niet is geverifieerd en de tenant geen beheerdersovername heeft [ondergaan,](../enterprise-users/domains-admin-takeover.md)kunt u directe federatie met dat domein instellen. Niet-beherende of via e-mail geverifieerde tenants worden gemaakt wanneer een gebruiker een B2B-uitnodiging inwisselt of een selfserviceregistratie voor Azure AD uitvoert met behulp van een domein dat momenteel niet bestaat. U kunt directe federatie met deze domeinen instellen. Als u probeert directe federatie in te stellen met een dns-geverifieerd domein, in de Azure Portal of via PowerShell, ziet u een foutmelding.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Welke methode heeft voorrang als directe federatie en verificatie van een een time-time wachtwoordcode voor e-mail beide zijn ingeschakeld?
Wanneer directe federatie tot stand is gebracht met een partnerorganisatie, heeft deze voorrang op een een time-time wachtwoordcodeverificatie via e-mail voor nieuwe gastgebruikers van die organisatie. Als een gastgebruiker een uitnodiging heeft ingewisseld met behulp van een een time-time wachtwoordcodeverificatie voordat u directe federatie in stelt, blijft deze een een time-time wachtwoordcodeverificatie gebruiken. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Worden aanmeldingsproblemen door directe federatie opgelost vanwege een gedeeltelijk gesynchroniseerde tenancy?
Nee, de [e-mailfunctie voor een een time-time wachtwoordcode](one-time-passcode.md) moet in dit scenario worden gebruikt. Een gedeeltelijk gesynchroniseerde tenancy verwijst naar een Azure AD-tenant van een partner waarbij on-premises gebruikersidentiteiten niet volledig zijn gesynchroniseerd met de cloud. Een gast van wie de identiteit nog niet bestaat in de cloud, maar die probeert uw B2B-uitnodiging in te wisselen, kan zich niet aanmelden. Met de functie voor een een time-wachtwoordcode kan deze gast zich aanmelden. De functie directe federatie heeft betrekking op scenario's waarbij de gast een eigen door IdP beheerd organisatieaccount heeft, maar de organisatie helemaal geen Azure AD-aanwezigheid heeft.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Zodra Directe federatie is geconfigureerd met een organisatie, moet elke gast worden verzonden en een afzonderlijke uitnodiging inwisselen?
Als u directe federatie instelt, wordt de verificatiemethode niet gewijzigd voor gastgebruikers die al een uitnodiging van u hebben ingewisseld. U kunt de verificatiemethode van een gastgebruiker bijwerken door [de inwisselstatus opnieuw in te instellen.](reset-redemption-status.md)
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Stap 1: de id-provider van de partnerorganisatie configureren
Eerst moet uw partnerorganisatie de id-provider configureren met de vereiste claims en relying partyvertrouwensrelatie. 

> [!NOTE]
> Om te laten zien hoe u een id-provider configureert voor directe federatie, gebruiken we Active Directory Federation Services (AD FS) als voorbeeld. Zie het artikel Configure direct federation with AD FS (Directe federatie configureren met [AD FS)](direct-federation-adfs.md)voor voorbeelden van het configureren van AD FS als een SAML 2.0- of WS-Fed-id-provider ter voorbereiding op directe federatie.

### <a name="saml-20-configuration"></a>SAML 2.0-configuratie

Azure AD B2B kan worden geconfigureerd om te federeren met id-providers die gebruikmaken van het SAML-protocol met specifieke vereisten die hieronder worden vermeld. Zie Use  [a SAML 2.0 Identity Provider (IdP) for Single Sign-On (Een SAML 2.0 Identity Provider (IdP) gebruiken voor](../hybrid/how-to-connect-fed-saml-idp.md)meer informatie over het instellen van een vertrouwensrelatie tussen uw SAML-id-provider en Azure AD.  

> [!NOTE]
> Het doeldomein voor directe federatie mag niet met DNS zijn geverifieerd in Azure AD. Het domein van de verificatie-URL moet overeenkomen met het doeldomein of het moet het domein van een toegestane id-provider zijn. Zie de [sectie Beperkingen](#limitations) voor meer informatie. 

#### <a name="required-saml-20-attributes-and-claims"></a>Vereiste SAML 2.0-kenmerken en -claims
De volgende tabellen tonen vereisten voor specifieke kenmerken en claims die moeten worden geconfigureerd bij de id-provider van derden. Als u directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het SAML 2.0-antwoord van de id-provider. Deze kenmerken kunnen worden geconfigureerd door te koppelen aan het XML-bestand van de online-beveiliging tokenservice of door ze handmatig in te geven.

Vereiste kenmerken voor het SAML 2.0-antwoord van de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De URI van de vergever van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |


Vereiste claims voor het SAML 2.0-token dat is uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|NameID-indeling     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed configureren 
Azure AD B2B kan worden geconfigureerd om te federeren met id-providers die gebruikmaken van het WS-Fed-protocol met een aantal specifieke vereisten, zoals hieronder wordt vermeld. Momenteel zijn de twee WS-Fed-providers getest op compatibiliteit met Azure AD AD FS en Shibboleth. Voor meer informatie over het tot stand brengen van een vertrouwensrelatie tussen een WS-Fed-compatibele provider met Azure AD, zie de "STS Integration Paper met WS-protocollen" beschikbaar in de [Azure AD Identity Provider Compatibility Docs](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Het doeldomein voor directe federatie mag niet met DNS zijn geverifieerd in Azure AD. Het domein van de verificatie-URL moet overeenkomen met het doeldomein of het domein van een toegestane id-provider. Zie de [sectie Beperkingen](#limitations) voor meer informatie. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Vereiste WS-Fed en claims

De volgende tabellen tonen vereisten voor specifieke kenmerken en claims die moeten worden geconfigureerd bij de externe WS-Fed id-provider. Als u directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het WS-Fed van de id-provider. Deze kenmerken kunnen worden geconfigureerd door een koppeling te maken naar het XML-bestand van de online beveiliging tokenservice of door ze handmatig in te geven.

Vereiste kenmerken in het WS-Fed bericht van de IdP:
 
|Kenmerk  |Waarde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De URI van de vergever van de partner-IdP, bijvoorbeeld `http://www.example.com/exk10l6w90DHM0yi...`         |

Vereiste claims voor het WS-Fed token dat is uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|OnveranderbareID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Stap 2: directe federatie configureren in Azure AD 
Vervolgens configureert u federatie met de id-provider die is geconfigureerd in stap 1 in Azure AD. U kunt de Azure AD-portal of PowerShell gebruiken. Het kan 5-10 minuten duren voordat het directe federatiebeleid van kracht wordt. Probeer gedurende deze periode geen uitnodiging in te wisselen voor het directe federatiedomein. De volgende kenmerken zijn vereist:
- Issuer URI of partner IdP
- Passieve verificatie-eindpunt van partner-IdP (alleen https wordt ondersteund)
- Certificaat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Directe federatie configureren in de Azure AD-portal

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **External Identities**  >  **Alle id-providers.**
3. Selecteer **New SAML/WS-Fed IdP**.

    ![Schermopname van de knop voor het toevoegen van een nieuwe SAML of WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Selecteer op **de pagina New SAML/WS-Fed IdP** onder **Identity provider protocol** de optie **SAML** of **WS-FED.**

    ![Schermopname van de parseknop op de pagina SAML WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Voer de domeinnaam van uw partnerorganisatie in. Dit is de doeldomeinnaam voor directe federatie
6. U kunt een metagegevensbestand uploaden om metagegevens te vullen. Als u ervoor kiest om metagegevens handmatig in te voeren, voert u de volgende gegevens in:
   - Domeinnaam van partner-IdP
   - Entiteits-id van partner-IdP
   - Passieve requestor-eindpunt van partner-IdP
   - Certificaat
   > [!NOTE]
   > Url van metagegevens is optioneel, maar dit wordt ten zeerste aangeraden. Als u de metagegevens-URL op geeft, kan Azure AD het handtekeningcertificaat automatisch vernieuwen wanneer het verloopt. Als het certificaat om een bepaalde reden vóór de verlooptijd wordt geroteerd of als u geen metagegevens-URL op geeft, kan het certificaat niet worden vernieuwd door Azure AD. In dit geval moet u het handtekeningcertificaat handmatig bijwerken.

7. Selecteer **Opslaan**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Directe federatie configureren in Azure AD met behulp van PowerShell

1. Installeer de nieuwste versie van de Azure AD PowerShell voor Graph-module[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Als u gedetailleerde stappen nodig hebt, bevat de quickstart voor het toevoegen van een gastgebruiker de sectie [Install the latest AzureADPreview module](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
1. Meld u bij de aanmeldingsprompt aan met het beheerde globale beheerdersaccount. 
2. Voer de volgende opdrachten uit en vervang de waarden uit het bestand met federatiemetagegevens. Voor AD FS Server en Okta is het federatiebestand federationmetadata.xml, bijvoorbeeld: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Stap 3: Directe federatie testen in Azure AD
Test nu uw directe federatie-installatie door een nieuwe B2B-gastgebruiker uit te nodigen. Zie Add [Azure AD B2B collaboration users in the Azure Portal (Azure AD B2B-samenwerkingsgebruikers toevoegen in de Azure Portal).](add-users-administrator.md)
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hoe kan ik directe federatierelatie bewerken?

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **External Identities**.
3. Selecteer **Alle id-providers**
4. Selecteer de provider **onder SAML/WS-Fed-id-providers.**
5. Werk in het detailvenster van de id-provider de waarden bij.
6. Selecteer **Opslaan**.


## <a name="how-do-i-remove-direct-federation"></a>Hoe kan ik directe federatie verwijderen?
U kunt uw directe federatie-instellingen verwijderen. Als u dat wel doet, kunnen directe federatie-gastgebruikers die hun uitnodigingen al hebben ingewisseld, zich niet aanmelden. U kunt ze echter opnieuw toegang geven tot uw resources door de [inwisselstatus opnieuw in te stellen.](reset-redemption-status.md) Directe federatie met een id-provider in de Azure AD-portal verwijderen:

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. 
2. Selecteer **External Identities**.
3. Selecteer **Alle id-providers.**
4. Selecteer de id-provider en selecteer vervolgens **Verwijderen.** 
5. Selecteer **Ja** om het verwijderen te bevestigen. 

Directe federatie met een id-provider verwijderen met behulp van PowerShell:
1. Installeer de nieuwste versie van de Azure AD PowerShell voor Graph-module[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Voer de volgende opdracht uit: 
   ```powershell
   Connect-AzureAD
   ```
3. Meld u bij de aanmeldingsprompt aan met het beheerde globale beheerdersaccount. 
4. Voer de volgende opdracht in:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [inwisselen van uitnodigingen](redemption-experience.md) wanneer externe gebruikers zich aanmelden bij verschillende id-providers.
