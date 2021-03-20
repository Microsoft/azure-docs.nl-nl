---
title: 'Zelfstudie: Azure Active Directory-integratie met Predictix Price Reporting | Microsoft Docs'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Predictix Price Reporting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 801197a171b5be2cfe46c249398381e1b25857ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515352"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Zelfstudie: Azure Active Directory-integratie met Predictix Price Reporting

In deze zelfstudie leert u hoe u Predictix Price Reporting kunt integreren met Azure Active Directory (Azure AD).

Deze integratie biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang krijgt tot Predictix Price Reporting.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Predictix Price Reporting (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Predictix Price Reporting wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich registreren voor een [proefabonnement van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een Predictix Price Reporting-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Predictix Price Reporting ondersteunt door SP geïnitieerde eenmalige aanmelding.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Predictix Price Reporting toevoegen vanuit de galerie

Als u de integratie van Predictix Price Reporting in Azure AD wilt instellen, moet u Predictix Price Reporting vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**:

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** > **Alle toepassingen**:

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

    ![Selecteer Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Predictix Price Reporting** in. Selecteer **Predictix Price Reporting** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding voor Azure AD met Predictix Price Reporting door een testgebruiker te gebruiken met de naam Britta Simon.
Om eenmalige aanmelding in te schakelen, moet u een relatie maken tussen een Azure AD-gebruiker en de desbetreffende gebruiker in Predictix Price Reporting.

Als u eenmalige aanmelding voor Azure AD met Predictix Price Reporting wilt configureren en testen, moet u deze stappen voltooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te schakelen.
2. **[Configureer Predictix Price Reporting voor eenmalige aanmelding](#configure-predictix-price-reporting-single-sign-on)** aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** om eenmalige aanmelding in te schakelen voor de gebruiker.
5. **[Maak een Predictix Price Reporting-testgebruiker](#create-a-predictix-price-reporting-test-user)** die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding voor Azure AD met Predictix Price Reporting wilt configureren, moet u deze stappen voltooien:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **Predictix Price Reporting**-toepassing de optie **Eenmalige aanmelding**:

    ![Selecteer Eenmalige aanmelding](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen:

    ![Pictogram bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit.

    ![Dialoogvenster Standaard SAML-configuratie](common/sp-identifier.png)

    1. Voer in het vak **Aanmeldings-URL** een URL met dit patroon in:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Voer in het vak **Identifier (Entity ID)** een URL in met dit patroon:

        ```https
        https://<companyname-pricing>.predictix.com
        https://<companyname-pricing>.dev.predictix.com
        ```

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. Hiervoor moet u de werkelijke aanmeldings-URL en id gebruiken. Neem contact op met het [ondersteuningsteam van Predictix Price Reporting](https://www.infor.com/company/customer-center/) om de waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het dialoogvenster **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen**, in de sectie **SAML-handtekeningcertificaat**, selecteert u de koppeling **Downloaden** naast **Certificaat (Base64)** overeenkomstig uw behoeften, en slaat u het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Predictix Price Reporting instellen** de juiste URL's op basis van uw vereisten.

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Azure AD-id**.

    1. **Afmeldings-URL**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Configureer Predictix Price Reporting voor eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren voor Predictix Price Reporting, moet u het certificaat dat u hebt gedownload en de URL's die u hebt gekopieerd van de Azure-portal naar het [ondersteuningsteam van de Predictix Price Reporting](https://www.infor.com/company/customer-center/) verzenden. Zij zorgen ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm:

    ![Selecteer Nieuwe gebruiker](common/new-user.png)

3. Voer in het dialoogvenster **Gebruiker** de volgende stappen uit.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Naam** **Britta Simon@\<yourcompanydomain> in.\<extension>** . (Bijvoorbeeld: BrittaSimon@contoso.com.)

    1. Selecteer **Wachtwoord weergeven** en noteer de waarde die in het vak **Wachtwoord** wordt getoond.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon instellen voor gebruik van eenmalige aanmelding voor Azure AD door haar toegang te geven tot Predictix Price Reporting.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Predictix Price Reporting**.

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Predictix Price Reporting**.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeelvenster **Gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de gebruikerslijst en selecteer vervolgens de knop **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het scherm op de knop **Selecteren**.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Een Predictix Price Reporting-testgebruiker maken

Vervolgens moet u een gebruiker met de naam Britta Simon maken in Predictix Price Reporting. Werk samen met het [ondersteuningsteam van Predictix Price Reporting](https://www.infor.com/company/customer-center/) om gebruikers toe te voegen. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie voor eenmalige aanmelding van Azure AD testen met behulp van het toegangsvenster.

Wanneer u de tegel Predictix Price Reporting selecteert in het toegangsvenster, wordt u automatisch aangemeld bij de Predictix Price Reporting-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Toepassingen openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)