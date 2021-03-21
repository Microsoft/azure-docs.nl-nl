---
title: 'Zelfstudie: Azure Active Directory-integratie met Mixpanel | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Mixpanel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: dfd262c1dc7aa2e6cfa6ae8835210086dd45e4f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521234"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Zelfstudie: Azure Active Directory-integratie met Mixpanel

In deze zelfstudie leert u hoe u Mixpanel kunt integreren met Azure Active Directory (Azure AD).
De integratie van Mixpanel met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Mixpanel.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Mixpanel (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Mixpanel hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Mixpanel waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mixpanel ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-mixpanel-from-the-gallery"></a>Mixpanel toevoegen vanuit de galerie

Voor het configureren van de integratie van Mixpanel in Azure AD moet u Mixpanel vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Mixpanel wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Mixpanel**, selecteer **Mixpanel** in de resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Mixpanel in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Mixpanel op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Mixpanel tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding wilt configureren en testen met Mixpanel, dient u de volgende procedures te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Mixpanel configureren](#configure-mixpanel-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Mixpanel maken](#create-mixpanel-test-user)** : als u een tegenhanger van Britta Simon in Mixpanel wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure Active Directory met Mixpanel moet u de volgende stappen uitvoeren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) **Eenmalige aanmelding** op de integratiepagina van de toepassing **Mixpanel**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Mixpanel](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://mixpanel.com/login/`

    > [!NOTE]
    > Registreer u op [https://mixpanel.com/register/](https://mixpanel.com/register/) om uw aanmeldingsreferenties in te stellen en neem contact op met het [ondersteuningsteam van Mixpanel](mailto:support@mixpanel.com) om instellingen voor eenmalige aanmelding in te schakelen voor uw tenant. U kunt de waarde voor de aanmeldings-URL indien nodig ook opvragen bij het ondersteuningsteam van Mixpanel. 

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Mixpanel instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-mixpanel-single-sign-on"></a>Eenmalige aanmelding configureren in Mixpanel

1. Meld u in een ander browservenster als beheerder aan bij Mixpanel.

2. Klik onderaan de pagina op het kleine **tandwiel** in de linkerbenedenhoek. 
   
    ![Eenmalige aanmelding in Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Klik op het tabblad **Access security** en klik vervolgens op **Change settings**.
   
    ![Schermopname van het tabblad Access security waar u instellingen kunt wijzigen.](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Klik in het dialoogvenster **Change your certificate** op **Choose file** om uw gedownloade certificaat te uploaden en klik vervolgens op **NEXT**.
   
    ![Schermopname van het dialoogvenster 'Change your certificate' (Uw certificaat wijzigen), waarin u een certificaatbestand kunt kiezen.](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Plak in het tekstvak Authentication URL op de pagina **Change your authentication URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal en klik vervolgens op **NEXT**.
   
    ![Schermopname van het deelvenster 'Change your authentication URL' (Uw verificatie-URL wijzigen), waar u uw aanmeldings-URL kunt kopiëren.](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Klik op **Gereed**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Mixpanel.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Mixpanel**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Mixpanel** in de lijst met toepassingen.

    ![De koppeling naar Mixpanel in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-mixpanel-test-user"></a>Testgebruiker voor Mixpanel maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Mixpanel. 

1. Meld u als beheerder aan bij de bedrijfssite van Mixpanel.

2. Klik onderaan de pagina op de knop met het tandwiel om het venster **Settings** te openen.

3. Klik op het tabblad **Team**.

4. Typ in het tekstvak **team member** het e-mailadres van Britta van Azure.
   
    ![Schermopname van het tabblad Team waar u een adres voor een uitnodiging toevoegt.](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Klik op **Uitnodigen**. 

> [!Note]
> De gebruiker krijgt een e-mail om het profiel in te stellen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Mixpanel klikt, wordt u automatisch aangemeld bij de instantie van Mixpanel waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)