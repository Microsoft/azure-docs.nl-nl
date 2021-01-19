---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SAP Fiori | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: ab21c6dc95ef39e6273dac405ba58e748890fc1a
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SAP Fiori

In deze zelfstudie leert u hoe u SAP Fiori integreert met Azure Active Directory (Azure AD). Wanneer u SAP Fiori integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot SAP Fiori.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP Fiori.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SAP Fiori waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Fiori ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Voor door SAP Fiori geïnitieerde iFrame-verificatie raden we aan de parameter **IsPassive** in de SAML AuthnRequest te gebruiken voor verificatie op de achtergrond. Voor meer informatie over de parameter **IsPassive** raadpleegt u [Eenmalige aanmelding met SAML van Azure AD](../develop/single-sign-on-saml-protocol.md).

## <a name="adding-sap-fiori-from-the-gallery"></a>SAP Fiori toevoegen uit de galerie

Als u de integratie van SAP Fiori met Azure AD wilt configureren, moet u SAP Fiori uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **SAP Fiori**.
1. Selecteer **SAP Fiori** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-fiori"></a>Eenmalige aanmelding van Azure AD voor SAP Fiori configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SAP Fiori met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Fiori.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met SAP Fiori te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor SAP Fiori configureren](#configure-sap-fiori-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor SAP Fiori maken](#create-sap-fiori-test-user)** : als u een tegenhanger van B.Simon in SAP Fiori wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Open een nieuw browservenster en meld u als beheerder aan bij de bedrijfssite van SAP Fiori.

1. Zorg ervoor dat **http**- en **https**-services actief zijn en de relevante poorten zijn toegewezen aan transactiecode **SMICM**.

1. Meld u aan bij de bedrijfsclient voor SAP-systeem **T01**, waarvoor eenmalige aanmelding is vereist. Activeer vervolgens HTTP-beveiligingssessies beheren.

    1. Ga naar transactiecode **SICF_SESSIONS**. Alle relevante profielparameters met de huidige waarden worden weergegeven. Ze zien eruit als het volgende voorbeeld:

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
        > Pas de parameters aan op basis van uw organisatievereisten. De bovenstaande parameters worden alleen als voorbeeld gegeven.

    1. Pas zo nodig de parameters aan in het exemplaarprofiel (standaardprofiel) van het SAP-systeem en start het SAP-systeem opnieuw op.

    1. Dubbelklik op de relevante client om een HTTP-beveiligingssessie in te schakelen.

        ![De pagina Huidige waarden van relevante profielparameters in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Activeer de volgende SICF-services:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Ga naar transactiecode **SAML2** in de bedrijfsclient voor SAP-systeem [**T01/122**]. De gebruikersinterface van de configuratie wordt geopend in een nieuw browservenster. In dit voorbeeld gebruiken we de bedrijfsclient voor SAP-systeem 122.

    ![De aanmeldingspagina van de SAP Fiori-bedrijfsclient](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Voer uw gebruikersnaam en wachtwoord in en selecteer vervolgens **Aanmelden**.

    ![De pagina SAML 2.0-configuratie van ABAP-systeem T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. In het vak **Naam van provider** vervangt u **T01122** door **http:\//T01122** en vervolgens selecteert u **Opslaan**.

    > [!NOTE]
    > De providernaam heeft standaard de indeling \<sid>\<client>. In Azure AD wordt de naam in de indeling \<protocol>://\<name> verwacht. We raden u aan de providernaam als https\://\<sid>\<client> te houden, zodat u meerdere SAP Fiori ABAP-engines in Azure AD kunt configureren.

    ![De bijgewerkte providernaam op de pagina SAML 2.0-configuratie van ABAP-systeem T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecteer het tabblad **Lokale provider** > **Metagegevens**.

1. In het dialoogvenster **SAML 2.0-metagegevens** downloadt u het gegenereerde XML-bestand met metagegevens en slaat u dit op uw computer op.

    ![De koppeling naar Metagegevens downloaden in het dialoogvenster SAP SAML 2.0-metagegevens](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Ga in Azure Portal op de integratiepagina van de toepassing **SAP Fiori** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Wanneer het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in het deelvenster **Standaard SAML-configuratie**. Typ in het tvak **Aanmeldings-URL** een URL met het volgende patroon: `https:\//\<your company instance of SAP Fiori\>`.

    > [!NOTE]
    > Een aantal klanten melden fouten met betrekking tot onjuist geconfigureerde waarden voor **Antwoord-URL**. Als u deze foutmelding krijgt, kunt u het volgende PowerShell-script gebruiken om de juiste Antwoord-URL voor uw exemplaar in te stellen:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > U kunt de `ServicePrincipal`-object-id zelf instellen voordat u het script uitvoert, of u kunt deze hier doorgeven.

1. In de SAP Fiori-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Configureer de volgende claims voor deze toepassing. Om deze kenmerkwaarden te beheren, selecteert u in het deelvenster **Eenmalige aanmelding met SAML instellen** de optie **Bewerken**.

    ![Het deelvenster Gebruikerskenmerken](common/edit-attribute.png)

1. In het deelvenster **Gebruikerskenmerken en claims** configureert u de SAML-tokenkenmerken zoals weergegeven in de vorige afbeelding. Voer de volgende stappen uit:

    1. Selecteer **Bewerken** om het deelvenster **Gebruikersclaims beheren** te openen.

    1. In de lijst **Transformatie** selecteert u **ExtractMailPrefix()** .

    1. In de lijst **Parameter 1** selecteert u **user.userprincipalname**.

    1. Selecteer **Opslaan**.

       ![Het deelvenster Gebruikersclaims beheren](./media/sapfiori-tutorial/nameidattribute.png)

       ![De sectie Transformatie in het deelvenster Gebruikersclaims beheren](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **SAP Fiori instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot SAP Fiori.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Fiori** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-fiori-sso"></a>Eenmalige aanmelding voor SAP Fiori configureren

1. Meld u aan bij het SAP-systeem en ga naar transactiecode **SAML2**. Er wordt een nieuw browservenster geopend met de pagina SAML-configuratie.

1. Selecteer het tabblad **Vertrouwde providers** om eindpunten voor een vertrouwde identiteitsprovider (Azure AD) te configureren.

    ![Het tabblad Vertrouwde providers in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecteer **Toevoegen** en selecteer vervolgens **Metagegevensbestand uploaden** in het contextmenu.

    ![De opties Toevoegen en Metagegevensbestand uploaden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Upload het metagegevensbestand dat u hebt gedownload in de Azure-portal. Selecteer **Next**.

    ![Het metagegevensbestand selecteren om te uploaden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Op de volgende pagina voert u in het vak **Alias** de aliasnaam in, bijvoorbeeld **aadsts**. Selecteer **Next**.

    ![Het vak Alias in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Zorg ervoor dat de waarde in het vak **Samenvattingsalgoritme** **SHA-256** is. Selecteer **Next**.

    ![De waarde van Samenvattingsalgoritme verifiëren in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Selecteer onder **Eindpunten voor eenmalige aanmelding** de optie **HTTP POST** en selecteer vervolgens **Volgende**.

    ![Opties in Eindpunten voor eenmalige aanmelding in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Selecteer onder **Eindpunten voor eenmalige afmelding** de optie **HTTP-omleiding** en selecteer vervolgens **Volgende**.

    ![Opties in Eindpunten voor eenmalige afmelding in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Selecteer onder **Artefacteindpunten** de optie **Volgende** om door te gaan.

    ![Opties in Artefacteindpunten in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Selecteer onder **Verificatievereisten** de optie **Voltooien**.

    ![Opties in Verificatievereisten en de optie Voltooien in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecteer **Vertrouwde provider** > **Identiteitsfederatie** (onderaan de pagina). Selecteer **Bewerken**.

    ![De tabbladen Vertrouwde provider en Identiteitsfederatie in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecteer **Toevoegen**.

    ![De optie Toevoegen op het tabblad Identiteitsfederatie](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Selecteer in het dialoogvenster **Ondersteunde NameID-indelingen** de optie **Niet opgegeven**. Selecteer **OK**.

    ![Het dialoogvenster Ondersteunde NameID-indelingen en opties in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    De waarden voor **Bron van gebruikers-id** en **Toewijzingsmodus van gebruikers-id** bepalen de koppeling tussen de SAP-gebruiker en de Azure AD-claim.  

    **Scenario 1**: Toewijzing van SAP-gebruiker aan Azure AD-gebruiker

    1. In SAP onder **Details van NameID-indeling ‘Niet opgegeven’** ziet u de details:

        ![Schermopname van het dialoogvenster Details of NameID Format "Unspecified" in SAP.](./media/sapfiori-tutorial/nameiddetails.png)

    1. In de Azure-portal onder **Gebruikerskenmerken en claims** ziet u de vereiste claims uit Azure AD.

        ![Schermopname met het dialoogvenster Gebruikerskenmerken en claims.](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Selecteer de SAP-gebruikers-id op basis van het geconfigureerd e-mailadres in SU01. In dit geval moet de e-mail-id voor elke gebruiker waarvoor eenmalige aanmelding is vereist, worden geconfigureerd in SU01.

    1.  In SAP onder **Details van NameID-indeling ‘Niet opgegeven’** ziet u de details:

        ![Het dialoogvenster Details van NameID-indeling ‘Niet opgegeven’ in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. In de Azure-portal onder **Gebruikerskenmerken en claims** ziet u de vereiste claims uit Azure AD.

       ![Het dialoogvenster Gebruikerskenmerken en claims in de Azure-portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecteer **Opslaan** en selecteer vervolgens **Inschakelen** om de identiteitsprovider in te schakelen.

    ![De opties Opslaan en Inschakelen in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecteer **OK** wanneer u hierom wordt gevraagd.

    ![De optie OK in het dialoogvenster SAML 2.0-configuratie in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Testgebruiker voor SAP Fiori maken

In deze sectie maakt u in SAP Fiori een gebruiker met de naam Britta Simon. Werk samen met uw interne SAP-deskundige of met de SAP-partner van uw organisatie om de gebruiker toe te voegen voor het SAP Fiori-platform.

## <a name="test-sso"></a>Eenmalige aanmelding testen

1. Nadat de identiteitsprovider Azure AD is geactiveerd in SAP Fiori, probeert u een van de volgende URL’s te openen om eenmalige aanmelding te testen (u zou niet om een gebruikersnaam en wachtwoord moeten worden gevraagd):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Vervang *sapurl* door de feitelijke SAP-hostnaam.

1. De test-URL zou u naar de volgende pagina Toepassing testen in SAP moeten leiden. Als de pagina wordt geopend, is eenmalige aanmelding van Azure AD ingesteld.

    ![De standaardpagina Toepassing testen in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Als u om een gebruikersnaam en wachtwoord wordt gevraagd, schakelt u tracering in om het probleem te helpen vaststellen. Gebruik de volgende URL voor de tracering: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Volgende stappen

Zodra u SAP Fiori hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).