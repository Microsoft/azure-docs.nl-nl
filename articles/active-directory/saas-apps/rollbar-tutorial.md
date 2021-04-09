---
title: 'Zelfstudie: Integratie van Azure Active Directory met Rollbar | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Rollbar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 2d5aedf24034c9ba5ee865dd0d2289169ea5f859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520645"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Zelfstudie: Integratie van Azure Active Directory met Rollbar

In deze zelfstudie leert u hoe u Rollbar kunt integreren met Azure Active Directory (Azure AD).
Integratie van Rollbar met Azure AD heeft de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Rollbar.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Rollbar (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met Rollbar, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Rollbar waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rollbar ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-rollbar-from-the-gallery"></a>Rollbar toevoegen vanuit de galerie

Om de integratie van Rollbar in Azure AD te configureren, moet u Rollbar vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Rollbar wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Rollbar** in het zoekvak, selecteer **Rollbar** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Rollbar in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Rollbar configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Rollbar tot stand is gebracht.

U moet de volgende bouwstenen voltooien om eenmalige aanmelding met Azure AD bij Rollbar te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Rollbar configureren](#configure-rollbar-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Rollbar](#create-rollbar-test-user)** : als u een tegenhanger van Britta Simon in Rollbar wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Rollbar:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Rollbar**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u deze URL: `https://saml.rollbar.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het klantondersteuningsteam van Rollbar](mailto:support@rollbar.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **Rollbar instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rollbar-single-sign-on"></a>Eenmalige aanmelding configureren voor Rollbar

1. Meld u in een andere webbrowser als beheerder aan bij uw Rollbar-bedrijfssite.

1. Klik in de rechterbovenhoek op **Profile Settings** en vervolgens op **Account Name Settings**.

    ![Schermopname van Account Name Settings geselecteerd in Profile Settings.](./media/rollbar-tutorial/general.png)

1. Klik onder SECURITY op **Identity Provider**.

    ![Schermopname van Identity Provider geselecteerd onder SECURITY.](./media/rollbar-tutorial/configure1.png)

1. Voer in de sectie **SAML Identity Provider** de volgende stappen uit:

    ![Schermopname van de SAML Identity Provider, waar u de beschreven waarden kunt invoeren.](./media/rollbar-tutorial/configure2.png)

    a. Selecteer **AZURE** in de vervolgkeuzelijst **SAML Identity Provider**.

    b. Open het gedownloade metagegevensbestand in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **SAML Metadata**.

    c. Klik op **Opslaan**.

1. Nadat u op de knop Opslaan hebt geklikt, wordt het scherm als volgt weergegeven:

    ![Schermopname van de resultaten op de pagina SAML Identity Provider.](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Om de volgende stap uit te voeren, moet u zich eerst als gebruiker toevoegen aan de Rollbar-app in Azure.
    >

    a. Als u wilt dat alle gebruikers moeten worden geverifieerd via Azure, klikt u op **log in via your identity provider** voor herverificatie via Azure.  

    b.  Weer terug in het scherm, schakelt u het selectievakje **Require login via SAML Identity Provider** in.

    b. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Rollbar.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Rollbar**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rollbar** in de lijst met toepassingen.

    ![De koppeling Rollbar in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rollbar-test-user"></a>Testgebruiker maken voor Rollbar

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Rollbar, moeten ze worden ingericht in Rollbar. In het geval van Rollbar is het inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de Rollbar-bedrijfssite.

1. Klik in de rechterbovenhoek op **Profile Settings** en vervolgens op **Account Name Settings**.

    ![Gebruiker](./media/rollbar-tutorial/general.png)

1. Klik op **Users**.

    ![Werknemer toevoegen](./media/rollbar-tutorial/user1.png)

1. Klik op **Invite Team Members**.

    ![Schermopname van de geselecteerde optie Invite Team Members.](./media/rollbar-tutorial/user2.png)

1. Voer in het tekstvak de naam van de gebruiker in, zoals **brittasimon\@contoso.com** en klik op **Add/Invite** (Toevoegen/Uitnodigen).

    ![Schermopname van Add/Invite Members met een opgegeven adres.](./media/rollbar-tutorial/user3.png)

1. De gebruiker ontvangt een uitnodiging en nadat deze is geaccepteerd, worden deze in het systeem gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Rollbar klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Rollbar in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)