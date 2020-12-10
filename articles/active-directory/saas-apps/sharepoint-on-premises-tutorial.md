---
title: 'Zelfstudie: Azure Active Directory-integratie met SharePoint on-premises | Microsoft Docs'
description: Informatie over de configuratie van eenmalige aanmelding tussen Azure Active Directory en SharePoint on-premises.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Zelfstudie: Integratie van eenmalige aanmelding in Azure Active Directory met SharePoint on-premises

In deze zelfstudie leert u hoe u SharePoint on-premises integreert met Azure Active Directory (Azure AD). Wanneer u SharePoint on-premises integreert met Azure AD, kunt u:

* Bepaal in Azure AD wie toegang heeft tot SharePoint on-premises.
* Schakel in dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SharePoint on-premises.
* Beheer uw accounts in Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met SharePoint on-premises, hebt u deze items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een SharePoint 2013-farm of nieuwer.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding (SSO) van Azure AD configureren en testen. Gebruikers van Azure AD hebben toegang tot uw SharePoint on-premises.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Bedrijfstoepassingen maken in Azure Portal

Voor het configureren van de integratie van SharePoint on-premises in Azure AD, moet u SharePoint on-premises uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

Om SharePoint on-premises toe te voegen vanuit de galerie:

1. Selecteer in het meest linkse deelvenster in Azure Portal de optie **Azure Active Directory**.

   > [!NOTE]
   > Als het element niet beschikbaar is, kunt u het ook openen via de koppeling **Alle services** bovenaan in het meest linkse deelvenster. In het volgende overzicht vindt u de **Azure Active Directory**-koppeling in de sectie **Identiteit**. U kunt ook zoeken met behulp van het filtervak.

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

1. Voer in het zoekvak **SharePoint on-premises** in. Selecteer **SharePoint on-premises** in het resultatenvenster.

    <kbd>![SharePoint on-premises in de lijst met resultaten](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Geef een naam op voor uw exemplaar van SharePoint on-premises en klik op **Toevoegen** om de toepassing toe te voegen.

1. Selecteer in de nieuwe bedrijfstoepassing **Eigenschappen** en controleer de waarde voor **Gebruikerstoewijzing vereist?** .

   <kbd>![Gebruikerstoewijzing vereist?-wisselknop](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   In dit scenario is de waarde ingesteld op **Nee**.

## <a name="configure-and-test-azure-ad"></a>Azure AD configureren en testen

In deze sectie configureert u eenmalige aanmelding in Azure AD met SharePoint on-premises. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SharePoint on-premises.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met SharePoint on-premises, gaat u als volgt te werk:

- [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
- [Configureer SharePoint on-premises](#configure-sharepoint-on-premises) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Maak een Azure AD-testgebruiker in het Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal) om een nieuwe gebruiker te maken in Azure AD voor eenmalige aanmelding.
- [Maak een Azure AD-beveiligingsgroep in het Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal) om een nieuwe beveiligingsgroep te maken in Azure AD voor eenmalige aanmelding.
- [Geef toestemming aan een Azure AD-account in SharePoint on-premises](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) om toestemmingen te geven aan een Azure AD-gebruiker.
- [Geef toestemming aan een Azure AD-groep in SharePoint on-premises](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) om toestemmingen te geven aan een Azure AD-groep.
- [Verleen toegang tot SharePoint on-premises aan een gastaccount in de Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) om toestemmingen te geven aan een gastaccount in Azure AD voor SharePoint on-premises.
- [Stel de vertrouwde id-provider in voor verschillende webtoepassingen](#configure-the-trusted-identity-provider-for-multiple-web-applications) om dezelfde vertrouwde id-provider te gebruiken voor verschillende webtoepassingen.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u eenmalige aanmelding van Azure AD in de Azure Portal inschakelen.

Om eenmalige aanmelding van Azure AD te configureren met SharePoint on-premises:

1. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de naam van de bedrijfstoepassing die u eerder heeft aangemaakt en vervolgens **Eenmalige aanmelding**.

1. Selecteer de **SAML**-modus in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** om eenmalige aanmelding in te schakelen.
 
1. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

1. Voer deze stappen uit in het gedeelte **Standaard SAML-configuratie**:

    ![Informatie over eenmalige aanmelding bij het SharePoint on-premises-domein en -URL's](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Voer in het vak **Id** een URL in met behulp van dit patroon: `urn:<sharepointFarmName>:<federationName>`.

    1. Voer in het vak **Antwoord-URL** een URL in met behulp van dit patroon: `https://<YourSharePointSiteURL>/_trust/`.

    1. Voer in het vak **Aanmeldings-URL** een URL in met dit patroon: `https://<YourSharePointSiteURL>/`.
    1. Selecteer **Opslaan**.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Kopieer in de sectie **SharePoint on-premises instellen** de juiste URL('s) op basis van wat u nodig hebt:
    
    - **Aanmeldings-URL**
    
        Kopieer de aanmeldings-URL en vervang **/saml2** op het einde door **/wsfed-** , zodat het eruitziet als https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed. (Deze URL is niet correct.)

    - **Azure AD-id**
    - **Afmeldings-URL**

    > [!NOTE]
    > Deze URL kan niet gebruikt worden in SharePoint. U moet **/saml2** vervangen door **/wsfed**. De SharePoint on-premises-toepassing maakt gebruik van een SAML 1.1-token, zodat Azure AD een WS Fed-aanvraag verwacht van de SharePoint-server. Na verificatie wordt het SAML 1.1-token uitgegeven.

### <a name="configure-sharepoint-on-premises"></a>SharePoint on-premises configureren

1. Een nieuwe vertrouwde id-provider maken in SharePoint Server 2016.

    Meld u aan bij de server voor SharePoint en open de SharePoint Management Shell. Vul de waarden in:
    - **$realm** is de id-waarde van de sectie SharePoint on-premises-domein en -URL's in de Azure Portal.
    - **$wsfedurl** is de URL van de SSO-service.
    - **$filepath** is het bestandpad waar u het certificaatbestand naar heeft gedownload vanuit de Azure Portal.

    Voer de volgende opdrachten uit om een nieuwe vertrouwde id-provider in te stellen.

    > [!TIP]
    > Zie [SharePoint PowerShell](/powershell/sharepoint/overview) als u geen ervaring hebt met PowerShell of meer wilt weten over hoe PowerShell werkt.


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. Schakel de vertrouwde id-provider voor uw toepassing in.

    1. Navigeer in **Centraal beheer** naar **Webtoepassing beheren** en selecteer de webtoepassing die u wilt beveiligen met Azure AD.

    1. Selecteer op het lint **Verificatieproviders** en kies de zone die u wilt gebruiken.

    1. Selecteer **Vertrouwde id-provider** en selecteer de id-provider die u zojuist hebt geregistreerd met de naam *AzureAD*.

    1. Selecteer **OK**.

    ![Uw verificatieprovider configureren](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Een Azure AD-testgebruiker maken in Azure Portal

Het doel van deze sectie is om in de Azure-portal een testgebruiker te maken.

1. Selecteer in het meest linkse deelvenster in Azure Portal de optie **Azure Active Directory**. Selecteer **Beheren** in het deelvenster **Gebruikers**.

1. Selecteer **Alle gebruikers** > **Nieuwe gebruiker** boven aan het scherm.

1. Selecteer **Gebruiker maken** en volg deze stappen bij de gebruikerseigenschappen. U kunt mogelijk gebruikers aanmaken in uw Azure AD met behulp van uw tenant-achtervoegsel of een geverifieerd domein. 

    1. Voer in het vak **Naam** de gebruikersnaam in. We hebben **TestUser** gebruikt.
  
    1. Voer in het vak **Gebruikersnaam** `TestUser@yourcompanydomain.extension` in. In dit voorbeeld ziet u `TestUser@contoso.com`.

       ![Het dialoogvenster Gebruiker](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die verschijnt in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

    1. U kunt de site nu delen met TestUser@contoso.com en deze gebruiker toegang geven.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Een Azure AD-beveiligingsgroep maken in Azure Portal

1. Selecteer **Azure Active Directory** > **Groepen**.

1. Selecteer **Nieuwe groep**.

1. Vul de vakken **Groepstype**, **Groepsnaam**, **Groepsbeschrijving** en **Lidmaatschapstype** in. Gebruik de pijlen om leden te selecteren en zoek of selecteer de leden die u wilt toevoegen aan de groep. Kies **Selecteren** om de geselecteerde leden toe te voegen en selecteer vervolgens **Maken**.

![Een Azure AD-beveiligingsgroep maken](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Geef toestemming aan een Azure AD-account in SharePoint on-premises

Als u een Azure AD-gebruiker toegang wilt verlenen in SharePoint on-premises, deelt u de siteverzameling of voegt u de Azure AD-gebruiker toe aan een van de groepen van de siteverzameling. Gebruikers kunnen zich nu aanmelden bij SharePoint 201X met identiteiten van Azure AD, maar er zijn nog steeds mogelijkheden om de gebruikerservaring te verbeteren. Wanneer u bijvoorbeeld zoekt naar een gebruiker, worden meerdere zoekresultaten weergegeven in de personenkiezer. Er is een zoekresultaat voor elk van de claimtypen die worden gemaakt in de claimtoewijzing. Als u een gebruiker wilt kiezen met de personenkiezer, moet u de gebruikersnaam exact invoeren en het resultaat van de **naam** claim kiezen.

![Zoekresultaten van claims](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Er wordt geen validatie uitgevoerd op de waarden die u zoekt, wat tot spelfouten kan leiden of ervoor kan zorgen dat gebruikers per ongeluk het verkeerde claimtype kiezen. In deze situatie hebben gebruikers mogelijk geen toegang tot resources.

De open-sourceoplossing [AzureCP](https://yvand.github.io/AzureCP/) biedt een aangepaste claimprovider voor SharePoint 2013, 2016 en 2019 om dit scenario bij het selecteren van personen op te lossen. Deze maakt gebruik van de Microsoft Graph API om te corrigeren wat gebruikers invoeren en validatie uit te voeren. Ga voor meer informatie naar [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Zonder AzureCP kunt u groepen toevoegen met behulp van de groeps-id van Azure AD, maar deze methode is niet gebruikersvriendelijk en betrouwbaar. Zo gaat dat:
  > 
  >![Voeg een Azure AD-groep toe aan een SharePoint-groep op id](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Geef toestemming aan een Azure AD-groep in SharePoint on-premises

Om Azure AD-beveiligingsgroepen toe te wijzen aan SharePoint on-premises moet u een aangepaste claimprovider gebruiken voor SharePoint Server. In dit voorbeeld wordt AzureCP gebruikt.

 > [!NOTE]
 > AzureCP is geen Microsoft-product en wordt niet ondersteund door Microsoft Ondersteuning. Ga naar de website van [AzureCP](https://yvand.github.io/AzureCP/) om AzureCP te downloaden, installeren en configureren op de on-premises SharePoint-farm. 

1. Configureer AzureCP op de on-premises SharePoint-farm of een alternatieve oplossing van een aangepaste claimprovider. Ga naar de website van [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) om AzureCP te configureren.

1. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de naam van de bedrijfstoepassing die u eerder heeft aangemaakt en vervolgens **Eenmalige aanmelding**.

1. Bewerk de sectie **Gebruikerskenmerken en claims** op de pagina **Eenmalige aanmelding met SAML instellen**.

1. Selecteer **Een groepsclaim toevoegen**.

1. Selecteer welke groepen die zijn gekoppeld aan de gebruiker in de claim moeten worden geretourneerd. Selecteer in dit geval **Alle groepen**. Selecteer **Groeps-id** en vervolgens **Opslaan** in de sectie **Bronkenmerk**.

Als u de Azure AD-beveiligingsgroep toegang wilt verlenen in SharePoint on-premises, deelt u de siteverzameling of voegt u de Azure AD-beveiligingsgroep toe aan een van de groepen van de siteverzameling.

1. Ga naar **SharePoint-siteverzameling**. Selecteer **Personen en groepen** onder **Site-instellingen** voor de siteverzameling. 

1. Selecteer de SharePoint-groep en vervolgens **Nieuw** > **Gebruikers toevoegen aan deze groep**. Wanneer u de naam van uw groep typt geeft de personenkiezer de Azure AD-beveiligingsgroep weer.

    ![Voeg een Azure AD-groep toe aan een SharePoint-groep](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Verleen een gastaccount toegang tot SharePoint on-premises in de Azure Portal.

U kunt een gastaccount op een consistente wijze toegang verlenen tot uw SharePoint-site omdat de UPN nu gewijzigd wordt. De gebruiker `jdoe@outlook.com` wordt nu bijvoorbeeld weergegeven als `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Om uw site te delen met externe gebruikers moet u enkele wijzigingen aanbrengen in uw sectie **Gebruikerskenmerken en claims** in de Azure Portal.

1. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de naam van de bedrijfstoepassing die u eerder heeft aangemaakt en vervolgens **Eenmalige aanmelding**.

1. Bewerk de sectie **Gebruikerskenmerken en claims** op de pagina **Eenmalige aanmelding met SAML instellen**.

1. Selecteer **unieke gebruikers-id (Naam-ID)** bij **Vereiste claim**.

1. Verander de eigenschap **Bronkenmerk** naar de waarde **user.localuserprincipalname** en selecteer **Opslaan**.

    ![Gebruikerskenmerken en claims van initiële bronkenmerken](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Ga via het lint terug naar **Op SAML gebaseerde aanmelding**. Nu ziet de sectie **Gebruikerskenmerken en claims** er zo uit: 

    ![Gebruikerskenmerken en claims definitief](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Een achternaam en roepnaam zijn niet vereist voor deze installatie.

1. Selecteer in het meest linkse deelvenster in Azure Portal de optie **Azure Active Directory** en vervolgens **Gebruikers**.

1. Selecteer **Nieuwe gastgebruiker**.

1. Selecteer de optie **Gebruiker uitnodigen**. Vul de gebruikerseigenschappen in en selecteer **Uitnodigen**.

1. U kunt de site nu delen met MyGuestAccount@outlook.com en deze gebruiker toegang geven.

    ![Een site delen met een gastaccount](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>De vertrouwde id-provider voor meerdere webtoepassingen configureren

De configuratie werkt voor één webtoepassing. Als u dezelfde vertrouwde id-provider wilt gebruiken voor meerdere webtoepassingen, moet u aanvullende configuratiestappen uitvoeren. Stel, u hebt een webtoepassing uitgebreid om de URL te gebruiken `https://sales.contoso.com` en wilt nu de gebruikers ook verifiëren voor `https://marketing.contoso.com`. Hiervoor moet u de id-provider bijwerken om te voldoen aan de WReply-parameter en moet u de toepassingsregistratie in Azure AD bijwerken om een antwoord-URL toe te voegen.

1. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de naam van de bedrijfstoepassing die u eerder heeft aangemaakt en vervolgens **Eenmalige aanmelding**.

1. Bewerk **Standaard SAML-configuratie** op de pagina **Eenmalige aanmelding met SAML instellen**.

    ![Standaard SAML-configuratie](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Voeg de URL voor de aanvullende webtoepassingen toe en selecteer **Opslaan** voor **Antwoord-URL (URL voor Assertion Consumer Service)** .

    ![De standaard SAML-configuratie bewerken](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Open de SharePoint 201x-beheershell op de SharePoint-server en voer de volgende opdrachten uit. Gebruik de naam van de uitgever van het vertrouwde identiteitstoken dat u eerder hebt gebruikt.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Ga in **Centraal beheer** naar de webtoepassing en schakel de bestaande vertrouwde id-provider in.

Mogelijk zijn er andere scenario's waarbij u toegang wilt verlenen tot uw exemplaar van SharePoint on-premises aan uw interne gebruikers. Voor dit scenario moet u Microsoft Azure Active Directory Connect implementeren zodat het mogelijk wordt om uw on-premises gebruikers te synchroniseren met Azure AD. Deze installatie wordt in een ander artikel besproken.

## <a name="next-steps"></a>Volgende stappen

Nadat u SharePoint hebt geconfigureerd kunt u Session Control afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
