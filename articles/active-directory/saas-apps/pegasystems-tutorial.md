---
title: 'Zelfstudie: Azure Active Directory-integratie met Pega Systems | Microsoft Docs'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649999"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Zelfstudie: Azure Active Directory-integratie met Pega Systems

In deze zelfstudie leert u hoe u Pega Systems integreert met Azure Active Directory (Azure AD). Wanneer u Pega-systemen integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Pega-systemen.
* Stel in dat uw gebruikers automatisch kunnen worden aangemeld bij Pega-systemen met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op eenmalige aanmelding (SSO) van Pega Systems.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Pega Systems ondersteunt door SP geïnitieerde en door IdP geïnitieerde eenmalige aanmelding.

## <a name="add-pega-systems-from-the-gallery"></a>Pega Systems toevoegen vanuit de galerie

Als u de integratie van Pega-systemen in azure AD wilt configureren, moet u Pega-systemen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Pega systemen** in het zoekvak.
1. Selecteer **Pega systemen** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Azure AD SSO voor Pega-systemen configureren en testen

Azure AD SSO configureren en testen met Pega-systemen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Pega-systemen.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Pega-systemen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Pega Systems SSO configureren](#configure-pega-systems-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Pega Systems-test gebruiker maken](#create-pega-systems-test-user)** : u hebt een equivalent van B. Simon in Pega-systemen dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Pega Systems** Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het dialoogvenster **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IdP geïnitieerde modus wilt configureren.

    ![Dialoogvenster Standaard SAML-configuratie](common/idp-intiated.png)

    1. Voer in het vak **Id** een URL met dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Voer in het vak **Antwoord-URL** een URL met dit patroon in:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en voert u de volgende stappen uit.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Pega Systems](common/both-advanced-urls.png)

    1. Voer in het vak **Aanmeldings-URL** de aanmeldwaarde van de URL in.

    1. Voer in het vak **Relaystatus** een URL met dit patroon in: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De waarden die hier worden vermeld, zijn tijdelijke aanduidingen. U moet de werkelijke id, antwoord-URL, aanmeldings-URL en relaystatus gebruiken. U kunt de id-en antwoord-URL-waarden ophalen uit een Pega-toepassing, zoals verderop in deze zelfstudie wordt uitgelegd. Neem contact op met het [ondersteuningsteam van PegaSystems](https://www.pega.com/contact-us)als u de waarde voor de relaystatus wilt ophalen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Voor de Pega Systems-toepassing moeten de SAML-asserties een specifieke indeling hebben. Om deze in de juiste indeling te krijgen, moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. Op de volgende schermopname worden de standaardkenmerken weergegeven. Selecteer het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen:

    ![Gebruikerskenmerken](common/edit-attribute.png)

7. Naast de kenmerken die in de vorige schermopname worden weergegeven, moeten voor de Pega Systems-toepassing nog enkele kenmerken worden doorgegeven in het SAML-antwoord. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om deze SAML-tokenkenmerken toe te voegen:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Deze waarden zijn specifiek voor uw organisatie. Geef de relevante waarden op.

    1. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen:

    ![Selecteer Nieuwe claim toevoegen](common/new-save-attribute.png)

    ![Het dialoogvenster Gebruikersclaims beheren](common/new-attribute-details.png)

    1. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat het vak **Naamruimte** leeg.

    1. Als **Bron** selecteert u **Kenmerk**.

    1. Selecteer de kenmerkwaarde voor die rij in de lijst met **Bronkenmerken**.

    1. Selecteer **OK**.

    1. Selecteer **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **XML-bestand met federatieve metagegevens** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

9. Kopieer in de sectie **Pega Systems instellen** de juiste URL's op basis van uw vereisten.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Pega-systemen.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Pega Systems**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-pega-systems-sso"></a>SSO van Pega Systems configureren

1. Meld u aan bij de Pega-Portal met een beheerdersaccount in een ander browservenster om eenmalige aanmelding te configureren aan de **Pega Systems**-zijde.

2. Selecteer **Maken** > **SysAdmin** > **Verificatieservice**:

    ![Selecteer verificatieservice](./media/pegasystems-tutorial/admin.png)
    
3. Voer de volgende stappen uit op het scherm **Verificatieservice maken**.

    ![Verificatieservice-scherm maken](./media/pegasystems-tutorial/admin1.png)

    1. Selecteer **SAML 2.0** in de lijst **Type**.

    1. Voer in het vak **Naam** een naam in (bijvoorbeeld **Azure AD SSO**).

    1. Voer een beschrijving in in het venster **Korte beschrijving**.  

    1. Selecteer **Maken en openen**.
    
4. Selecteer in de sectie **Informatie over id-provider (IdP)** **Metagegevens van IdP importeren** en blader naar het metagegevensbestand dat u hebt gedownload van Azure Portal. Klik op **Verzenden** om de metagegevens te laden:

    ![Sectie Informatie over id-provider (IdP)](./media/pegasystems-tutorial/admin2.png)
    
    Bij het importeren worden de IdP-gegevens ingevuld, zoals hier wordt weer gegeven:

    ![Geïmporteerde IdP-gegevens](./media/pegasystems-tutorial/idp.png)
    
6. Voltooi de volgende stappen in de sectie **Instellingen serviceprovider (SP)** .

    ![Instellingen voor serviceprovider](./media/pegasystems-tutorial/sp.png)

    1. Kopieer de waarde van de **Entiteits-id** en plak deze in het tekstvak voor de **id** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    1. Kopieer de waarde van de **Assertion Consumer Service (ACS)-locatie** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal.

    1. Selecteer **Uitschakelen aanvraag ondertekening**.

7. Selecteer **Opslaan**.

### <a name="create-pega-systems-test-user"></a>Pega Systems-test gebruiker maken

Vervolgens moet u een gebruiker met de naam Britta Simon maken in Pega Systems. Werk met het [ondersteuningsteam van Pega Systems](https://www.pega.com/contact-us) om gebruikers te maken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Pega-systemen, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van de Pega-systemen en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Pega-systemen waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Pega-systemen in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Pega-systemen waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Wanneer u Pega-systemen hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).