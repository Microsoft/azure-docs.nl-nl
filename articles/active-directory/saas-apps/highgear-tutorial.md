---
title: 'Zelfstudie: Azure Active Directory-integratie met HighGear | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en HighGear.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: b3806d768e8335801e2dd097eac7a440b157de38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92444649"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Zelfstudie: Azure Active Directory-integratie met HighGear

In deze zelfstudie leert u hoe u HighGear kunt integreren met Azure Active Directory (Azure AD).
De integratie van HighGear met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot HighGear.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij HighGear (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om de integratie van Azure AD-integratie met HighGear te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand krijgen
* Een HighGear-systeem met een Enterprise- of Unlimited-licentie

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie kunt u leren hoe u in een testomgeving eenmalige aanmelding van Azure AD configureert en test.

* HighGear ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-highgear-from-the-gallery"></a>HighGear toevoegen vanuit de galerie

Om de integratie van HighGear in Azure AD te configureren, moet u HighGear vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om HighGear toe te voegen vanuit de galerie:**

1. In de **[Azure-portal](https://portal.azure.com)** klikt u in het navigatievenster aan de linkerkant op het pictogram **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **HighGear** in het zoekvak, selecteer **HighGear** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![HighGear in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u leren hoe u Azure AD-eenmalige aanmelding met uw HighGear-systeem configureert en test met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in het HighGear-systeem tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met uw HighGear-systeem wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor HighGear configureren](#configure-highgear-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de zijde van de HighGear-toepassing configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor HighGear maken](#create-highgear-test-user)**: als u een tegenhanger van Britta Simon in HighGear wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker. 
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie kunt u leren hoe u Azure AD-eenmalige aanmelding in de Azure-portal inschakelt.

Voor de configuratie van eenmalige aanmelding in Azure AD met uw HighGear-systeem moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **HighGear**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. Plak in het tekstvak **Identifier** de waarde van het veld **Service Provider Entity ID** dat staat vermeld op de pagina met instellingen voor eenmalige aanmelding in uw HighGear-systeem.

    ![Het veld Service Provider Entity ID](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > U moet zich aanmelden bij uw HighGear-systeem om toegang te krijgen tot de pagina met instellingen voor eenmalige aanmelding. Wanneer u bent aangemeld, beweegt u de muis over het tabblad Administration in HighGear en klikt u op het menu-item Single Sign-On Settings.
    
    ![Het menu-item Single Sign-On Settings](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Plak in het tekstvak **Reply URL** de waarde van **Assertion Consumer Service (ACS) URL** van de pagina met instellingen voor eenmalige aanmelding in uw HighGear-systeem.

    ![Het veld Assertion Consumer Service (ACS) URL](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

     ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

     Plak in het tekstvak **Sign-on URL** de waarde van het veld **Service Provider Entity ID** dat staat vermeld op de pagina met instellingen voor eenmalige aanmelding in uw HighGear-systeem. (Deze entiteits-id is ook de basis-URL van het HighGear-systeem die moet worden gebruikt voor door SP geïnitieerde eenmalige aanmelding.)

    ![Het veld Service Provider Entity ID](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke Identifier, Reply URL and Sign-on URL van de pagina **Single Sign-On Settings** in uw HighGear-systeem. Als u hulp nodig hebt, kunt u contact opnemen met het [HighGear-ondersteuningsteam](mailto:support@highgear.com).

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **certificaat (Base64)** te downloaden, en slaat u dit op uw computer op. U hebt dit in een latere stap van de configuratie voor eenmalige aanmelding nodig.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Noteer in de sectie **HighGear instellen** de locatie van de volgende URL's.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL. U hebt deze waarde nodig in stap 2 van **Eenmalige aanmelding bij HighGear configureren** hieronder.

    b. Azure AD-id. U hebt deze waarde nodig in stap 3 van **Eenmalige aanmelding bij HighGear configureren** hieronder.

    c. Afmeldings-URL. U hebt deze waarde nodig in stap 4 van **Eenmalige aanmelding bij HighGear configureren** hieronder.

### <a name="configure-highgear-single-sign-on"></a>Eenmalige aanmelding bij HighGear configureren

Als u HighGear wilt configureren voor eenmalige aanmelding, meldt u zich aan bij uw HighGear-systeem. Wanneer u bent aangemeld, beweegt u de muis over het tabblad Administration in HighGear en klikt u op het menu-item Single Sign-On Settings.

![Het menu-item Single Sign-On Settings](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. In **Identity Provider Name** typt u een korte beschrijving. Deze wordt weergegeven in de knop voor eenmalige aanmelding van HighGear op de aanmeldingspagina. Bijvoorbeeld: Azure AD

2. Plak in het veld **Single Sign-On (SSO) URL** in HighGear de waarde van het veld **Aanmeldings-URL** uit de sectie **HighGear instellen** in Azure.

3. Plak in het veld **Identity Provider Entity ID** in HighGear de waarde van het veld **Azure AD-id** uit de sectie **HighGear instellen** in Azure.

4. Plak in het veld **Single Logout (SLO) URL** in HighGear de waarde van het veld **Afmeldings-URL** uit de sectie **HighGear instellen** in Azure.

5. Gebruik Kladblok om het certificaat te openen dat u hebt gedownload uit de sectie **SAML-handtekeningcertificaat** in Azure. U moet de indeling **Certificaat (Base64)** hebben gedownload. Kopieer de inhoud van het certificaat in Kladblok en plak deze in het veld **Identity Provider Certificate** in HighGear.

6. Stuur een e-mail naar het [HighGear ondersteuningsteam](mailto:support@highgear.com) om uw HighGear-certificaat aan te vragen. Volg de instructies die u van hen ontvangt om de velden **HighGear Certificate** en **HighGear Certificate Password** in te vullen.

7. Klik op de knop **Save** om uw configuratie voor eenmalige aanmelding bij HighGear op te slaan.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot HighGear.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **HighGear**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **HighGear** in de lijst met toepassingen.

    ![De koppeling HighGear in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-highgear-test-user"></a>HighGear-testgebruiker maken

Meld u aan bij uw HighGear-systeem als u een testgebruiker voor HighGear wilt maken om uw configuratie van eenmalige aanmelding te testen.

1. Klik op de knop **Create New Contact**.

    ![De knop Create New Contact](media/highgear-tutorial/create-new-contact-button.png)

    In het volgende menu kunt u kiezen welk type contactpersoon u wilt maken.

2. Klik op het menu-item **Individual** om een HighGear-gebruiker te maken.

    Aan de rechterkant schuift een deelvenster uit waarin u de gegevens voor de nieuwe gebruiker kunt typen.  
    ![Het formulier New Contact](media/highgear-tutorial/new-contact-form.png)

3. Typ in het veld **Name** een naam voor de contactpersoon. Bijvoorbeeld: Britta Simon

4. Klik op het menu **More Options** en selecteer het menu-item **Account Info**.

    ![Op het menu-item Account Info klikken](media/highgear-tutorial/account-info-menu-item.png)

5. Stel het veld **Can Log In** in op Yes.

    Het veld **Enable Single Sign-On** wordt ook automatisch ingesteld op Yes.

6. Typ in het veld **Single Sign-On User Id** de id van de gebruiker. Bijvoorbeeld: BrittaSimon@contoso.com

    De sectie Account Info ziet er nu ongeveer als volgt uit:  
    ![De voltooide sectie Account Info](media/highgear-tutorial/finished-account-info-section.png)

7. Als u de contactpersoon wilt opslaan, klikt u op de knop **Save** onderaan het deelvenster.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel HighGear klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van HighGear in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)