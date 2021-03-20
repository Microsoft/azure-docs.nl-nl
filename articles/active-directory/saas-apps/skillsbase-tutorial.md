---
title: 'Zelfstudie: Azure Active Directory-integratie met Skills Base | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Skills Base.
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
ms.openlocfilehash: 9b712756b6daf7380998bb98f56113cab29ebb59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015159"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Zelfstudie: Azure Active Directory-integratie met Skills Base

In deze zelfstudie leert u hoe u Skills Base kunt integreren met Azure Active Directory (Azure AD).
De integratie van Skills Base met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Skills Base.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Skills Base (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Skills Base, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Skills Base waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Skills Base ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Skills Base ondersteunt het **Just In Time** inrichten van gebruikers

## <a name="adding-skills-base-from-the-gallery"></a>Skills Base toevoegen vanuit de galerie

Voor het configureren van de integratie van Skills Base in Azure AD moet u Skills Base vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Skills Base toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Skills Base** in het zoekvak, selecteer **Skills Base** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Skills Base in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Azure AD met Skills Base configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Skills Base tot stand is gebracht.

Om eenmalige aanmelding bij Azure AD met Skills Base te configureren en te testen, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Skills Base configureren](#configure-skills-base-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Skills Base maken](#create-skills-base-test-user)** : als u een tegenhanger van Britta Simon in Skills Base wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding bij Azure AD met Skills Base te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de toepassingsintegratiepagina van **Skills Base** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Skills Base-domein en URL's voor eenmalige aanmelding](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE]
    > U kunt de aanmeldings-URL ophalen in de Skills Base-toepassing. Meld u aan als beheerder en ga naar Beheer-> Instellingen-> Exemplaardetails -> Snelkoppeling. Kopieer de aanmeldings-URL en plak deze in het bovenstaande tekstvak.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Skills Base instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-skills-base-single-sign-on"></a>Eenmalige aanmelding met Skills Base configureren

1. Meld u in een ander browservenster als beveiligingsbeheerder aan bij Skills Base.

2. Klik aan de linkerkant van het menu onder **BEHEERDER** op **Verificatie**.

    ![De beheerder](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

3. Selecteer op de pagina **Verificatie** de optie **SAML 2** voor eenmalige aanmelding.

    ![Schermopname van de verificatiepagina met SAML 2 geselecteerd voor eenmalige aanmelding.](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

4. Voer de volgende stappen uit op de pagina **Verificatie**:

    ![Schermopname van de verificatiepagina waarin u de beschreven waarden kunt invoeren.](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Klik op de knop **IdP-metagegevens bijwerken** naast de optie **Status** en plak de inhoud van de XML met metagegevens die u in de Azure-portal hebt gedownload, in het opgegeven tekstvak.

    > [!Note]
    > U kunt IdP-metagegevens ook valideren via het hulpprogramma **Metagegevensvalidatie**, zoals gemarkeerd in de bovenstaande schermopname.

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
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding met Azure te gebruiken door toegang te verlenen tot Skills Base.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Skills Base**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Skills Base** in de lijst met toepassingen.

    ![De Skills Base-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-skills-base-test-user"></a>Skills Base-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Skills Base. Skills Base ondersteunt het Just In Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Skills Base bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

> [!Note]
> Als u een gebruiker handmatig moet maken, volgt u de instructies [hier](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Skills Base in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Skills Base waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)