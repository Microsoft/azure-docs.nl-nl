---
title: 'Zelfstudie: Azure Active Directory-integratie met IdeaScale | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en IdeaScale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: ef04bf9f6d9ca6ff5089d2826c05a5acfb759b04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001657"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Zelfstudie: Azure Active Directory-integratie met IdeaScale

In deze zelfstudie leert u hoe u IdeaScale kunt integreren met Azure Active Directory (Azure AD).
De integratie van IdeaScale met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot IdeaScale.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij IdeaScale (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met IdeaScale hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op IdeaScale waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* IdeaScale ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-ideascale-from-the-gallery"></a>IdeaScale vanuit de galerie toevoegen

Voor het configureren van de integratie van IdeaScale in Azure AD moet u IdeaScale vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u IdeaScale vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **IdeaScale**, selecteer **IdeaScale** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![IdeaScale in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met IdeaScale op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in IdeaScale tot stand is gebracht.

Om Azure AD-eenmalige aanmelding met IdeaScale te configureren en te testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[IdeaScale voor eenmalige aanmelding configureren](#configure-ideascale-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor IdeaScale maken](#create-ideascale-test-user)** : als u een tegenhanger van Britta Simon in IdeaScale wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure Active Directory met IdeaScale moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **IdeaScale** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij IdeaScale](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.ideascale.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:
    
    ```http
    http://<companyname>.ideascale.com
    https://<companyname>.ideascale.com
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [IdeaScale-ondersteuningsteam](https://support.ideascale.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **IdeaScale instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ideascale-single-sign-on"></a>Eenmalige aanmelding voor IdeaScale configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van IdeaScale.

2. Ga naar **Community-instellingen**.

    ![Community-instellingen](./media/ideascale-tutorial/ic790847.png "Community-instellingen")

3. Ga naar **Beveiliging \> Instellingen voor eenmalige aanmelding**.

    ![Schermopname met daarin de instellingen voor eenmalige aanmelding geselecteerd in het menu Beveiliging.](./media/ideascale-tutorial/ic790848.png "Instellingen voor eenmalige aanmelding")

4. Selecteer **SAML 2.0** als **Type eenmalige aanmelding**.

    ![Type eenmalige aanmelding](./media/ideascale-tutorial/ic790849.png "Type eenmalige aanmelding")

5. Voer in het dialoogvenster **Instellingen voor eenmalige aanmelding** de volgende stappen uit:

    ![Schermopname van het dialoogvenster met instellingen voor eenmalige aanmelding.](./media/ideascale-tutorial/ic790850.png "Instellingen voor eenmalige aanmelding")

    a. Plak in het tekstvak **SAML idP entiteits-id** de waarde van de **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    b. Open het uit de Azure-portal gedownloade metagegevensbestand in Kladblok, kopieer de inhoud ervan en plak deze in het tekstvak **SAML IdP metagegevens**.

    c. Plak in het tekstvak **Afmelding voltooid-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Klik op **Wijzigingen opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot IdeaScale.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, **Alle toepassingen** en vervolgens **IdeaScale**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **IdeaScale** in de lijst met toepassingen.

    ![De IdeaScale-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ideascale-test-user"></a>IdeaScale-testgebruiker maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij IdeaScale, moeten ze worden ingericht voor IdeaScale. In het geval van IdeaScale is het inrichten een handmatige taak.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw **IdeaScale**-bedrijfssite als beheerder.

2. Ga naar **Community-instellingen**.

    ![Community-instellingen](./media/ideascale-tutorial/ic790847.png "Community-instellingen")

3. Ga naar **Basisinstellingen \> Leden beheren**.

4. Klik op **Lid toevoegen**.

    ![Ledenbeheer](./media/ideascale-tutorial/ic790852.png "Ledenbeheer")

5. Voer in het gedeelte Nieuw lid toevoegen de volgende stappen uit:

    ![Nieuw lid toevoegen](./media/ideascale-tutorial/ic790853.png "Nieuw lid toevoegen")

    a. Typ in het tekstvak **E-mailadressen** het e-mailadres van een geldig Azure AD-account dat u wilt inrichten.

    b. Klik op **Wijzigingen opslaan**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.

> [!NOTE]
> U kunt de Aazure AD-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van IdeaScale voor het maken van gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel IdeaScale klikt, wordt u automatisch aangemeld bij de instantie van IdeaScale waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)