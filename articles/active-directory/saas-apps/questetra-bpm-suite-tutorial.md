---
title: 'Zelfstudie: Azure Active Directory-integratie met Questetra BPM Suite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Questetra BPM Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 13f05005529d3983e042398f5274fd7f981b8c8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516586"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Zelfstudie: Azure Active Directory-integratie met Questetra BPM Suite

In deze zelfstudie leert u hoe u Questetra BPM Suite integreert met Azure Active Directory (Azure AD).
De integratie van Questetra BPM Suite met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Questetra BPM Suite.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Questetra BPM Suite (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Questetra BPM Suite, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement waarvoor eenmalige aanmelding van Questetra BPM Suite is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Questetra BPM Suite biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM Suite uit de galerie toevoegen

Als u de integratie van Questetra BPM Suite in Azure AD wilt configureren, moet u Questetra BPM Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Questetra BPM Suite toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Questetra BPM Suite** in het zoekvak, selecteer **Questetra BPM Suite** in het paneel met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Questetra BPM Suite in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Questetra BPM Suite op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Questetra BPM Suite.

Als u eenmalige aanmelding van Azure AD bij Questetra BPM Suite wilt configureren en testen, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Questetra BPM Suite configureren](#configure-questetra-bpm-suite-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Questetra BPM Suite maken](#create-questetra-bpm-suite-test-user)** : als u in Questetra BPM Suite een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u eenmalige aanmelding van Azure AD met Questetra BPM Suite wilt configureren, voert u de volgende stappen uit:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de **Questetra BPM Suite**-toepassing, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij Questetra BPM Suite-domeinen en -URL's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en id. U kunt deze waarden ophalen in de sectie **SP-informatie** op uw **Questetra BPM Suite**-bedrijfssite. Dit wordt later uitgelegd in de zelfstudie, of neem contact op met het [klantondersteuningsteam van Questetra BPM Suite](https://www.questetra.com/contact/). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Questetra BPM Suite instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Eenmalige aanmelding van Questetra BPM Suite configureren

1. Meld u in een ander browservenster als beheerder aan bij uw **Questetra BPM Suite**-bedrijfssite.

2. Klik in het menu bovenaan op **Systeeminstellingen**. 
   
    ![Schermopname van Systeeminstellingen geselecteerd in uw Questetra BPM Suite-bedrijfssite.][10]

3. Klik op **SSO (SAML)** om de pagina **SingleSignOnSAML** te openen. 
   
    ![Schermopname met S S O (SAML) geselecteerd.][11]

4. Voer op uw **Questetra BPM Suite**-bedrijfssite in de sectie **SP-informatie** de volgende stappen uit:

    a. Kopieer de **ACS-URL** en plak deze in het vak **Aanmeldings-URL** in de sectie **Basis-SAML-configuratie** in Azure Portal.
    
    b. Kopieer de **Entiteits-id** en plak deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** in Azure Portal.

5. Voer op uw **Questetra BPM Suite**-bedrijfssite de volgende stappen uit: 
   
    ![Eenmalige aanmelding configureren][15]
   
    a. Schakel het selectievakje **Eenmalige aanmelding inschakelen** in.
   
    b. Plak in het tekstvak **Entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd uit Azure Portal.
    
    c. Plak in het tekstvak **Aanmeldingspagina-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.
    
    d. Plak in het tekstvak **Afmeldingspagina-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.
    
    e. Typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` in het tekstvak **NameID-indeling**.

    f. Open in Kladblok het met **Base 64** gecodeerde certificaat dat u hebt gedownload uit Azure Portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Validatiecertificaat**. 

    g. Klik op **Opslaan**.

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

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding van Azure door haar toegang te geven tot Questetra BPM Suite.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Questetra BPM Suite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Questetra BPM Suite** in de lijst met toepassingen.

    ![De koppeling Questetra BPM Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-questetra-bpm-suite-test-user"></a>Een Questetra BPM Suite-testgebruiker maken

Het doel van deze sectie is om een gebruiker met de naam Britta Simon te maken in Questetra BPM Suite.

**Voer de volgende stappen uit om in Questetra BPM Suite een gebruiker met de naam Britta Simon te maken:**

1. Meld u als beheerder aan bij uw Questetra BPM Suite-bedrijfssite.

2. Ga naar **Systeeminstellingen > Gebruikerslijst > Nieuwe gebruiker**.
 
3. Voer de volgende stappen uit in het dialoogvenster Nieuwe gebruiker: 
   
    ![Een testgebruiker maken][300] 
   
    a. Typ de **naam** van de gebruiker in het tekstvak **Naam** britta.simon@contoso.com.
   
    b. Typ in het tekstvak **E-mail** het **e-mailadres** van de gebruiker britta.simon@contoso.com.
   
    c. Typ in het tekstvak **Wachtwoord** een **wachtwoord** van de gebruiker.
    
    d. Klik op **Nieuwe gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Questetra BPM Suite in het toegangsvenster klikt, wordt u automatisch aangemeld bij de Questetra BPM Suite-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png