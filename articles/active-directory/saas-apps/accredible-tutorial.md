---
title: 'Zelfstudie: Integratie van Microsoft Azure Active Directory met Accredible | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding tussen Azure Active Directory en Accredible configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: cf74b563352ccd0f62e583bccccd72078cbb2046
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673786"
---
# <a name="tutorial-azure-active-directory-integration-with-accredible"></a>Zelfstudie: Integratie van Azure Active Directory met Accredible

In deze zelfstudie leert u hoe u Accredible kunt integreren met Azure Active Directory (Azure AD).
Het integreren van Accredible met Microsoft Azure Active Directory biedt u de volgende voordelen:

* U kunt in Microsoft Azure Active Directory beheren wie toegang heeft tot Accredible.
* U kunt instellen dat gebruikers automatisch met hun Microsoft Azure Active Directory-account worden aangemeld bij Accredible (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Microsoft Azure Active Directory-integratie met Accredible hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Accredible waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Accredible ondersteunt door **IDP** geïnitieerde SSO

## <a name="adding-accredible-from-the-gallery"></a>Accredible toevoegen vanuit de galerie

Voor het configureren van de integratie van Accredible met Microsoft Azure Active Directory, moet u Accredible vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Als u Accredible vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Accredible**, selecteer **Accredible** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Accredible in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met Accredible op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Accredible tot stand is gebracht.

Om eenmalige aanmelding van Microsoft Azure Active Directory met Accredible te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Accredible-eenmalige aanmelding configureren](#configure-accredible-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Accredible maken](#create-accredible-test-user)** : als u een tegenhanger van Britta Simon in Accredible wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Om eenmalige aanmelding van Microsoft Azure Active Directory met Accredible te configureren, moet u de volgende stappen uitvoeren:

1. In de [Microsoft Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Accredible**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over Accredible-domein en -URL’s voor eenmalige aanmelding](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    ```http
    https://api.accredible.com/sp/admin/accredible
    https://api.accredible.com/sp/user/accredible
    ```

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://api.accredible.com/v1/saml/admin/<Unique id>/consume`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Gebruik de id-waarde respectievelijk op basis van de rol van de gebruiker. Elke klant heeft een unieke antwoord-URL, afhankelijk van zijn ID. Neem contact op met het [Accredible-ondersteuningsteam](mailto:support@accredible.com) om deze waarden te verkrijgen.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In het gedeelte **Accredible instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-accredible-single-sign-on"></a>Eenmalige aanmelding voor Accredible configureren

Als u eenmalige aanmelding aan de zijde van **Accredible** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Microsoft Azure-portal gekopieerde URL's naar het [Accredible-ondersteuningsteam](mailto:support@accredible.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.

    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Accredible.

1. Selecteer **Bedrijfstoepassingen** in de Microsoft Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Accredible**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Accredible** in de lijst met toepassingen.

    ![De koppeling Accredible in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-accredible-test-user"></a>Accredible-testgebruiker maken

In deze sectie gaat u een gebruiker maken met de naam Britta Simon in Accredible. U moet de e-mail-id van de gebruiker verzenden naar het[Accredible-ondersteuningsteam](mailto:support@accredible.com). Dit team verifieert de e-mail en stuurt u de uitnodigingsmail zodat u de gebruiker kunt toevoegen aan het Accredible-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Accredible in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Accredible waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)