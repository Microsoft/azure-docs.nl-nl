---
title: 'Zelfstudie: Azure Active Directory-integratie met SAML SSO for Jira by resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Jira by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e1440feacd10935b15195110cd7ea3e20b4d523c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687614"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Zelfstudie: Azure Active Directory-integratie met SAML SSO for Jira by resolution GmbH

Deze zelfstudie laat zien hoe u SAML SSO for Jira by resolution GmbH instelt met Azure Active Directory (Azure AD).
Integratie van SAML SSO for Jira by resolution GmbH met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie zich kan aanmelden bij Jira met de SAML SSO-invoegtoepassing met de resolutie GmbH.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-accounts bij Jira worden aangemeld met gebruik van SAML SSO for Jira by resolution GmbH (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie en SAML SSO for Jira by resolution GmbH te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand krijgen
* Een abonnement op SAML SSO for Jira by resolution GmbH waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO for Jira by resolution GmbH ondersteunt **SP** en **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Een bedrijfstoepassing voor eenmalige aanmelding toevoegen

Om eenmalige aanmelding in te stellen in Azure AD, moet u een nieuwe ondernemingstoepassing toevoegen. In de galerie is er een vooraf geconfigureerde toepassing vooraf ingesteld hiervoor, **SAML SSO for Jira by Solution GmbH**.

**Voer de volgende stappen uit om SAML SSO for Jira by resolution GmbH toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Ondernemingstoepassingen** en klik vervolgens op **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAML SSO for Jira by resolution GmbH** in het zoekvak, selecteer **SAML SSO for Jira by resolution GmbH** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen. U kunt ook de naam van de ondernemingstoepassing wijzigen.

     ![SAML SSO for Jira by resolution GmbH in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Eenmalige aanmelding configureren en testen met de SAML SSO-invoegtoepassing en Azure AD

In deze sectie gaat u eenmalige aanmelding testen en configureren voor Jira voor een Azure AD-gebruiker. Dit gebeurt voor een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAML SSO for Jira by resolution GmbH tot stand is gebracht.

Als u eenmalige aanmelding wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[De Azure AD-bedrijfstoepassing configureren voor eenmalige aanmelding](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – De Azure AD-bedrijfstoepassing configureren voor de eenmalige aanmelding
2. **[SAML SSO-invoegtoepassing configureren van uw Jira-instantie](#configure-the-saml-sso-plugin-of-your-jira-instance)** – De instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** – Een testgebruiker maken in Azure AD.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** – De testgebruiker inschakelen de eenmalige aanmelding aan de Azure-kant te gebruiken.
1. **[De testgebruiker maken in Jira](#create-the-test-user-also-in-jira)** – Maak een tegenhanger in Jira voor de Azure AD-testgebruiker.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** – Controleer of de configuratie werkt.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configureren van eenmalige aanmelding voor een Azure AD-ondernemingstoepassing

In deze sectie gaat u eenmalige aanmelding in de Azure-portal instellen.

Voer de volgende stappen uit om eenmalige aanmelding met SAML SSO for Jira by resolution GmbH te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in de zojuist gemaakte ondernemingstoepassing **SAML SSO for Jira by resolution GmbH** **Eenmalige aanmelding** in het linkerpaneel.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer de **SAML**-modus voor **Eenmalige aanmeldingsmethode selecteren** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Klik daarna op het pictogram **Bewerken** om het dialoogvenster **Standaardconfiguratie van SAML** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname van de sectie 'Standaard SAML-configuratie' met de tekstvakken 'Id' en 'Antwoord-URL' gemarkeerd en de knop 'Opslaan' geselecteerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over domein en URL's voor eenmalige aanmelding met SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Vervang **\<server-base-url>** met de basis-URL van uw Jira-exemplaar voor de id, de antwoord-URL en de aanmeldings-URL. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal. Als u een probleem hebt, neem contact op met het [Klantondersteuningsteam voor SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** downloadt u **Federatieve metagegevens XML** en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>De SAML SSO-invoegtoepassing van uw Jira-exemplaar configureren 

1. Meld u in een ander browservenster als beheerder aan bij uw Jira-instantie.

2. Beweeg de muis aanwijzer over het tandwiel aan de rechterkant en klik op **Apps beheren**.
    
    ![Schermopname met een pijl die wijst naar het tandwielpictogram en met 'Apps beheren' geselecteerd in de vervolgkeuzelijst.](./media/samlssojira-tutorial/addon1.png)

3. Als u wordt omgeleid naar de toegangspagina voor beheerders, voer het **Wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de toegangspagina voor beheerders.](./media/samlssojira-tutorial/addon2.png)

4. Jira leidt u normaal gesproken over naar de Atlassian marketplace. Als dat niet het geval is, klikt u op **Nieuwe apps zoeken** in het linkerpaneel. Zoek **SAML Single Sign On (SSO) for JIRA** en klik op de knop **Installeren** om de SAML-invoegtoepassing te installeren.

    ![Schermopname waarop de pagina 'Atlassian Marketplace voor JIRA' wordt weergegeven met een pijl die wijst naar de knop Installeren voor de app 'S A M L Single Sign On (S S O) Jira, S A M L/S S O'.](./media/samlssojira-tutorial/store.png)

5. De installatie van de invoegtoepassing wordt gestart. Wanneer u klaar bent, klikt u op de knop **Sluiten**.

    ![Schermopname van het dialoogvenster 'Installeren'.](./media/samlssojira-tutorial/store-2.png)

    ![Schermopname van het dialoogvenster 'Geïnstalleerd en klaar voor gebruik!' met de knop 'Sluiten' geselecteerd.](./media/samlssojira-tutorial/store-3.png)

6. Klik vervolgens op **Beheren**.

    ![Schermopname waarin de app 'S A M L Single Sign On (S S O) Jira, S A M L/S S O' wordt weergegeven met de knop 'Beheren' geselecteerd.](./media/samlssojira-tutorial/store-4.png)
    
8. Klik daarna op **Configureren** om de zojuist geïnstalleerde invoegtoepassing te configureren.

    ![Schermopname van de pagina 'Apps beheren', met de knop 'Configureren' geselecteerd voor de app 'S A M L SingleSignOn for Jira'.](./media/samlssojira-tutorial/store-5.png)

9. In de wizard **SAML SSO-invoegtoepassing configureren** klik op **Nieuwe IdP toevoegen** om Azure AD als een nieuwe id-provider te configureren.

    ![Schermopname van de pagina 'Welkom', met de knop 'Nieuwe IdP toevoegen' geselecteerd.](./media/samlssojira-tutorial/addon4.png) 

10. Voer op de pagina **Uw SAML-id-provider kiezen** de volgende stappen uit:

    ![Schermopname van de pagina 'Uw SAML-id-provider kiezen' met de tekstvakken 'Type IdP' en 'Naam' gemarkeerd en de knop 'Volgende' geselecteerd.](./media/samlssojira-tutorial/addon5a.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **Naam** van de id-provider toe (bijvoorbeeld Azure AD).
    
    c. Voeg een (optionele) **Omschrijving** van de id-provider toe (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
11. Op de pagina **Configuratie id-provider** klikt u op **Volgende**.
 
    ![Schermopname van de pagina 'Configuratie id-provider'.](./media/samlssojira-tutorial/addon5b.png)

12. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Schermopname van de pagina 'SAML-IDP-metagegevens importeren' met de actie 'XML-bestand met metagegevens selecteren' geselecteerd.](./media/samlssojira-tutorial/addon5c.png)

    a. Klik op de knop **XML-bestand met metagegevens selecteren** en kies het bestand **Federatieve metagegevens XML** dat u eerder hebt gedownload.

    b. Klik op de knop **Importeren**.
     
    c. Wacht even totdat het importeren is geslaagd.  
     
    d. Klik op de knop **Next**
    
13. Op de pagina **Gebruikers-id-kenmerk en transformatie** klikt u op de knop **Volgende**.

    ![Schermopname van de pagina 'Gebruikers-id-kenmerk en transformatie' met de knop 'Volgende' geselecteerd.](./media/samlssojira-tutorial/addon5d.png)
    
14. Op de pagina **Gebruiker maken en bijwerken** klikt u op **Opslaan en volgende** om de instellingen op te slaan.
    
    ![Schermopname van de pagina 'Gebruiker maken en bijwerken' met de knop 'Opslaan en volgende' geselecteerd.](./media/samlssojira-tutorial/addon6a.png)
    
15. Op de pagina **Uw instellingen testen** klikt u op **Test overslaan en handmatig configureren** om de gebruikerstest nu over te slaan. Dit wordt uitgevoerd in de volgende sectie en daarvoor zijn bepaalde instellingen in de Azure-portal vereist.
    
    ![Schermopname van de pagina 'Uw instellingen testen' met de knop 'Test overslaan en handmatig configureren' geselecteerd.](./media/samlssojira-tutorial/addon6b.png)
    
16. Klik op **OK** om de waarschuwing over te slaan.
    
    ![Schermopname van het waarschuwingsvenster waarin de knop 'OK' is geselecteerd.](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken. Met de gebruiker wordt eenmalige aanmelding getest.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Kies **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In **Gebruikerseigenschappen** voert u de volgende stappen uit:

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. Voer in het veld **Gebruikersnaam** <b>BrittaSimon@contoso.com</b> in.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie voegt u Britta Simon toe aan de ondernemingstoepassing, zodat ze eenmalige aanmelding kan gebruiken.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**. 

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Zoek in de lijst toepassingen naar de ondernemingstoepassing die u aan het begin van deze zelfstudie hebt gemaakt. Als u de stappen van de zelfstudie volgt, wordt deze **SAML SSO for Jira by resolution GmbH** genoemd. Als u het een andere naam hebt gegeven, zoekt u die naam.

    ![De koppeling SAML SSO for Jira by resolution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Klik in het linkerpaneel op **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onderaan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-the-test-user-also-in-jira"></a>Maak de testgebruiker ook in Jira

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAML SSO for Jira by resolution GmbH, moeten ze worden ingericht in SAML SSO for Jira by resolution GmbH. In het geval van deze zelfstudie moet u de inrichting handmatig uitvoeren. Er zijn echter ook andere inrichtingsmodellen beschikbaar voor de SAML SSO-invoegtoepassing per resolutie, bijvoorbeeld **Just In Time** inrichten. Raadpleeg de documentatie bij [SAML by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Als u hierover een vraag hebt, neemt u contact op met in [Resolutie-ondersteuning](https://www.resolution.de/go/support).

**Voor het handmatig inrichten van een gebruikersaccount moet u de volgende stappen uitvoeren:**

1. Meld u aan bij Jira-exemplaar als beheerder.

2. Beweeg de muisaanwijzer over het tandwiel en selecteer **Gebruikersbeheer**.

   ![Schermopname van een pijl die wijst naar het tandwielpictogram en met 'Gebruikersbeheer' geselecteerd in de vervolgkeuzelijst.](./media/samlssojira-tutorial/user1.png)

3. Als u wordt omgeleid naar de pagina Toegang voor beheerders, voer het **Wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de pagina 'Beheerderstoegang' met het tekstvak 'Wachtwoord' gemarkeerd.](./media/samlssojira-tutorial/user2.png) 

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Schermopname van het tabblad 'Gebruikersbeheer' met 'Gebruiker maken' geselecteerd.](./media/samlssojira-tutorial/user3-new.png) 

5. In het dialoogvenster **Nieuwe gebruiker maken** voert u de volgende stappen uit. U moet de gebruiker op dezelfde manier maken als in Azure AD:

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user4-new.png) 

    a. Typ in het tekstvak **Emailadres** het e-mailadres van de gebruiker: <b>BrittaSimon@contoso.com</b>.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker: **Britta Simon**.

    c. Typ in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker <b>BrittaSimon@contoso.com</b>. 

    d. Typ in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker.

    e. Klik op **Gebruikers maken** om het maken van de gebruiker te voltooien.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel SAML SSO for Jira by resolution GmbH klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van SAML SSO for Jira by resolution GmbH waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U kunt ook eenmalige aanmelding testen als u navigeert naar `https://<server-base-url>/plugins/servlet/samlsso`. Vervang **\<server-base-url>** door de basis-URL van uw Jira-exemplaar.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Omleiding van eenmalige aanmelding voor Jira inschakelen

Zoals vermeld in de sectie voor, zijn er momenteel twee manieren om eenmalige aanmelding te activeren. U kunt met behulp van de **Azure-portal** of **een speciale koppeling naar uw Jira-exemplaar** gebruiken. Met de SAML SSO-invoegtoepassing van de oplossing GmbH kunt u eenmalige aanmelding activeren door eenvoudigweg **toegang te krijgen tot een URL die verwijst naar uw Jira-exemplaar**.

In essentie worden alle gebruikers die toegang hebben tot Jira, omgeleid naar de eenmalige aanmelding na het activeren van een optie in de invoegtoepassing.

Als u SSO-omleiding wilt activeren, gaat u als volgt te werk in **uw Jira-exemplaar**:

1. Open de configuratie pagina van de SAML SSO-invoegtoepassing in Jira.
1. Klik op **Omleiding** in het linkerpaneel.

   ![Gedeeltelijke schermafbeelding van de configuratiepagina van de Jira SAML SingleSignOn-invoegtoepassing met markering van de omleidingskoppeling in de linkernavigatiebalk.](./media/samlssojira-tutorial/ssore1.png)

1. Tik op **SSO-omleiding inschakelen**.

   ![Gedeeltelijke schermopname van de configuratiepagina van de Jira SAML SingleSignOn-invoegtoepassing met markering van het geselecteerde selectievakje "SSO-omleiding inschakelen".](./media/samlssojira-tutorial/ssore2.png) 

1. Klik in de rechterbovenhoek op de knop **Instellingen opslaan**.

Nadat u de optie hebt geactiveerd, kunt u nog steeds de prompt voor gebruikersnaam en wachtwoord bereiken als de optie **Nosso inschakelen** is aangevinkt door naar `https://<server-base-url>/login.jsp?nosso`te navigeren. Zoals gewoonlijk, vervang **\<server-base-url>** met de basis-URL.


## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)