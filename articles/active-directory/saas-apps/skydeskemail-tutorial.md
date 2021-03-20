---
title: 'Zelfstudie: Integratie van Azure Active Directory met SkyDesk Email | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SkyDesk Email.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516019"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Zelfstudie: Integratie van Azure Active Directory met SkyDesk Email

In deze zelfstudie leert u hoe u SkyDesk Email kunt integreren met Azure Active Directory (Azure AD).
Integratie van SkyDesk Email met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SkyDesk Email.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SkyDesk Email (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met SkyDesk Email hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SkyDesk Email waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SkyDesk Email ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk Email toevoegen vanuit de galerie

Voor het configureren van de integratie van SkyDesk Email in Azure AD, moet u SkyDesk Email vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SkyDesk Email wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SkyDesk Email** in het zoekvak, selecteer **SkyDesk Email** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![SkyDesk Email in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met SkyDesk Email op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in SkyDesk Email tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met SkyDesk Email wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SkyDesk Email configureren](#configure-skydesk-email-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor SkyDesk Email maken](#create-skydesk-email-test-user)** : als u een tegenhanger van Britta Simon in SkyDesk Email wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SkyDesk Email, moet u de volgende stappen uitvoeren:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van **SkyDesk Email**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij SkyDesk Email](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [SkyDesk Email-clientondersteuningsteam](https://www.skydesk.jp/apps/support/) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **SkyDesk Email instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Eenmalige aanmelding voor SkyDesk Email configureren

1. Meld u in een andere webbrowser als beheerder aan bij uw SkyDesk Email-account.

1. Klik in het menu bovenaan op **Setup** en klik vervolgens op **Org**.

    ![Schermopname van Org geselecteerd in het Setup-menu.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klik op in het linkerdeelvenster op **Domains** (Domeinen).

    ![Schermopname van Domains geselecteerd in Control Panel.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klik op **Add Domain** (Domein toevoegen).

    ![Schermopname waarin Add Domain is geselecteerd.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Voer uw domeinnaam in en controleer het domein.

    ![Schermopname van het tabblad Add Domain waar u uw domein kunt invoeren.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klik in het linkerdeelvenster op **SAML Authentication**.

    ![Schermopname van SAML Authentication geselecteerd in Control Panel.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Voer in het dialoogvenster **SAML Authentication** de volgende stappen uit:

    ![Schermopname van het dialoogvenster SAML Authentication Details waarin u de beschreven waarden kunt invoeren.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Als u op SAML gebaseerde verificatie wilt gebruiken, moet u **geverifieerde domein**- of **Portal-URL**-instellingen hebben. U kunt de portal-URL instellen met de unieke naam.

    ![Schermopname van de Portal U R L waar u de naam invoert.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Het veld **Change Password URL** is optioneel. Laat het veld leeg.

    d. Klik op **Get Key From File** om het gedownloade certificaat te selecteren in Azure Portal en klik vervolgens op **Open** om het certificaat te uploaden.

    e. Selecteer **RSA** als **algoritme**.

    f. Klik op **OK** om de wijzigingen op te slaan.

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot SkyDesk Email.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SkyDesk Email**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **SkyDesk Email**.

    ![De SkyDesk Email-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-skydesk-email-test-user"></a>Testgebruiker voor SkyDesk Email maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in SkyDesk Email.

Klik in het linkerpaneel in SkyDesk Email op **User Access** en voer uw gebruikersnaam in.

![Schermopname van User Access geselecteerd in Control Panel.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Als u bulkgebruikers wilt maken, neemt u contact op met het [klantondersteuningsteam van SkyDesk Email](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SkyDesk Email klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van SkyDesk Email waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)