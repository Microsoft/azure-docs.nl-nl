---
title: 'Zelfstudie: Azure Active Directory-integratie met PerformanceCentre | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PerformanceCentre.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 79255b98312b16fa21e07abad701e08578c6ca89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515505"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Zelfstudie: Azure Active Directory-integratie met PerformanceCentre

In deze zelfstudie leert u hoe u PerformanceCentre kunt integreren met Azure Active Directory (Azure AD).
De integratie van PerformanceCentre met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot PerformanceCentre.
* U kunt uw gebruikers zich automatisch laten aanmelden bij PerformanceCentre (eenmalige aanmelding) met hun Azure Active Directory-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met PerformanceCentre hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op PerformanceCentre waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PerformanceCentre ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-performancecentre-from-the-gallery"></a>PerformanceCentre toevoegen vanuit de galerie

Voor het configureren van de integratie van PerformanceCentre met Microsoft Azure Active Directory moet u PerformanceCentre vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om PerformanceCentre vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **PerformanceCentre**, selecteer **PerformanceCentre** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![PerformanceCentre in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure Active Directory voor PerformanceCentre configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in PerformanceCentre tot stand is gebracht.

Om eenmalige aanmelding van Azure Active Directory voor PerformanceCentre te testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor PerformanceCentre configureren](#configure-performancecentre-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor PerformanceCentre maken](#create-performancecentre-test-user)** : als u een tegenhanger van Britta Simon in PerformanceCentre wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure Active Directory wilt configureren voor PerformanceCentre:

1. Selecteer in [Azure Portal](https://portal.azure.com/) **Eenmalige aanmelding** op de integratiepagina van de toepassing **PerformanceCentre**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor PerformanceCentre-domein en -URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `http://<companyname>.performancecentre.com/saml/SSO`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van PerformanceCentre](https://www.performio.co/contact-us) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **PerformanceCentre instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-performancecentre-single-sign-on"></a>Eenmalige aanmelding voor PerformanceCentre configureren

1. Meld u als beheerder aan bij uw **PerformanceCentre**-bedrijfssite.

2. Klik op het tabblad aan de linkerkant op **Configureren**.
   
    ![Schermopname van het menu van PerformanceCenter met Configureren geselecteerd.][10]

3. Klik op het tabblad aan de linkerkant op **Diversen** en klik vervolgens op **Eenmalige aanmelding**.
   
    ![Schermopname van het tabblad Configureren met Eenmalige aanmelding geselecteerd in het menu Diversen.][11]

4. Selecteer **SAML-** als **protocol**.
   
    ![Schermopname van de sectie Eenmalige aanmelding configureren met SAML geselecteerd in het menu Protocol.][12]

5. Open uw gedownloade metagegevens in Kladblok, kopieer de inhoud en plak deze vervolgens in het tekstvak **Metagegevens identiteitsprovider** en klik vervolgens op **Opslaan**.
   
    ![Schermopname van het tekstvak Metagegevens identiteitsprovider.][13]

6. Controleer of de waarden voor de items **Basis-URL voor entiteiten** en **URL voor entiteits-id** correct zijn.
    
     ![Eenmalige aanmelding van Azure AD][14]

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot PerformanceCentre.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **PerformanceCentre**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **PerformanceCentre**.

    ![De PerformanceCentre-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-performancecentre-test-user"></a>Een testgebruiker voor PerformanceCentre maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in PerformanceCentre.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in PerformanceCentre:**

1. Meld u als beheerder aan bij uw PerformanceCentre-bedrijfssite.

2. Klik in het menu aan de linkerkant op **Verbanden leggen** en klik vervolgens op **Deelnemer maken**.
   
    ![Schermopname van de PerformanceCenter-bedrijfssite Verbanden leggen - Deelnemers met de knop Deelnemer maken geselecteerd.][400]

3. Voer de volgende stappen uit in het dialoog venster **Verbanden leggen - Deelnemer maken**:
   
    ![Gebruiker maken][401]
    
    a. Typ de vereiste kenmerken voor Britta Simon in gerelateerde tekstvakken.
    
    >[!IMPORTANT]
    >Het gebruikersnaamkenmerk van Britta in PerformanceCentre moet hetzelfde zijn als die van de gebruikersnaam in Azure AD.
    
    b. Selecteer **Clientbeheerder** bij **Rol kiezen**.
    
    c. Klik op **Opslaan**. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PerformanceCentre in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van PerformanceCentre waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png