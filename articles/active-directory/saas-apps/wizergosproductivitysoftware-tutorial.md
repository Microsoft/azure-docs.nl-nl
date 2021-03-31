---
title: 'Zelfstudie: Azure Active Directory-integratie met Wizergos Productivity Software | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Wizergos Productivity Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: b4cddae25bbf7ff113d2ea67700e28eb81c0e7c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92638020"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Zelfstudie: Azure Active Directory-integratie met Wizergos Productivity Software

In deze zelfstudie leert u hoe u Wizergos Productivity Software kunt integreren met Azure Active Directory (AD).
De integratie van Wizergos Productivity Software met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Wizergos Productivity Software.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Wizergos Productivity Software (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure Active Directory-integratie met Wizergos Productivity Software hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Wizergos Productivity Software waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wizergos Productivity Software biedt ondersteuning voor **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Wizergos Productivity Software toevoegen vanuit de galerie

Voor het configureren van de integratie van Wizergos Productivity Software met Azure AD moet u Wizergos Productivity Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Wizergos Productivity Software wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Wizergos Productivity Software** in het zoekvak, selecteer **Wizergos Productivity Software** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Wizergos Productivity Software in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Wizergos Productivity Software configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Wizergos Productivity Software tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure Active Directory bij Wizergos Productivity Software moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Wizergos Productivity Software configureren](#configure-wizergos-productivity-software-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Wizergos Productivity Software-testgebruiker maken](#create-wizergos-productivity-software-test-user)** : als u een equivalent van Britta Simon inWizergos Productivity Software wilt hebben dat gekoppeld is aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding voor Azure Active Directory met Wizergos Productivity Software voert u de volgende stappen uit:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Wizergos Productivity Software** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Wizergos Productivity Software](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://www.wizergos.net`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Wizergos Productivity Software instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Eenmalige aanmelding voor Wizergos Productivity Software configureren

1. Meld u in een ander browservenster als beheerder aan bij de Wizergos Productivity Software-tenant.

2. Selecteer in het Hamburgermenu de optie **Beheerder**.

    ![Schermopname waarin het pictogram Beheerder in het menu is geselecteerd.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. Selecteer op de pagina Beheerder in het linkermenu de optie **VERIFICATIE** en klik p **Azure AD**.

    ![Schermopname met Azure AD geselecteerd vanuit VERIFICATIE.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. Voer in de sectie **VERIFICATIE** de volgende stappen uit:

    ![Schermopname van de pagina VERIFICATIE waarin u de beschreven waarden kunt invoeren.](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klik op **UPLOADEN** om het gedownloade certificaat vanuit Azure AD te uploaden.
    
    b. Plak in het tekstvak **Issuer URL** de **Azure AD-ID** die u uit de Azure-portal hebt gekopieerd.
    
    c. Plak in het tekstvak **Eenmalige aanmeldings-URL** de waarde van de **aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.
    
    d. Plak in het tekstvak **Eenmalige afmeldings-URL** de waarde van de **afmeldings-URL** die u uit de Azure Portal hebt gekopieerd.
    
    e. Klik op de knop **Save**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Wizergos Productivity Software.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en vervolgens **Wizergos Productivity Software**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Wizergos Productivity Software** in de lijst met toepassingen.

    ![De koppeling Wizergos Productivity Software in de toepassingenlijst](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-wizergos-productivity-software-test-user"></a>Testgebruiker maken voor Wizergos Productivity Software

In deze sectie gaat u een gebruiker met de naam Britta Simon in Wizergos Productivity Software maken. Werk samen met het [ondersteuningsteam van Wizergos Productivity Software](mailTo:support@wizergos.com) om de gebruikers toe te voegen in het Wizergos Productivity Software-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Wizergos Productivity Software klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Wizergos Productivity Software waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)