---
title: 'Zelfstudie: Azure Active Directory-integratie met ScreenSteps | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ScreenSteps.
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
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999787"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Zelfstudie: Azure Active Directory-integratie met ScreenSteps

In deze zelfstudie leert u hoe u ScreenSteps kunt integreren met Azure Active Directory (Azure AD).
De integratie van ScreenSteps met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot ScreenSteps.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ScreenSteps (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met ScreenSteps hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op ScreenSteps waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ScreenSteps ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps toevoegen vanuit de galerie

Voor het configureren van de integratie van ScreenSteps in Azure Active Directory, moet u ScreenSteps uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u ScreenSteps wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **ScreenSteps**, selecteer **ScreenSteps** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![ScreenSteps in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Azure Active Directory met ScreenSteps configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in ScreenSteps tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met ScreenSteps, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor ScreenSteps configureren](#configure-screensteps-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor ScreenSteps maken](#create-screensteps-test-user)** : als u een tegenhanger van Britta Simon in ScreenSteps wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding bij Azure Active Directory wilt configureren met ScreenSteps:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **ScreenSteps**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij ScreenSteps](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Deze waarde is niet echt. Vervang deze waarde door de werkelijke Aanmeldings-URL op de manier die verderop in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Set up ScreenSteps** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-screensteps-single-sign-on"></a>Eenmalige aanmelding voor ScreenSteps configureren

1. Meld u in een ander browservenster aan bij uw ScreenSteps-bedrijfssite als beheerder.

1. Klik op **Accountinstellingen**.

    ![Accountbeheer](./media/screensteps-tutorial/ic778523.png "Accountbeheer")

1. Klik op **Eenmalige aanmelding**.

    ![Schermopname met "Eenmalige aanmelding" geselecteerd.](./media/screensteps-tutorial/ic778524.png "Externe verificatie")

1. Klik op **Eindpunt voor eenmalige aanmelding maken**.

    ![Externe verificatie](./media/screensteps-tutorial/ic778525.png "Externe verificatie")

1. Voer in het gedeelte **Eindpunt voor eenmalige aanmelding maken** de volgende stappen uit:

    ![Een verificatie-eindpunt maken](./media/screensteps-tutorial/ic778526.png "Een verificatie-eindpunt maken")

    a. Typ een titel in het tekstvak **Titel**.

    b. Selecteer **SAML** in de lijst **Modus**.

    c. Klik op **Create**.

1. **Bewerk** het nieuwe eindpunt.

    ![Eindpunt bewerken](./media/screensteps-tutorial/ic778528.png "Eindpunt bewerken")

1. Voer in het gedeelte **Eindpunt voor eenmalige aanmelding bewerken** de volgende stappen uit:

    ![Extern verificatie-eindpunt](./media/screensteps-tutorial/ic778527.png "Extern verificatie-eindpunt")

    a. Klik op **Nieuw SAML-certificaatbestand uploaden** en upload vervolgens het certificaat dat u heeft gedownload in het Azure-portaal.

    b. Plak de waarde van de **aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Remote Login URL**.

    c. Plak de waarde van **Afmeldings-URL**, die u heeft gekopieerd in het Azure-portaal in het tekstvak **Afmeldings-URL**.

    d. Selecteer een **Groep** om gebruikers aan toe te wijzen wanneer ze zijn ingericht.

    e. Klik op **Update**.

    f. Kopieer de **SAML Consumer-URL** naar het klembord en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in het Azure-portaal.

    g. Ga terug naar **Eindpunt voor eenmalige aanmelding**.

    h. Klik op de knop **Standaard maken voor account** om dit eindpunt te gebruiken voor alle gebruikers die zich aanmelden bij ScreenSteps. U kunt ook op de knop **Toevoegen aan site** klikken om dit eindpunt te gebruiken voor specifieke sites in **ScreenSteps**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot ScreenSteps.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **ScreenSteps**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ScreenSteps** in de lijst met toepassingen.

    ![De koppeling naar ScreenSteps in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-screensteps-test-user"></a>ScreenSteps-testgebruiker maken

In deze sectie gaat u in ScreenSteps een gebruiker maken met de naam Britta Simon. Neem contact op met het [klantondersteuningsteam van ScreenSteps](https://www.screensteps.com/contact) om de gebruikers toe te voegen in het ScreenSteps-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ScreenSteps in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van ScreenSteps waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)