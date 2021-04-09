---
title: 'Zelfstudie: Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SAP NetWeaver | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP NetWeaver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: b6b8dab3472473082562f1e4c0216886191e4a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97962802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SAP NetWeaver

In deze zelfstudie leert u hoe u SAP NetWeaver integreert met Azure Active Directory (Azure AD). Wanneer u SAP NetWeaver integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang tot SAP NetWeaver heeft.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SAP NetWeaver.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SAP NetWeaver waarvoor eenmalige aanmelding (SSO) is ingeschakeld.
* Voor SAP NetWeaver V7.20 is ten minste vereist

## <a name="scenario-description"></a>Scenariobeschrijving

* SAP NetWeaver ondersteunt zowel **SAML** (**door SP geïnitieerde SSO**) als **OAuth**. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

> [!NOTE]
> De toepassing in SAML of in OAuth configureren conform de vereisten van uw organisatie. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP NetWeaver met Azure AD moet u SAP NetWeaver uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **SAP NetWeaver**.
1. Selecteer **SAP NetWeaver** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Eenmalige aanmelding van Azure AD voor SAP NetWeaver configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met SAP NetWeaver met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP NetWeaver.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SAP NetWeaver te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAP NetWeaver configureren met SAML](#configure-sap-netweaver-using-saml)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[De testgebruiker voor SAP NetWeaver maken](#create-sap-netweaver-test-user)** als u een equivalent van B. Simon in SAP NetWeaver wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.
1. **[SAP NetWeaver configureren voor OAuth](#configure-sap-netweaver-for-oauth)** om de instellingen voor OAuth aan de kant van de toepassing te configureren.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP NetWeaver moet u de volgende stappen uitvoeren:

1. Open een nieuw browservenster en meld u aan als beheerder bij de SAP NetWeaver-site van uw bedrijf

1. Zorg ervoor dat **http**- en **https**-services actief zijn en de juiste poorten zijn toegewezen in T-code **SMICM**.

1. Meld u aan bij de bedrijfsclient van het SAP-systeem (T01), waarvoor eenmalige aanmelding is vereist, en activeer HTTP-beveiligingssessie Management.

    a. Ga naar transactiecode **SICF_SESSIONS**. Hier worden alle relevante profielparameters met de huidige waarden weergegeven. Deze zien eruit als hieronder:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Pas de bovenstaande parameters aan volgens de vereisten van uw organisatie. De bovenstaande parameters worden hier slechts als indicatie weergegeven.

    b. Pas zo nodig de parameters aan in het exemplaar-/standaardprofiel van het SAP-systeem en start het SAP-systeem opnieuw op.

    c. Dubbelklik op de relevante client om de HTTP-beveiligingssessie in te schakelen.

    ![De HTTP Security-sessie ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Activeer de onderstaande SICF-services:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Ga naar transactiecode **SAML2** in de bedrijfsclient van het SAP-systeem [T01/122]. In een browser wordt een gebruikersinterface geopend. In dit voorbeeld wordt ervanuit gegaan dat 122 een SAP-bedrijfsclient is.

    ![Transactiecode](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Voer uw gebruikersnaam en wachtwoord in de gebruikersinterface in en klik op **Edit**.

    ![gebruikersnaam en wachtwoord](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Vervang **Provider Name** T01122 door `http://T01122` en klik op **Save**.

    > [!NOTE]
    > Standaard wordt Provider Name in de indeling `<sid><client>` weergegeven, maar in Azure AD wordt verwacht dat de naam in de indeling `<protocol>://<name>` wordt weergegeven, waarbij wordt aangeraden Provider Name als `https://<sid><client>` te handhaven zodat meerdere SAP NetWeaver ABAP-engines in Azure AD kunnen worden geconfigureerd.

    ![De verschillende SAP NetWeaver ABAP-engines](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Metagegevens van de serviceprovider genereren**: als de instellingen **Local Provider** en **Trusted Providers** in de SAML 2.0-gebruikersinterface zijn geconfigureerd, bestaat de volgende stap uit het genereren van het bestand met metagegevens van de serviceprovider (dat alle instellingen, contexten voor verificatie en overige configuraties in SAP bevat). Als dit bestand is gegenereerd, moet het in Azure AD worden geüpload.

    ![Metagegevens van serviceprovider genereren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Ga naar het tabblad **Local Provider**.

    b. Klik op **Metadata**.

    c. Sla het gegenereerde **XML-bestand met metagegevens** op de computer op en upload het naar de sectie **Basic SAML Configuration** om de waarden **Identifier** en **Reply URL** automatisch in Azure Portal in te vullen.

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **SAP NetWeaver** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. Klik op **Metagegevensbestand uploaden** om het **metagegevensbestand van de serviceprovider** te uploaden dat u eerder hebt verkregen.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Identifier** en **Reply URL** automatisch ingevuld in het tekstvak van de sectie **Standaard SAML-configuratie**, zoals hieronder weergegeven:

    d. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Enkele klanten hebben een fout gerapporteerd dat een onjuiste antwoord-URL voor hun instantie is geconfigureerd. Als u een dergelijke foutmelding krijgt, kunt u het volgende PowerShell-script als tijdelijke oplossing gebruiken om de juiste antwoord-URL voor uw instantie in te stellen:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > De ServicePrincipal-object-id moet eerst door uzelf worden ingesteld, maar u kunt deze ook hier doorgeven.

1. De SAP NetWeaver-toepassing verwacht de SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![kenmerk bewerken](common/edit-attribute.png)

1. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![pictogram bewerken](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    c. Selecteer **user.userprincipalname** in de lijst **Parameter 1**.

    d. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **SAP NetWeaver instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
    1. Voer in het veld **Naam**`B.Simon` in.  
    1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
    1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SAP NetWeaver.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Typ en selecteer **SAP NetWeaver** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-netweaver-using-saml"></a>SAP NetWeaver configureren met SAML

1. Meld u aan bij het SAP-systeem en ga naar transactiecode SAML2. Er wordt een nieuwe browservenster geopend met een SAML-configuratiescherm.

2. Ga naar het tabblad **Trusted Providers** om eindpunten voor vertrouwde identiteitsproviders (Azure AD) te configureren.

    ![Vertrouwde providers voor eenmalige aanmelding configureren](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Druk op **Add** en selecteer **Upload Metadata File** in het snelmenu.

    ![Eenmalige aanmelding configureren 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Upload het metagegevensbestand dat u eerder in de Azure-portal hebt gedownload.

    ![Eenmalige aanmelding configureren 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Typ de aliasnaam in het volgende scherm. Bijvoorbeeld aadsts en druk op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren 4](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Zorg ervoor dat **Digest Algorithm****SHA-256** is, vereis geen wijzigingen en druk op **Next**.

    ![Eenmalige aanmelding configureren 5](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Gebruik bij **Single Sign-On Endpoints****HTTP POST** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren 6](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Gebruik bij **Single Logout Endpoints****HTTPRedirect** en klik op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren 7](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Druk bij **Artifact Endpoints** op **Next** om door te gaan.

    ![Eenmalige aanmelding configureren 8](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Klik bij **Authentication Requirements** op **Finish**.

    ![Eenmalige aanmelding configureren 9](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ga naar het tabblad **Trusted Provider** > **Identity Federation** (vanaf de onderkant van het scherm). Klik op **Bewerken**.

    ![Eenmalige aanmelding configureren 10](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klik onder het tabblad **Identity Federation** op **Add** (onderste venster).

    ![Eenmalige aanmelding configureren 11](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Selecteer in het pop-upvenster **Supported NameID formats** de optie **Unspecified** en klik op OK.

    ![Eenmalige aanmelding configureren 12](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

1. Geef de waarde voor **User ID Source** op als **Assertion Attribute**, de waarde voor de **User ID mapping mode** op als **Email** en geef de **Assertion Attribute Name** op als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    ![Eenmalige aanmelding configureren ](./media/sapnetweaver-tutorial/nameid-format.png)

14. De waarden **User ID Source** en **User ID mapping mode** bepalen de koppeling tussen de SAP-gebruiker en de Azure AD-claim.

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: Toewijzing van SAP-gebruiker aan Azure AD-gebruiker.

    a. Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren 13](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren 14](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Selecteer de SAP-gebruikers-id op basis van geconfigureerd e-mailadres in SU01. In dit geval moet de e-mail-id voor elke gebruiker waarvoor eenmalige aanmelding is vereist, worden geconfigureerd in SU01.

    a.  Schermopname van details NameID van SAP.

    ![Eenmalige aanmelding configureren 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Schermopname met Vereiste claims van Azure AD.

    ![Eenmalige aanmelding configureren 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klik op **Save** en vervolgens op **Enable** om de id-provider in te schakelen.

    ![Eenmalige aanmelding configureren 17](./media/sapnetweaver-tutorial/configuration1.png)

16. Klik op **OK** zodra daarom wordt gevraagd.

    ![Eenmalige aanmelding configureren 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver-testgebruiker maken

    In deze sectie maakt u een gebruiker met de naam B.simon in SAP NetWeaver. Werk samen met uw interne SAP-deskundige of met de SAP-partner van uw organisatie om de gebruikers aan het SAP NetWeaver-platform toe te voegen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

1. Als de id-provider Azure AD is geactiveerd, opent u de onderstaande URL om eenmalige aanmelding te controleren (u wordt niet om gebruikersnaam en wachtwoord gevraagd)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (of) gebruik de onderstaande URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Vervang sapurl door de feitelijke SAP-hostnaam.

2. Via de bovenstaande URL moet u op het hieronder genoemd scherm terechtkomen. Als u de onderstaande pagina kunt bereiken, is eenmalige aanmelding voor Azure AD ingesteld.

    ![eenmalige aanmelding testen](./media/sapnetweaver-tutorial/testingsso.png)

3. Als u om een gebruikersnaam en wachtwoord wordt gevraagd, probeert u het probleem vast te stellen door de tracering in te schakelen met behulp van de onderstaande URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>SAP NetWeaver configureren voor OAuth

1. SAP-gedocumenteerd proces is beschikbaar op de locatie: [Inschakelen NetWeaver Gateway Service en Aanmaken bereik OAuth 2.0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Ga naar SPRO en zoek **Services activeren en onderhouden**.

    ![Services activeren en onderhouden](./media/sapnetweaver-tutorial/oauth01.png)

3. In dit voorbeeld willen we verbinding maken met de OData-service: `DAAG_MNGGRP` met OAuth naar Azure AD eenmalige aanmelding. Gebruik de naam van de technische service voor het zoeken naar de service `DAAG_MNGGRP` en activeer indien deze nog niet actief is (zoek naar `green` status onder het tabblad ICF-knooppunten). Zorg ervoor dat de systeem alias (het verbonden back-end-systeem, waar de service daadwerkelijk wordt uitgevoerd) juist is.

    ![OData-service](./media/sapnetweaver-tutorial/oauth02.png)

    * Klik vervolgens op knop **OAuth** op de bovenste knoppenbalk en wijs `scope` toe (behoud de standaard naam zoals deze wordt aangeboden).

4. In ons voorbeeld is het bereik `DAAG_MNGGRP_001` en wordt het gegenereerd op basis van de servicenaam door automatisch een nummer toe te voegen. Rapport `/IWFND/R_OAUTH_SCOPES` kan worden gebruikt om de naam van het bereik te wijzigen of handmatig te maken.

    ![OAuth configureren](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Bericht `soft state status is not supported`: kan worden genegeerd, omdat er geen problemen zijn. Kijk [hier](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true) voor meer informatie.

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Een servicegebruiker maken voor de OAuth 2.0 Client

1. OAuth2 maakt gebruik van een `service ID` voor het verkrijgen van het toegangstoken voor de eindgebruiker. Belangrijke beperking van het OAuth-ontwerp: de `OAuth 2.0 Client ID` moet identiek zijn aan de `username` die de OAuth 2.0-client gebruikt voor aanmelding bij het aanvragen van een toegangstoken. Daarom gaan we in ons voorbeeld een OAuth 2.0-client met de naam CLIENT1 registreren, en het is vereist dat er een gebruiker met dezelfde naam (CLIENT1) bestaat in het SAP-systeem en die gebruiker gaan we configureren om te gebruiken met de toepassing waarnaar wordt verwezen. 

2. Wanneer u een OAuth-client registreert, gebruiken we de `SAML Bearer Grant type`.

    >[!NOTE]
    >Raadpleeg voor meer informatie OAuth 2.0 Clientregistratie voor het toekenningstype SAML Bearer [hier](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type).

3. tcod: SU01 / maak gebruiker CLIENT1 als `System type` en wijs het wachtwoord toe, sla het op aangezien de aanmeldingsgegevens moeten worden opgegeven aan de API-programmeur, die dit moet branden samen met de gebruikersnaam voor de aanroepcode. Er mag geen profiel of rol worden toegewezen.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>De nieuwe OAuth 2.0-client-id registreren met de wizard voor het maken

1. Om een nieuwe **OAuth 2.0-client** te registreren start u transactie **SOAUTH2**. In de transactie wordt een overzicht weergegeven van de OAuth 2.0-clients die al zijn geregistreerd. Kies **Maken** om de wizard te starten voor de nieuwe OAuth-client met de naam CLIENT1 in dit voorbeeld.

2. Ga naar T-Code: **SOAUTH2** en geef de beschrijving op en klik vervolgens op **volgende**.

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![ID van OAuth 2.0-client](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecteer de al toegevoegde **SAML2 IdP-Azure AD** in de vervolgkeuzelijst en sla deze op.

    ![SAML2 IdP – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![SAML2 IdP – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![SAML2 IdP – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Klik op **Toevoegen** onder bereik toewijzing om het eerder gemaakte bereik toe te voegen: `DAAG_MNGGRP_001`

    ![Bereik](./media/sapnetweaver-tutorial/oauth09.png)

    ![scopetoewijzing](./media/sapnetweaver-tutorial/oauth10.png)

5. Klik op **voltooien**.

## <a name="next-steps"></a>Volgende stappen

Zodra u Azure AD SAP NetWeaver hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
