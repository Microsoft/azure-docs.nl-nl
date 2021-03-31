---
title: 'Zelfstudie: Azure Active Directory-integratie met Flatter Files | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Flatter Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453213"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Zelfstudie: Azure Active Directory-integratie met Flatter Files

In deze zelfstudie leert u hoe u Flatter Files kunt integreren met Azure Active Directory (Azure AD).
De integratie van Flatter Files met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot Flatter Files heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Flatter Files (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Flatter Files hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Flatter Files waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Flatter Files ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-flatter-files-from-the-gallery"></a>Flatter Files toevoegen vanuit de galerie

Voor het configureren van de integratie van Flatter Files met Azure AD moet u Flatter Files uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Flatter Files vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Flatter Files** in het zoekvak, selecteer **Flatter Files** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![Flatter Files in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Flatter Files configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Flatter Files tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij Flatter Files moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Flatter Files configureren](#configure-flatter-files-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Flatter Files-testgebruiker maken](#create-flatter-files-test-user)**: als u een equivalent van Britta Simon in Flatter Files wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Flatter Files moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Flatter Files** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over eenmalige aanmelding bij het Flatter Files-domein en Flatter Files-URL's](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Flatter Files instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-flatter-files-single-sign-on"></a>Eenmalige aanmelding voor Flatter Files configureren

1. Meld u bij uw Flatter Files-toepassing aan als beheerder.

2. Klik op **DASHBOARD**. 
   
    ![Schermopname met "DASHBOARD" geselecteerd in de app "Flatter Files".](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klik op **Instellingen** en voer de volgende stappen uit op het tabblad **Bedrijf**: 
   
    ![Schermopname met het tabblad "Company" met "Use S A M L 2.0 for Authentication" geselecteerd en de knop "Configure S A M L" geselecteerd.](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecteer **SAML 2.0 gebruiken voor verificatie**.
    
    b. Klik op **SAML configureren**.

4. Voer in het dialoogvenster **SAML-configuratie** de volgende stappen uit: 
   
    ![Eenmalige aanmelding configureren](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Typ uw geregistreerde domein in het tekstvak **Domein**.
   
   > [!NOTE]
   > Als u nog geen domein hebt geregistreerd, neemt u contact op met het ondersteuningsteam van Flatter Files via [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Plak in het tekstvak voor de **URL van de identiteitsprovider** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
    c.  Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    d. Klik op **Update**.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Flatter Files.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Flatter Files**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Flatter Files** in de lijst met toepassingen.

    ![De Flatter Files-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-flatter-files-test-user"></a>Flatter Files-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Flatter Files.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in Flatter Files:**

1. Meld u als beheerder aan bij de bedrijfssite van **Flatter Files**.

2. Klik in het navigatievenster links op **Settings** en vervolgens op het tabblad **Users**.
   
    ![Schermopname met de pagina "Settings" met het tabblad "Users" geselecteerd.](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klik op **Add User**. 

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:
   
    ![Een Flatter Files-gebruiker maken](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Typ **Britta** in het tekstvak **First Name**.
   
    b. Typ **Simon** in het tekstvak **Last Name**. 
   
    c. Typ in het tekstvak **Email address** het e-mailadres van het account van Britta Simon in de Azure-portal.
   
    d. Klik op **Submit**   


### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Flatter Files klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Flatter Files waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)