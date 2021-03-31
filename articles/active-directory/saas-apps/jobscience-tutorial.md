---
title: 'Zelfstudie: Azure Active Directory-integratie met Jobscience | Microsoft Docs'
description: Informatie over eenmalige aanmelding configureren tussen Azure Active Directory en Jobscience.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5a104dcd6ccf500c115359a1b72c67b85359a802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002184"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Zelfstudie: Azure Active Directory-integratie met Jobscience

In deze zelfstudie leert u hoe u Jobscience kunt integreren met Azure Active Directory (Azure AD).

De integratie van Jobscience met Azure AD biedt de volgende voordelen:

- U kunt in Azure AD bepalen wie er toegang heeft tot Jobscience
- U kunt uw gebruikers zich automatisch laten aanmelden bij Jobscience (eenmalige aanmelding) met hun Azure AD-account
- U kunt uw accounts vanaf één centrale locatie beheren: de Azure Portal

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Jobscience te configureren, hebt u het volgende nodig:

- Een Azure AD-abonnement
- Een Jobscience-abonnement waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u hier een gratis proefversie van één maand krijgen: [Proefversie](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie gaat u eenmalige aanmelding van Azure AD in een testomgeving testen. Het scenario dat in deze zelfstudie wordt beschreven, bestaat uit twee belangrijke bouwstenen:

1. Jobscience toevoegen vanuit de galerie
1. Eenmalige aanmelding van Azure AD configureren en testen

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience toevoegen vanuit de galerie
Voor het configureren van de integratie van Jobscience met Azure AD moet u Jobscience vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Jobscience vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram. 

    ![Active Directory][1]

1. Ga naar **Bedrijfstoepassingen**. Ga vervolgens naar **Alle toepassingen**.

    ![Schermopname toont de Azure Portal-bedrijfstoepassingen onder 'Beheren', waarbij 'Alle toepassingen' is geselecteerd.][2]
    
1. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Schermopname waarin 'Nieuwe toepassing' is geselecteerd.][3]

1. Typ **Jobscience** in het zoekvak.

    ![Schermopname toont 'Toevoegen' uit de galerie waarin 'Jobscience' is ingevoerd.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Selecteer in het resultatenvenster **Jobscience** en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Schermopname toont de resultaten die Jobscience bevatten.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Eenmalige aanmelding van Azure AD configureren en testen
In dit gedeelte gaat u eenmalige aanmelding bij Jobscience via Azure AD configureren en testen op basis van een testgebruiker met de naam Britta Simon.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wie de tegenhangende gebruiker in Jobscience is voor een gebruiker in Azure AD. Met andere woorden, er moet een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Jobscience tot stand worden gebracht.

Wijs in Jobscience de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **Gebruikersnaam** om de relatie vast te leggen.

Als u eenmalige aanmelding bij Jobscience via Azure AD wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configuring-azure-ad-single-sign-on)** - zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Een Azure AD-testgebruiker maken](#creating-an-azure-ad-test-user)** - voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
1. **[Testgebruiker voor een Jobscience-testgebruiker maken](#creating-a-jobscience-test-user)** - om een tegenhanger van Britta Simon in Jobscience te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[De Azure AD-testgebruiker toewijzen](#assigning-the-azure-ad-test-user)** - zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Eenmalige aanmelding testen](#testing-single-sign-on)** - om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding van Azure AD in de Azure Portal in en configureert u eenmalige aanmelding in uw Jobscience-toepassing.

**Ga als volgt te werk om eenmalige aanmelding voor Azure AD met Jobscience te configureren:**

1. In de Azure Portal selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Jobscience**.

    ![Schermopname met 'Eenmalige aanmelding' geselecteerd onder 'Beheren' in de Azure Portal.][4]

1. Selecteer, in het dialoogvenster **Eenmalige aanmelding**, **Op SAML gebaseerde aanmelding** als **Modus** om eenmalige aanmelding in te schakelen.
 
    ![Schermopname waarin op SAML gebaseerde aanmeldingsmodus is geselecteerd.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Voer de volgende stappen uit in **Jobscience-domein en -URL's**:

    ![Schermopname toont de aanmeldings-URL.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. U kunt de waarden verkrijgen door contact op te nemen met [het klantondersteuningsteam van Jobscience](http://www.jobscience.com/support) of van het SSO-profiel dat u kunt maken. Dit wordt verderop in de zelfstudie uitgelegd. 
 
1. Onder **SAML-handtekeningcertificaat** klikt u op **Certificaat (Base64)** . Sla het certificaatbestand vervolgens op uw computer op.

    ![Schermopname van het deelvenster SAML-handtekeningcertificaat waar u een certificaat kunt downloaden.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klik op de knop **Save**.

    ![Schermopname toont de knop 'Opslaan'.](./media/jobscience-tutorial/tutorial_general_400.png)

1. Klik in het gedeelte **Configuratie van Jobscience** op **Jobscience configureren** om het venster **Eenmalige aanmelding configureren** te openen. Kopieer de **Afmeldings-URL, SAML-entiteit-id en SAML-service-URL voor eenmalige aanmelding** in de sectie **Snelzoekgids**.

    ![Schermopname toont het configuratievenster Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Meld u aan bij uw Jobscience-bedrijfssite als beheerder.

1. Ga naar **Installatie**.
   
   ![Schermopname toont het instellingsitem voor uw bedrijf.](./media/jobscience-tutorial/IC784358.png "Instellen")

1. Klik op **Domeinbeheer** in de sectie **Beheren** in het navigatievenster aan de linkerzijde om de verwante sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
   
   ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Om te controleren dat uw domein correct is ingesteld, moet u ervoor zorgen dat het in '**Stap 4 Geïmplementeerd naar Gebruikers**' is en uw '**Mijn domeininstellingen**' beoordelen.

    ![Domein geïmplementeerd voor gebruiker](./media/jobscience-tutorial/ic784377.png "Domein geïmplementeerd voor gebruiker")

1. Klik, op de Jobscience-bedrijfssite, op **Beveiligingsmaatregelen** en klik vervolgens op **Instellingen voor eenmalige aanmelding**.
    
    ![Schermopname met 'Instellingen voor eenmalige aanmelding' geselecteerd in 'Beveiligingsmaatregelen'.](./media/jobscience-tutorial/ic784364.png "Beveiligingsmaatregelen")

1. Voer de volgende stappen uit in het gedeelte **Instellingen voor eenmalige aanmelding**:
    
    ![Instellingen voor eenmalige aanmelding](./media/jobscience-tutorial/ic781026.png "Instellingen voor eenmalige aanmelding")
    
    a. Selecteer **SAML Enabled**.

    b. Klik op **Nieuw**.

1. Voer in het dialoogvenster **Instellingen aanpassen voor SAML eenmalige aanmelding** de volgende stappen uit:
    
    ![Instellingen voor op SAML gebaseerde eenmalige aanmelding](./media/jobscience-tutorial/ic784365.png "Instellingen voor op SAML gebaseerde eenmalige aanmelding")
    
    a. Typ in het tekstvak **Name** een naam voor de configuratie.

    b. Plak in het tekstvak **Certificaatverlener** de waarde van de **SAML-entiteit-id** die u hebt gekopieerd vanuit de Azure Portal.

    c. Typ `https://salesforce-jobscience.com` in het tekstvak **Entiteits-id**

    d. Klik op **Bladeren** om uw Azure AD-certificaat te uploaden.

    e. Selecteer **Assertie bevat de federatie-id van het gebruikersobject** bij **SAML-identiteitstype**.

    f. Bij **SAML-identiteitslocatie** selecteert u **Identiteit bevindt zich in het NameIdentfier-element van de Onderwerpinstructie**.

    g. Plak in het tekstvak **URL voor het inloggen van de id-provider** de waarde van **SAML-aanmeldings-URL voor eenmalige aanmelding** die u hebt gekopieerd uit de Azure Portal.

    h. Plak in het tekstvak **Afmeldings-URL van ID-provider** de waarde van de **Afmeldings-URL** in die u hebt gekopieerd uit het Azure Portal.

    i. Klik op **Opslaan**.

1. Klik op **Domeinbeheer** in de sectie **Beheren** in het navigatievenster aan de linkerzijde om de verwante sectie uit te vouwen en klik vervolgens op **Mijn domein** om de pagina **Mijn domein** te openen. 
    
    ![Mijn domein](./media/jobscience-tutorial/ic767825.png "Mijn domein")

1. Klik in de sectie **Huisstijl van aanmeldingspagina** op de pagina **Mijn domein** op **Bewerken**.
    
    ![Schermopname van de sectie 'Huisstijl van de aanmeldingspagina' met de knop 'Aanpassen'.](./media/jobscience-tutorial/ic767826.png "Huisstijl van aanmeldingspagina")

1. In de sectie **Verificatieservice** op de pagina **Huisstijl van aanmeldingspagina** wordt de naam van uw **SAML SSO-instellingen** weergegeven. Selecteer dit en klik vervolgens op **Opslaan**.
    
    ![Schermopname van de sectie 'Huisstijl van de aanmeldingspagina' met 'PPE' en 'Opslaan' geselecteerd.](./media/jobscience-tutorial/ic784366.png "Huisstijl van aanmeldingspagina")

1. Om de aanmeldings-URL voor eenmalige aanmelding, die door SP geïnitieerd is, te verkrijgen, klikt u op de **Instellingen voor eenmalige aanmelding** in het menu **Beveiligingsmaatregelen**.

    ![Schermopname waarin 'Beveiligingsmaatregelen voor beheerders met eenmalige aanmelding' is geselecteerd.](./media/jobscience-tutorial/ic784368.png "Beveiligingsmaatregelen")
    
    Klik op het SSO-profiel dat u hebt gemaakt in de bovenstaande stap. Op deze pagina wordt de URL voor eenmalige aanmelding voor uw bedrijf weergegeven (bijvoorbeeld `https://companyname.my.salesforce.com?so=companyid`.    

> [!TIP]
> U kunt nu een beknopte versie van deze instructies in [Azure Portal](https://portal.azure.com) lezen terwijl u de app instelt!  Klik nadat u deze app onder **Active Directory > Bedrijfstoepassingen** hebt toegevoegd op het tabblad **Eenmalige aanmelding** en open de ingesloten documentatie via het gedeelte **Configuratie** onderaan. Hier leest u meer over de functie voor ingesloten documentatie: [Ingesloten documentatie in Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

![Een Azure AD-gebruiker maken][100]

**Voer de volgende stappen uit om een testgebruiker in Azure AD te maken:**

1. Klik in het navigatiedeelvenster aan de linkerzijde in de **Azure Portal** op het **Azure Active Directory**-pictogram.

    ![Schermopname met het Azure AD-pictogram in de Azure Portal.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Om de lijst met gebruikers weer te geven, gaat u naar **Gebruikers en groepen** en klikt u op **Alle gebruikers**.
    
    ![Schermopname waarin 'Gebruikers en groepen' is geselecteerd in het menu 'Beheren', met 'Alle gebruikers' geselecteerd.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Om het dialoogvenster **Gebruiker** te openen, klikt u op **Toevoegen** aan de bovenkant van het dialoogvenster.
 
    ![Schermopname toont de knop 'Toevoegen' om het dialoogvenster 'Gebruiker' te openen.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Voer de volgende stappen uit in het dialoogvenster **Gebruiker**:
 
    ![Schermopname van het dialoogvenster 'Gebruiker', waar u de in deze stap beschreven waarden kunt invoeren.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Typ, in het veld **Naam**, **Britta Simon**.

    b. In het tekstvak **Gebruikersnaam** voert u het **e-mailadres** van Britta Simon in.

    c. Selecteer **Wachtwoord weergeven** en noteer de weergegeven waarde van het **Wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-jobscience-test-user"></a>Een Jobscience-testgebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Jobscience, moeten ze in Jobscience worden ingericht. In het geval van Jobscience is inrichten een handmatige taak.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het creëren van Jobscience-gebruikersaccounts of API's van Jobscience gebruiken om Azure Active Directory-gebruikersaccounts in te richten.
>  
        
**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw **Jobscience**-bedrijfssite als beheerder.

1. Ga naar Installatie.
   
   ![Schermopname toont het instellingsitem.](./media/jobscience-tutorial/ic784358.png "Instellen")
1. Ga naar **Gebruikers beheren \> Gebruikers**.
   
   ![Gebruikers](./media/jobscience-tutorial/ic784369.png "Gebruikers")
1. Klik op **New User**.
   
   ![Alle gebruikers](./media/jobscience-tutorial/ic784370.png "Alle gebruikers")
1. Voer de volgende stappen uit in het dialoogvenster **Gebruiker bewerken**:
   
   ![Gebruiker bewerken](./media/jobscience-tutorial/ic784371.png "Gebruiker bewerken")
   
   a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, bijvoorbeeld Britta.
   
   b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, bijvoorbeeld Simon.
   
   c. Typ in het tekstvak **Alias** een aliasnaam van de gebruiker, bijvoorbeeld brittas.

   d. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

   e. Typ een gebruikersnaam van een gebruiker in het tekstvak **Gebruikersnaam**, bijvoorbeeld Brittasimon@contoso.com.

   f. Typ in het tekstvak **Bijnaam** een bijnaam van de gebruiker, bijvoorbeeld Simon.

   g. Klik op **Opslaan**.

    
> [!NOTE]
> De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="assigning-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding via Azure door haar toegang te geven tot Jobscience.

![Schermopname toont de weergavenaam van een account.][200] 

**Voer de volgende stappen uit om Britta Simon toe te wijzen aan Jobscience:**

1. Open in de Azure Portal de toepassingenweergave. Navigeer vervolgens naar de mapweergave en ga naar **Bedrijfstoepassingen**. Klik tot slot op **Alle toepassingen**.

    ![Schermopname toont de bedrijfstoepassingen in het menu van de Azure Portal, waarbij 'Alle toepassingen' is geselecteerd.][201] 

1. Selecteer **Jobscience** in de lijst met toepassingen.

    ![Schermopname waarin Jobscience is geselecteerd.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Klik, in het menu aan de linkerkant, op **Gebruikers en groepen**.

    ![Schermopname waarin 'Gebruikers en groepen' is geselecteerd in het Azure Portalmenu.][202] 

1. Klik op de knop **Add**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname toont de knop 'Toevoegen', die wordt gebruikt om toewijzingen toe te voegen.][203]

1. Selecteer, in het dialoogvenster **Gebruikers en groepen**, **Britta Simon** in de lijst 'Gebruikers'.

1. Klik op de knop **Selecteren** in het dialoogvenster **Gebruikers en groepen**.

1. Klik, in het dialoogvenster **Toewijzing toevoegen**, op de knop **Toewijzen**.
    
### <a name="testing-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel 'Jobscience' klikt, wordt u automatisch aangemeld bij uw Jobscience-toepassing.
Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png