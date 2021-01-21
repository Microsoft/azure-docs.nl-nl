---
title: 'Zelfstudie: Azure Active Directory-integratie met SAML SSO for Bitbucket by resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621244"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Zelfstudie: Azure Active Directory-integratie met SAML SSO for Bitbucket by resolution GmbH

In deze zelf studie leert u hoe u SAML SSO kunt integreren voor bitbucket door Solution GmbH met Azure Active Directory (Azure AD). Wanneer u SAML SSO integreert voor bitbucket door de oplossing GmbH met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot toSAML SSO voor bitbucket door de oplossing GmbH.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij toSAML SSO voor bitbucket door de oplossing GmbH met hun Azure AD-accounts.
* Uw accounts op één centrale locatie beheren: de Azure-portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met SAML SSO for Bitbucket by resolution GmbH te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SAML SSO for Bitbucket by resolution GmbH waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO for Bitbucket by resolution GmbH ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* SAML SSO for Bitbucket by resolution GmbH ondersteunt **Just-In-Time** gebruikers inrichten


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>SAML SSO voor bitbucket toevoegen door de oplossing van de galerie

Voor het configureren van de integratie van SAML SSO for Bitbucket by resolution GmbH in Azure AD moet u SAML SSO for Bitbucket by resolution GmbH vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAML SSO voor bitbucket by Solution GmbH** in het zoekvak.
1. Selecteer **SAML SSO voor bitbucket door de oplossing GmbH** van de resultaten te selecteren en de app vervolgens toe te voegen. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Azure AD SSO voor SAML SSO configureren en testen voor bitbucket door de oplossing GmbH

Azure AD SSO configureren en testen met SAML SSO voor bitbucket by Solution GmbH, door gebruik te maken van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAML SSO voor bitbucket door de oplossing GmbH.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SAML SSO voor bitbucket door Solution GmbH:


1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[SAML SSO configureren voor bitbucket door de oplossing GmbH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** -om de instellingen voor één Sign-On te configureren aan de kant van de toepassing.
    1. **[Testgebruiker voor SAML SSO for Bitbucket by resolution GmbH maken](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**: als u een tegenhanger van Britta Simon in SAML SSO for Bitbucket by resolution GmbH wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u eenmalige aanmelding van Azure AD in de Azure Portal inschakelen.
 
1. Ga in het Azure Portal naar de pagina voor het oplossen van problemen met de integratie **van de** bitbucket-app voor de SAML- **SSO voor de oplossing** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:


    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:
    
    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de SAML SSO voor bitbucket door de oplossing GmbH.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAML SSO voor bitbucket by resolution GmbH**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>SAML SSO configureren voor bitbucket met de oplossing GmbH SSO

1. Meld u aan bij de bedrijfssite van SAML SSO for Bitbucket by resolution GmbH als een beheerder.

2. Klik aan de rechterkant van de hoofdwerkbalk op **Settings** (Instellingen).

3. Ga naar het gedeelte ACCOUNTS en klik in de menubalk op **SAML SingleSignOn** (Eenmalige aanmelding met SAML).

    ![De eenmalige aanmelding met SAML](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Op de **pagina SAML SIngleSignOn Plugin Configuration** (Configuratie van plug-in voor eenmalige aanmelding met SAML) klikt u op **Add idp** (IdP toevoegen). 

    ![De IdP toevoegen](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Voer op de pagina **Choose your SAML Identity Provider** (Uw SAML-id-provider kiezen) de volgende stappen uit:

    ![De id-provider](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Selecteer bij **Idp Type** (Type IdP) de optie **AZURE AD**.

    b. Voer in het tekstvak **Name** (Naam) de naam in.

    c. Voer in het tekstvak **Description** (Omschrijving) de omschrijving in.

    d. Klik op **Volgende**.

6. Op de **pagina voor het configureren van de id-provider** klikt u op **Next** (Volgende).

    ![De id-configuratie](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Klik op de pagina **Import SAML Idp Metadata** (Metagegevens voor SAML-IdP importeren) op de optie **Load File** (Bestand laden) om het bestand **METADATA XML** te uploaden die u uit de Azure-portal hebt gedownload.

    ![De IdP-metagegevens](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Klik op **Volgende**.

9. Klik op **Save settings** (Instellingen opslaan).

    ![Het opslaan](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Testgebruiker voor SAML SSO for Bitbucket by resolution GmbH maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam Britta Simon in SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH biedt ondersteuning voor Just-In-Time-inrichting en het handmatig inrichten van gebruikers. Neem contact op met [het klantondersteuningsteam van SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) naargelang uw behoeften.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar SAML SSO voor bitbucket by Solution GmbH sign on URL, waar u de aanmeldings stroom kunt initiëren.  

* Ga naar SAML SSO voor bitbucket by resolution GmbH sign-on URL direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de SAML SSO voor bitbucket door de oplossing GmbH waarvoor u de SSO hebt ingesteld.

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel SAML SSO voor bitbucket by resolution GmbH in het gedeelte mijn apps klikt, wordt u als geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de SAML SSO voor bitbucket door de oplossing GmbH waarvoor u de SSO instelt. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u de SAML SSO voor bitbucket hebt geconfigureerd door de oplossing GmbH, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).