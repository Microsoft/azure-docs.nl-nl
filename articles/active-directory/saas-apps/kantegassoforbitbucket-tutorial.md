---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kantega SSO voor Bitbucket | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Kantega SSO voor Bitbucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 52879eb7cb7a9d90113971aa66c590b99b2d5e88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459277"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kantega SSO voor Bitbucket

In deze zelfstudie leert u hoe u Kantega SSO voor Bitbucket kunt integreren met Azure AD (Active Directory).
Integratie van Kantega SSO voor Bitbucket met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Kantega SSO voor Bitbucket.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Kantega SSO (eenmalige aanmelding) voor Bitbucket.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Kantega SSO voor Bitbucket wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Kantega SSO for Bitbucket waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kantega SSO for Bitbucket ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Kantega SSO for Bitbucket toevoegen vanuit de galerie

Om de integratie van Kantega SSO for Bitbucket in Azure AD te configureren, moet u Kantega SSO for Bitbucket vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Kantega SSO for Bitbucket toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Kantega SSO for Bitbucket** in het zoekvak, selecteer **Kantega SSO for Bitbucket** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Kantega SSO for Bitbucket in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure Active Directory met Kantega SSO for Bitbucket op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Kantega SSO for Bitbucket tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Kantega SSO for Bitbucket wilt configureren en testen, moet u de volgende stappen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Kantega SSO for Bitbucket configureren](#configure-kantega-sso-for-bitbucket-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Kantega SSO for Bitbucket maken](#create-kantega-sso-for-bitbucket-test-user)** : als u een tegenhanger van Britta Simon in Kantega SSO for Bitbucket wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding van Azure AD wilt configureren voor Kantega SSO for Bitbucket, voert u de volgende stappen uit:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Kantega SSO for Bitbucket** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Deze waarden krijgt u tijdens de configuratie van de Bitbucket-invoegtoepassing, wat later in de zelfstudie wordt uitgelegd.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **Kantega SSO for Bitbucket instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Eenmalige aanmelding voor Kantega SSO for Bitbucket configureren

1. Meld u in een ander browservenster als een beheerder aan bij de Bitbucket-beheerportal.

1. Klik op het tandwiel en klik op **Find new add-ons**.

    ![Schermopname van BitBucket-beheer met Nieuwe invoegtoepassingen zoeken geselecteerd.](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Zoek **Kantega SSO for Bitbucket (SAML en Kerberos)** en klik op de knop **Installeren** om de nieuwe SAML-invoegtoepassing te installeren.

    ![Schermopname van Kantega SSO voor Bitbucket SAML & Kerberos met de optie om te installeren.](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. De installatie van de invoegtoepassing wordt gestart.

    ![Schermopname van de Voortgang van de installatie.](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Nadat de installatie is voltooid. Klik op **Sluiten**.

    ![Schermopname van de knop Sluiten.](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Klik op **Beheren**.

    ![Schermopname van de knop Beheren.](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname van 'User-installed add-ons' (Door de gebruiker geïnstalleerde invoegtoepassingen) met 'Configure' (Configureren) geselecteerd.](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. In het gedeelte **SAML**. Selecteer **Azure Active Directory (Azure AD)** in de vervolgkeuzelijst **Id-provider toevoegen**.

    ![Schermopname van de Eenmalige aanmelding van Kantega met Azure AD als id-provider geselecteerd.](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Selecteer als abonnementsniveau de optie **Basic**.

    ![Schermopname van 'Azure AD voorbereiden' met 'Basic' geselecteerd.](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Voer de volgende stappen uit in het gedeelte **App-eigenschappen**:

    ![Schermopname van de sectie 'App properties' (App-eigenschappen) waar u de informatie voor deze stap kunt opgeven.](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopieer de waarde voor de **app-id-URI** en gebruik deze als **id, antwoord-URL en aanmeldings-URL** in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit in het gedeelte **Metagegevens importeren**:

    ![Schermopname van de sectie 'Metadata import' (Metagegevens importeren), waar u naar een metagegevensbestand kunt bladeren.](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Selecteer **Metadata file on my computer** en upload het metagegevensbestand dat u vanuit Azure Portal hebt gedownload.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit op in het gedeelte **Name and SSO location**:

    ![Schermopname met de naam en SSO-locatie waarbij Azure AD de naam van de id-provider is.](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. In het tekstvak **Identity provider name** (bijvoorbeeld Azure AD) voegt u de naam van de id-provider toe.

    b. Klik op **Volgende**.

1. Controleer het handtekeningcertificaat en klik op **Volgende**.

    ![Schermopname van de Handtekeningverificatie.](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Voer de volgende stappen uit in de sectie **Bitbucket user accounts**:

    ![Schermopname van BitBucket-gebruikersaccounts waarvoor u de mogelijkheid hebt om gebruikers te maken.](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Selecteer **Create users in Bitbucket's internal Directory if needed** en voer de juiste naam van de groep voor gebruikers in (dit kunnen meerdere aantallen groepen zijn, gescheiden door komma's).

    b. Klik op **Volgende**.

1. Klik op **Voltooien**.

    ![Schermopname van de Samenvattingspagina.](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Voer in het gedeelte **Known domains for Azure AD** de volgende stappen uit:

    ![Schermopname van de Bekende domeinen voor Azure AD waar u deze stappen kunt uitvoeren.](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Selecteer **Known domains** in het linkerdeelvenster van de pagina.

    b. Voer de domeinnaam in het tekstvak **Known domains** in.

    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Kantega SSO for Bitbucket.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Kantega SSO for Bitbucket**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Kantega SSO for Bitbucket** in de lijst met toepassingen.

    ![De koppeling Kantega SSO for Bitbucket in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Testgebruiker voor Kantega SSO for Bitbucket maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Bitbucket, moeten ze worden ingericht in Bitbucket. In het geval van Kantega SSO for Bitbucket is inrichting een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Bitbucket.

1. Klik op het instellingenpictogram.

    ![Schermopname van het pictogram Instellingen.](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Klik in de tabbladsectie **Beheer** op **Gebruikers**.

    ![Schermopname van BitBucket-beheer met Gebruikers geselecteerd. ](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klik op **Gebruiker maken**.

    ![Schermopname van BitBucket-beheer met Gebruiker maken geselecteerd.](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. In het dialoogvenster **Nieuwe gebruiker maken** voert u de volgende stappen uit:

    ![Schermopname van het dialoogvenster Gebruiker maken waarin u deze stappen kunt uitvoeren.](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker, bijvoorbeeld Britta Simon.

    c. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Voer in het tekstvak **Wachtwoord bevestigen** het wachtwoord van de gebruiker opnieuw in.

    f. Klik op **Gebruiker maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Kantega SSO voor Bitbucket klikt, wordt u automatisch aangemeld bij het exemplaar van Kantega SSO voor Bitbucket waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)