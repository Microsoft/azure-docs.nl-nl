---
title: 'Zelfstudie: Azure Active Directory-integratie met Mitel Connect | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Mitel Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015176"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Zelfstudie: Azure Active Directory-integratie met Mitel MiCloud Connect of CloudLink Platform

In deze zelfstudie leert u hoe u de app Mitel Connect kunt gebruiken voor de integratie van Azure Active Directory (Azure AD) met Mitel MiCloud Connect of CloudLink Platform. De app Mitel Connect is beschikbaar in Azure Gallery. Het integreren van Azure AD met MiCloud Connect of CloudLink Platform biedt de volgende voordelen:

* U kunt de toegang van gebruikers tot MiCloud Connect-apps en CloudLink-apps in Azure AD beheren met behulp van hun bedrijfsreferenties.
* U kunt instellen dat gebruikers in uw account automatisch worden aangemeld bij MiCloud Connect of CloudLink (eenmalige aanmelding) door gebruik te maken van hun Azure AD-accounts.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u geen Azure-abonnement hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/) voordat u begint met de integratie van Azure AD met Mitel MiCloud Connect of CloudLink-platform.

## <a name="prerequisites"></a>Vereisten

Voor de configuratie van Azure AD-integratie met MiCloud Connect, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Mitel MiCloud Connect-account of een Mitel CloudLink-account, afhankelijk van de toepassing die u wilt configureren.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u eenmalige aanmelding van Azure AD configureren en testen.

* Mitel Connect biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding
* Zodra u Mitel Connect hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Mitel Connect toevoegen vanuit de galerie

Voor het configureren van de integratie van Mitel Connect in Azure AD moet u Mitel Connect uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps in Azure Portal.

1. Selecteer **Azure Active Directory** in **[Azure Portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer **Nieuwe toepassing**.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Mitel Connect** in het zoekveld, selecteer **Mitel Connect** in het resultatenvenster en selecteer vervolgens **Toevoegen**.

     ![Mitel Connect in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met MiCloud Connect of CloudLink Platform op basis van een testgebruiker met de naam **_Britta Simon_**. Voor een goede werking van eenmalige aanmelding moet er een koppeling tot stand worden gebracht tussen de gebruiker in de Azure AD-portal en de bijbehorende gebruiker op het Mitel-platform. Raadpleeg de volgende secties voor informatie over het configureren en testen van eenmalige aanmelding van Azure AD met MiCloud Connect of CloudLink Platform.
* Eenmalige aanmelding van Azure AD configureren en testen met MiCloud Connect
* Eenmalige aanmelding van Azure AD configureren en testen met CloudLink Platform

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Eenmalige aanmelding van Azure AD configureren en testen met MiCloud Connect

Eenmalige aanmelding van Azure AD configureren en testen met MiCloud Connect:

1. **[MiCloud Connect configureren voor eenmalige aanmelding met Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** : als u uw gebruikers in staat wilt stellen deze functie te gebruiken en de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
2. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
3. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
4. **[Een Mitel MiCloud Connect-testgebruiker maken](#create-a-mitel-micloud-connect-test-user)** : als u een tegenhanger van Britta Simon wilt hebben voor uw MiCloud Connect-account dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
5. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud Connect voor eenmalige aanmelding met Azure AD configureren

In deze sectie schakelt u eenmalige aanmelding van Azure AD in voor MiCloud Connect in Azure Portal en configureert u uw MiCloud Connect-account om eenmalige aanmelding met Azure AD mogelijk te maken.

Als u MiCloud Connect met eenmalige aanmelding voor Azure AD wilt configureren, is het het gemakkelijkst om Azure Portal en de Mitel Account-portal naast elkaar te openen. U moet bepaalde gegevens van Azure Portal naar de Mitel Account-portal kopiëren en sommige gegevens van de Mitel Account-portal naar Azure Portal.


1. De configuratiepagina openen in [Azure Portal](https://portal.azure.com/):

    1. Op de pagina voor integratie van toepassingen van **Mitel Connect** selecteert u **Single sign-on**.

       ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

    1. In het dialoogvenster **Select a Single sign-on method** selecteert u **SAML**.
    
       ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)
    
       De op SAML gebaseerde aanmeldingspagina wordt weergegeven.

2. Het dialoogvenster voor configuratie openen in de Mitel Account-portal:

    1. In het menu **Phone System** selecteert u **Add-On Features**.

    1. Rechts van **Single Sign-On** selecteert u **Activate** of **Settings**.
    
    Het dialoogvenster voor instellingen voor eenmalige aanmelding van Connect verschijnt.
    
3. Schakel het selectievakje **Enable Single Sign-On** in.
    
    ![Schermafbeelding met de instellingenpagina voor eenmalige aanmelding van Mitel Connect, met het selectievakje om eenmalige aanmelding in te schakelen geselecteerd.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Selecteer in Azure Portal het pictogram **Bewerken** in het gedeelte **Standaard SAML-configuratie**.
   
    ![Schermopname van Eenmalige aanmelding instellen met de SAML-pagina met het pictogram bewerken geselecteerd.](common/edit-urls.png)

    Het dialoogvenster Standaard SAML-configuratie verschijnt.

5.  Kopieer de URL van het veld **Mitel Identifier (Entity ID)** in de Mitel Account-portal en plak deze in het veld **Id (entiteits-id)** in Azure Portal.

6. Kopieer de URL uit het veld **Reply URL (Assertion Consumer Service URL)** in de Mitel Account-portal en plak deze in het veld **Antwoord-URL (URL voor Assertion Consumer Service)** in Azure Portal.

   ![Schermopname van de Basic SAML-configuratie in de Azure-portal en de sectie Id-provider instellen in de Mitel Account-portal met lijnen waarmee de relatie er tussen wordt aangegeven.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. In het tekstvak **Aanmeldings-URL** typt u een van de volgende URL's:

    1. **https://portal.shoretelsky.com** : voor gebruik van de Mitel Account-portal als Mitel-standaardtoepassing
    1. **https://teamwork.shoretel.com** : voor gebruik van Teamwork als Mitel-standaardtoepassing

    > [!NOTE]
    > De Mitel-standaardtoepassing is de toepassing die wordt geopend wanneer een gebruiker de Mitel Connect-tegel in het toegangsvenster selecteert. Dit is ook de toepassing die wordt geopend bij het uitvoeren van een testinstallatie vanuit Azure AD.

8. Selecteer **Opslaan** in het dialoogvenster **Standaard SAML-configuratie** in Azure Portal.

9. Selecteer in het gedeelte **SAML-handtekeningcertificaat**, op de pagina **Op SAML gebaseerde aanmelding** in Azure Portal, de optie **Downloaden** naast **Certificaat (Base64)** om het **handtekeningcertificaat** te downloaden en op uw computer op te slaan.

    ![Schermopname van het deelvenster SAML-handtekeningcertificaat waar u een certificaat kunt downloaden.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Open het bestand Handtekeningcertificaat in een teksteditor, kopieer alle gegevens in het bestand en plak de gegevens in het veld **Signing Certificate** in de Mitel Account-portal. 

      ![Schermopname van het veld Handtekeningcertificaat.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. In het gedeelte **Mitel Connect instellen**, op de pagina **Op SAML gebaseerde aanmelding** van Azure Portal, gaat u als volgt te werk:

     1. Kopieer de URL in het veld **Aanmeldings-URL** en plak deze in het veld **Sign-in URL** in de Mitel Account-portal.

     1. Kopieer de URL in het veld **Azure AD-id** en plak deze in het veld **Entity ID** in de Mitel Account-portal.
         
         ![Schermopname van de relatie tussen de op SAML gebaseerde aanmeldingspagina van de Azure-portal en de Motel Account-portal.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Selecteer **Save** in het dialoogvenster **Connect Single Sign-On Settings** in de Mitel Account-portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In het dialoogvenster voor de gebruikerseigenschappen voert u de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    1. Typ in het veld **Naam** **Britta Simon**.
  
    1. Typ in het veld **Gebruikersnaam** brittasimon@\<yourcompanydomain\>.\<extension\>.  Bijvoorbeeld BrittaSimon@contoso.com.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Mitel Connect.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Mitel Connect**.

    ![De Mitel Connect-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de bijbehorende rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Een Mitel MiCloud Connect-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon voor uw MiCloud Connect-account. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

Zie het artikel [Adding a User](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) (Gebruiker toevoegen) in de Mitel Knowledge Base voor meer informatie over het toevoegen van gebruikers in de Mitel Account-portal.

Maak een gebruiker voor uw MiCloud Connect-account met de volgende gegevens:

* **Naam:** Britta Simon
* **Zakelijk e-mailadres:** `brittasimon@<yourcompanydomain>.<extension>`   
  (Bijvoorbeeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **Gebruikersnaam:** `brittasimon@<yourcompanydomain>.<extension>`  
  (Bijvoorbeeld: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); de gebruikersnaam van de gebruiker is doorgaans dezelfde als het zakelijke e-mailadres van de gebruiker.)

> [!NOTE]
> De gebruikersnaam van de gebruiker voor MiCloud Connect moet identiek zijn aan het e-mailadres van de gebruiker in Azure.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u de configuratie van eenmalige aanmelding van Azure AD testen via het toegangsvenster.

Wanneer u de Mitel Connect-tegel selecteert in het toegangsvenster, wordt u automatisch omgeleid om u aan te melden bij de MiCloud Connect-toepassing, die u in het veld **Sign-on URL** hebt geconfigureerd als uw standaardtoepassing. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Eenmalige aanmelding van Azure AD configureren en testen met CloudLink Platform

In deze sectie wordt beschreven hoe u eenmalige aanmelding van Azure AD kunt inschakelen voor het CloudLink-platform in Azure Portal en hoe u uw CloudLink-platformaccount kunt configureren om eenmalige aanmelding met Azure AD toe te staan.

Als u het CloudLink-platform wilt configureren om eenmalige aanmelding voor Azure AD mogelijk te maken, is het raadzaam om Azure Portal en de CloudLink Accounts-portal naast elkaar te openen. U moet namelijk een aantal gegevens van Azure Portal naar de CloudLink Accounts-portal kopiëren en vice versa.

1. De configuratiepagina openen in [Azure Portal](https://portal.azure.com/):

    1. Op de pagina voor integratie van toepassingen van **Mitel Connect** selecteert u **Single sign-on**.

       ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

    1. In het dialoogvenster **Select a Single sign-on method** selecteert u **SAML**.

       ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)
    
       De pagina **Op SAML gebaseerde aanmelding** wordt geopend, waarin het gedeelte **Standaard SAML-configuratie** wordt weergegeven.

       ![Schermopname van de op SAML gebaseerde aanmeldingspagina met de Basic SAML-configuratie.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Het configuratiepaneel **Azure AD Single Sign On** in de CloudLink Accounts-portal openen:

    1. Ga naar de pagina **Account Information** van het klantaccount waarmee u de integratie wilt inschakelen.

    1. Selecteer in het gedeelte **Integrations** de optie **+ Add new**. Het deelvenster **Integrations** wordt weergegeven in een pop-upvenster.

    1. Selecteer het tabblad **3rd party**. Er wordt een lijst met ondersteunde toepassingen van derden weergegeven. Selecteer de knop **Add** die is gekoppeld aan **Azure AD Single Sign On** en selecteer **Done**.

       ![Schermopname van de pagina Integraties waar u een Eenmalige aanmelding van Azure kunt toevoegen.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       **Azure AD Single Sign On** is ingeschakeld voor het klantaccount en wordt toegevoegd aan het gedeelte **Integrations** van de pagina **Account Information** page.   

   1. Selecteer **Complete Setup**.
    
      ![Schermopname van de optie Volledige installatie voor Eenmalige aanmelding van Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Het configuratiedeelvenster **Azure AD Single Sign On** wordt geopend.
      
       ![Schermopname van de configuratie voor Eenmalige aanmelding van Azure AD.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       U wordt aangeraden het selectievakje bij **Enable Mitel Credentials (Optional)** in het gedeelte **Optional Mitel credentials** in te schakelen. Schakel dit selectievakje alleen in als u wilt dat de gebruiker zich niet alleen bij de CloudLink-toepassing aanmeldt via eenmalige aanmelding, maar ook met de Mitel-referenties.

3. Selecteer in Azure Portal, vanaf de pagina **Op SAML gebaseerde aanmelding**, het pictogram **Bewerken** in het gedeelte **Standaard SAML-configuratie**. Het deelvenster **Standaard SAML-configuratie** wordt geopend.

    ![Schermopname van het deelvenster Basic SAML-configuratie met het pictogram Bewerken geselecteerd.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Kopieer de URL in het veld **Mitel Identifier (Entity ID)** in de CloudLink Accounts-portal en plak deze in het veld **Id (entiteits-id)** in Azure Portal.

 5. Kopieer de URL uit het veld **Reply URL (Assertion Consumer Service URL)** in de CloudLink Accounts-portal en plak deze in het veld **Antwoord-URL (URL voor Assertion Consumer Service)** in Azure Portal.  
    
    ![Schermopname van de relatie tussen pagina's in de CloudLink Accounts-portal en de Azure-portal.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. In het tekstvak **Aanmeldings-URL** typt u de URL `https://accounts.mitel.io` om de CloudLink Accounts-portal als de Mitel-standaardtoepassing te gebruiken.
     
     ![Schermopname van het tekstvak Aanmelden bij URL.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > De Mitel-standaardtoepassing is de toepassing die wordt geopend wanneer een gebruiker de Mitel Connect-tegel in het toegangsvenster selecteert. Dit is ook de toepassing die wordt geopend als de gebruiker een testinstallatie configureert vanuit Azure AD.

7. Selecteer **Opslaan** in het dialoogvenster **Standaard SAML-configuratie**.

8. Selecteer in het gedeelte **SAML-handtekeningcertificaat**, op de pagina **Op SAML gebaseerde aanmelding** in Azure Portal, de optie **Downloaden** naast **Certificaat (Base64)** om het **handtekeningcertificaat** te downloaden. Sla het certificaat op uw computer op.
  
    ![Schermopname van de sectie SAML-handtekeningcertificaat, waar u een Base64-certificaat kunt downloaden.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Open het bestand Handtekeningcertificaat in een teksteditor, kopieer alle gegevens in het bestand en plak de gegevens in het veld **Signing Certificate** in de CloudLink Accounts-portal.  

    > [!NOTE]
    > Als u meer dan één certificaat hebt, wordt u aangeraden ze na elkaar te plakken. 
       
    ![Schermopname van Stap twee van de procedure, waar u waarden invult vanuit uw Azure AD-integratie.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. In het gedeelte **Mitel Connect instellen**, op de pagina **Op SAML gebaseerde aanmelding** van Azure Portal, gaat u als volgt te werk:

     1. Kopieer de URL in het veld **Aanmeldings-URL** en plak deze in het veld **Sign-in URL** in de CloudLink Accounts-portal.

     1. Kopieer de URL in het veld **Azure AD-id** en plak deze in het veld **IDP Identifier (Entity ID)** in de CloudLink Accounts-portal.
     
        ![Schermopname van de bron voor de waarden die hier worden beschreven in Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Selecteer **Save** in het deelvenster **Azure AD Single Sign On** in de CloudLink Accounts-portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In het dialoogvenster voor de gebruikerseigenschappen voert u de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    1. Typ in het veld **Naam** **Britta Simon**.
  
    1. Typ in het veld **Gebruikersnaam** brittasimon@\<yourcompanydomain\>.\<extension\>.  Bijvoorbeeld BrittaSimon@contoso.com.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Mitel Connect.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Mitel Connect**.

    ![De Mitel Connect-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de bijbehorende rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-cloudlink-test-user"></a>Een CloudLink-testgebruiker maken

In deze sectie wordt beschreven hoe u een testgebruiker met de naam **_Britta Simon_** voor uw CloudLink-platform maakt. Er moeten gebruikers worden gemaakt en geactiveerd voordat ze eenmalige aanmelding kunnen gebruiken.

Zie **_Managing Users_** in the [documentatie voor CloudLink Accounts](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html) voor meer informatie over het toevoegen van gebruikers in de CloudLink Accounts-portal.

Maak een gebruiker in uw CloudLink-Accounts-portal met de volgende gegevens:

* Naam: Britta Simon
* Voornaam: Britta
* Achternaam: Simon
* E-mail: BrittaSimon@contoso.com

> [!NOTE]
> Het e-mailadres van de gebruiker voor CloudLink moet identiek zijn aan de **user principal name** in Azure Portal.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u in het toegangsvenster de configuratie voor eenmalige aanmelding van Azure AD.

Wanneer u de Mitel Connect-tegel selecteert in het toegangsvenster, wordt u automatisch omgeleid om u aan te melden bij de CloudLink-toepassing, die u in het veld **Sign-on URL** hebt geconfigureerd als uw standaardtoepassing. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)