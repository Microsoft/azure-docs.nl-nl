---
title: 'Zelfstudie: Integratie van Azure Active Directory met Optimizely | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Optimizely configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c10681a053631a338bd0b9a7d19c5625151a9a5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515794"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Zelfstudie: Integratie van Azure Active Directory met Optimizely

In deze zelfstudie leert u hoe u Optimizely kunt integreren met Azure Active Directory (Azure AD).
Integratie van Optimizely met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Optimizely.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Optimizely (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Optimizely hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Optimizely waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Optimizely ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-optimizely-from-the-gallery"></a>Optimizely toevoegen vanuit de galerie

Om de integratie van Optimizely te configureren in Azure AD, moet u Optimizely uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u Optimizely wilt toevoegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Optimizely** in het zoekvak, selecteer **Optimizely** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Optimizely in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Optimizely op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Optimizely tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Optimizely wilt configureren en testen, moet u de volgende onderdelen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Optimizely configureren](#configure-optimizely-single-sign-on)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Optimizely maken](#create-optimizely-test-user)** : als u een tegenhanger van Britta Simon in Optimizely wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD wilt configureren voor Optimizely:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Optimizely**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Optimizely](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.optimizely.net/<instance name>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Dit zijn niet de echte waarden. U werkt deze waarden bij met de werkelijke URL en id voor aanmelding, zoals verderop in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de Optimizely-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname met het dialoogvenster Gebruikerskenmerken met de knop Bewerken rechtsboven geselecteerd.](common/edit-attribute.png)

6. Bovendien verwacht de Optimizely-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk |
    | ---------------| --------------- |
    | e-mail | user.mail |
    
    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname met het dialoogvenster Gebruikersclaims met de acties 'Nieuwe claim toevoegen' en 'Opslaan' gemarkeerd.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Optimizely instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-optimizely-single-sign-on"></a>Eenmalige aanmelding configureren voor Optimizely

1. Als u eenmalige aanmelding wilt configureren in **Optimizely**, neemt u contact op met de accountmanager van Optimizely en verstrekt u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's.

2. Als reactie op uw e-mail verstrekt Optimizely u de waarden voor de URL (door SP geïnitieerde eenmalige aanmelding) en de id (entiteits-id van serviceprovider) voor aanmelding.

    a. Kopieer de waarde van de **URL voor door SP geïnitieerde eenmalige aanmelding** die door Optimizely is verstrekt en plak deze in het tekstvak **Aanmeldings-URL** in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    b. Kopieer de waarde van de **entiteits-id van de serviceprovider** en plak deze in het tekstvak **Id** in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

3. Meld u in een ander browservenster aan bij uw Optimizely-toepassing.

4. Klik op accountnaam in de rechterbovenhoek en klik vervolgens **Account Settings**.

    ![Schermopname van de geselecteerde accountnaam in de rechter bovenhoek, met de optie accountinstellingen geselecteerd in het menu.](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Schakel op het tabblad Account het selectievakje **Enable SSO** onder Single Sign On in het gedeelte **Overview** in.
  
    ![Eenmalige aanmelding van Azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Optimizely.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Optimizely**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Optimizely** in de lijst met toepassingen.

    ![De Optimizely-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-optimizely-test-user"></a>Optimizely-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Optimizely.

1. Selecteer op de startpagina het tabblad **Collaborators**.

2. Om een nieuwe samenwerker aan het project toe te voegen, klikt u op **New Collaborator**.
   
    ![Schermopname met de pagina Optimalisatie op basis van het tabblad Samenwerkers en de knop Nieuwe samenwerker geselecteerd.](./media/optimizely-tutorial/create_aaduser_10.png)

3. Vul het e-mailadres in en wijs een rol toe. Klik op **Uitnodigen**.

    ![Een Azure AD-testgebruiker maken](./media/optimizely-tutorial/create_aaduser_11.png)

4. De persoon ontvangt een e-mailuitnodiging. Deze moet zich met het e-mailadres aanmelden bij Optimizely.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Optimizely in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Optimizely waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)