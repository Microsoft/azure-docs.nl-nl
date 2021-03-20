---
title: 'Zelfstudie: Azure Active Directory-integratie met Rightscale | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Rightscale.
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
ms.openlocfilehash: 013eadedc00dee23a09eff89147406cc14f017ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516576"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Zelfstudie: Azure Active Directory-integratie met Rightscale

In deze zelfstudie leert u hoe u Rightscale kunt integreren met Azure Active Directory (Azure AD).
De integratie van Rightscale met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Rightscale.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Rightscale (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om de integratie van Azure AD met Rightscale te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Rightscale waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rightscale biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-rightscale-from-the-gallery"></a>Rightscale toevoegen vanuit de galerie

Voor het configureren van de integratie van Rightscale in Azure AD moet u Rightscale vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Rightscale vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Rightscale**, selecteer **Rightscale** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![Rightscale in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Rightscale op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Rightscale tot stand is gebracht.

U hebt de volgende bouwstenen nodig om eenmalige aanmelding van Azure AD met Rightscale te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Rightscale configureren](#configure-rightscale-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Rightscale maken](#create-rightscale-test-user)** : als u een tegenhanger van Britta Simon in Softeon WMS wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding in Azure AD te configureren voor Rightscale:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Rightscale** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Schermopname van de pagina SAML-basisconfiguratie.](common/preintegrated.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://login.rightscale.com/`

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Rightscale instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rightscale-single-sign-on"></a>Eenmalige aanmelding voor Rightscale configureren

1. Als u wilt dat eenmalige aanmelding voor uw toepassing wordt geconfigureerd, moet u zich bij uw Rightscale-tenant aanmelden als beheerder.

2. Klik in het menu aan de bovenkant op het tabblad **Settings** (Instellingen) en selecteer **Single Sign-On** (Eenmalige aanmelding).

    ![Schermopname van Eenmalige aanmelding geselecteerd vanuit Instellingen.](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Klik op de knop **new** (nieuw) om **uw SAML-id-providers** toe te voegen.

    ![Schermopname van de nieuwe knoppen die zijn geselecteerd om een SAML-ID-provider toe te voegen.](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Voer in het tekstvak **Display Name** (Weergavenaam) de naam van uw bedrijf in.

    ![Schermopname die laat zien waar u een weergavenaam kunt invoeren.](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Selecteer **Allow RightScale-initiated SSO using a discovery hint** (Door Rightscale geïnitieerde eenmalige aanmelding toestaan met een detectiehint) en voer uw **domeinnaam** in het onderstaande tekstvak in.

    ![Schermopname die laat zien waar u een aanmeldingsmethode kunt opgeven.](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Plak de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd, in **SAML SSO Endpoint** in RightScale.

    ![Schermopname die laat zien waar u een SAML S S O-eindpunt kunt invoeren.](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Plak de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd, in **SAML EntityID** in RightScale.

    ![Schermopname die laat zien waar u een SAML-entiteits-id kunt invoeren.](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Klik op **Browser** om het certificaat te uploaden dat u uit de Azure-portal hebt gedownload.

    ![Schermopname die laat zien waar u uw SAML-handtekeningcertificaat kunt opgeven.](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot Rightscale.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Rightscale**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rightscale** in de lijst met toepassingen.

    ![De koppeling naar Rightscale in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rightscale-test-user"></a>Testgebruiker voor Rightscale maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Rightscale. Neem contact op met het [ondersteuningsteam van Rightscale](mailto:support@rightscale.com) om de gebruikers toe te voegen in het Rightscale-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Rightscale in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Rightscale waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)