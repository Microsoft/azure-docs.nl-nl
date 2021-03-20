---
title: 'Zelfstudie: Azure Active Directory-integratie met Sedgwick CMS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sedgwick CMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: ccda5c050c9f8af711637ea45090b6647c6c3488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674659"
---
# <a name="tutorial-azure-active-directory-integration-with-sedgwick-cms"></a>Zelfstudie: Azure Active Directory-integratie met Sedgwick CMS

In deze zelfstudie leert u hoe u Sedgwick CMS kunt integreren met Azure Active Directory (Azure AD).
De integratie van Sedgwick CMS met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Sedgwick CMS.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-accounts worden aangemeld bij Sedgwick CMS (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Sedgwick CMS hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Sedgwick CMS waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sedgwick CMS ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-sedgwick-cms-from-the-gallery"></a>Sedgwick CMS toevoegen vanuit de galerie

Voor het configureren van de integratie van Sedgwick CMS met Azure Active Directory moet u Sedgwick CMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Sedgwick CMS wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Sedgwick CMS**, selecteer **Sedgwick CMS** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Sedgwick CMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Sedgwick CMS configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Sedgwick CMS tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij Sedgwick CMS te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Sedgwick CMS configureren](#configure-sedgwick-cms-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Sedgwick CMS](#create-sedgwick-cms-test-user)** : als u een tegenhanger van Britta Simon in Sedgwick CMS te hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding met Azure Active Directory wilt configureren met Sedgwick CMS:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Sedgwick CMS**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Sedgwick CMS](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    - `expresspreview.sedgwickcms.net/voe/sso`
    - `claimlookup.com/Voe/sso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://<subdomain>.sedgwickcms.net/voe/sso`
    - `https://claimlookup.com/Voe/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [clientondersteuningsteam van Sedgwick CMS](https://www.sedgwick.com/help) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Sedgwick CMS instellen** kopieert u de juiste URL('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sedgwick-cms-single-sign-on"></a>Eenmalige aanmelding voor Sedgwick CMS configureren

Als u eenmalige aanmelding aan de zijde van **Sedgwick CMS** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Sedgwick CMS](https://www.sedgwick.com/help). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie zorgt u ervoor dat Britta Simon eenmalige aanmelding met Azure kan gebruiken door haar toegang te geven tot Sedgwick CMS.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en vervolgens **Sedgwick CMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **Sedgwick CMS**.

    ![De Sedgwick CMS-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sedgwick-cms-test-user"></a>Sedgwick CMS-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Sedgwick CMS. Neem contact op met het [ondersteuningsteam van Sedgwick CMS](https://www.sedgwick.com/help) om de gebruikers toe te voegen in het Sedgwick CMS-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Sedgwick CMS klikt, wordt u automatisch aangemeld bij de instantie van Sedgwick CMS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)