---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kantega SSO voor Confluence | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Kantega SSO voor Confluence.
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
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646211"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Kantega SSO voor Confluence

In deze zelfstudie leert u hoe u Kantega SSO voor Confluence kunt integreren met Azure AD (Active Directory).
Integratie van Kantega SSO voor Confluence met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Kantega SSO voor Confluence.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Kantega SSO (eenmalige aanmelding) voor Confluence.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Kantega SSO voor Confluence wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Kantega SSO voor Confluence waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kantega SSO voor Confluence ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Kantega SSO voor Confluence toevoegen vanuit de galerie

Om de integratie van Kantega SSO voor Confluence in Azure AD te configureren, moet u Kantega SSO voor Confluence uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Kantega SSO voor Confluence toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Kantega SSO voor Confluence** in het zoekvak, selecteer **Kantega SSO voor Confluence** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Kantega SSO voor Confluence in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met Kantega SSO voor Confluence op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Kantega SSO voor Confluence tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Kantega SSO voor Confluence wilt configureren en testen, moet u de volgende stappen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Kantega SSO voor Confluence configureren](#configure-kantega-sso-for-confluence-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Kantega SSO voor Confluence maken](#create-kantega-sso-for-confluence-test-user)** : als u een tegenhanger van Britta Simon in Kantega SSO voor Confluence wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding van Azure AD wilt configureren voor Kantega SSO voor Confluence, voert u de volgende stappen uit:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Kantega SSO voor Confluence** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname van de sectie 'Standaard SAML-configuratie' met de velden 'Id' en 'Antwoord-URL' gemarkeerd en de knop 'Opslaan' geselecteerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Kantega SSO voor Confluence](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Deze waarden krijgt u tijdens de configuratie van Confluence, wat later in de zelfstudie wordt uitgelegd.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Kantega SSO voor Confluence instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Eenmalige aanmelding voor Kantega SSO voor Confluence configureren

1. Meld u in een ander browservenster als een beheerder aan bij de **Confluence-beheerportal**.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Schermopname waarin het menupictogram 'Tandwiel' en 'Invoegtoepassingen' geselecteerd worden weergegeven.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Op het tabblad **ATLASSIAN MARKETPLACE** klikt u op **Nieuwe invoegtoepassingen zoeken**.

    ![Schermopname van het tabblad 'ATTLASSIAN MARKETPLACE' met 'Nieuwe invoegtoepassingen zoeken' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Zoek **Kantega SSO voor Confluence (SAML en Kerberos)** en klik op de knop **Installeren** om de nieuwe SAML-invoegtoepassing te installeren.

    ![Schermopname waarin de pagina 'Nieuwe invoegtoepassingen zoeken' wordt weergegeven met 'Kantega SSO voor Confluence SAML Kerberos' in het zoekvak en de knop 'Installeren' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. De installatie van de invoegtoepassing wordt gestart.

    ![Schermopname van het scherm 'Installeren' van de invoegtoepassing.](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Nadat de installatie is voltooid. Klik op **Sluiten**.

    ![Schermopname van het scherm 'Geïnstalleerd en klaar voor gebruik' en de actie 'Sluiten' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klik op **Beheren**.

    ![Schermopname waarin de invoegtoepassing 'Eenmalige aanmelding voor Kantega met Kerberos en SAML' wordt weergegeven met de knop 'Beheren' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname waarin de pagina 'Kantega Eenmalige aanmelding met Kerberos en SAML' wordt weergegeven met de knop 'Configureren' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Deze nieuwe invoegtoepassing is ook terug te vinden op het tabblad **GEBRUIKERS EN BEVEILIGING**.

    ![Schermopname van het tabblad 'GEBRUIKERS & BEVEILIGING' met de actie 'Eenmalige aanmelding voor Kantega' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. In het gedeelte **SAML**. Selecteer **Azure Active Directory (Azure AD)** in de vervolgkeuzelijst **Id-provider toevoegen**.

    ![Schermopname van de sectie 'SAML' met 'Id-provider toevoegen' en 'Azure Active Directory (Azure AD)' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Selecteer als abonnementsniveau de optie **Basic**.

    ![Schermopname van de sectie 'Azure AD voorbereiden' met 'Basic' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Voer de volgende stappen uit in het gedeelte **App-eigenschappen**:

    ![Schermopname van de sectie 'App-eigenschappen' met het veld 'App ID URL' en 'Kopiëren' gemarkeerd en de knop 'Volgende' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopieer de waarde voor de **app-id-URI** en gebruik deze als **id, antwoord-URL en aanmeldings-URL** in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit in het gedeelte **Metagegevens importeren**: 

    ![Schermopname van de sectie 'Importeren metagegevens' met 'Metagegevensbestand op mijn computer' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Selecteer **Metadata file on my computer** en upload het metagegevensbestand dat u vanuit Azure Portal hebt gedownload.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit op in het gedeelte **Name and SSO location**:

    ![Schermopname van de 'Naam en SSO-locatie', waarbij het tekstvak 'Naam van de id-provider' is gemarkeerd en de knop 'Volgende' is geselecteerd.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. In het tekstvak **Identity provider name** (bijvoorbeeld Azure AD) voegt u de naam van de id-provider toe.

    b. Klik op **Volgende**.

1. Controleer het handtekeningcertificaat en klik op **Volgende**.

    ![Schermopname van de sectie 'Handtekeningverificatie' met de knop 'Volgende' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Voer de volgende stappen uit in de sectie **Confluence user accounts**:

    ![Scherm afbeelding met de sectie ' confluence-gebruikers accounts ' met de optie ' gebruikers in de interne Directory van de confluence maken indien nodig ' en de knop volgende geselecteerd.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Selecteer **Create users in Confluence's internal Directory if needed** en voer de juiste naam van de groep voor gebruikers in (dit kunnen meerdere aantallen groepen zijn, gescheiden door komma's).

    b. Klik op **Volgende**.

1. Klik op **Voltooien**.

    ![Schermopname van de pagina 'Samenvatting' met de knop 'Voltooien' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Voer in het gedeelte **Known domains for Azure AD** de volgende stappen uit: 

    ![Schermopname van de pagina 'Bekende domeinen voor Azure AD' met het tekstvak 'Bekende domeinen' gemarkeerd en de knop 'Opslaan' geselecteerd.](./media/kantegassoforconfluence-tutorial/addon12.png)

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Kantega SSO voor Confluence.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Kantega SSO voor Confluence**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Kantega SSO voor Confluence** in de lijst met toepassingen.

    ![De koppeling Kantega SSO voor Confluence in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Testgebruiker voor Kantega SSO voor Confluence maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Confluence, moeten ze worden ingericht in Confluence. In het geval van Kantega SSO voor Confluence is inrichting een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beheerder aan bij de bedrijfssite van Kantega SSO voor Confluence.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Schermopname met het pictogram 'Tandwiel' en 'Gebruikersbeheer' geselecteerd.](./media/kantegassoforconfluence-tutorial/user1.png)

1. Klik onder de sectie Users op het tabblad **Add Users**. Voer de volgende stappen uit in het dialoogvenster **Add a User**:

    ![Werknemer toevoegen](./media/kantegassoforconfluence-tutorial/user2.png)

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. Typ in het tekstvak **Full Name** de volledige naam van de gebruiker, zoals Britta Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. Typ in het tekstvak **Password** het wachtwoord van de gebruiker.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.

    f. Klik op de knop **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Kantega SSO for Confluence klikt, wordt u automatisch aangemeld bij het exemplaar van Kantega SSO for Confluence waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)