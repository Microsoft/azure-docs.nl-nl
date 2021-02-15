---
title: 'Zelfstudie: Azure Active Directory-integratie met SAML SSO for Confluence by resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 64e358ef6c20c72b1a6a406df1e49ca5a9763b1c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094241"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Zelfstudie: Azure Active Directory-integratie met SAML SSO for Confluence by resolution GmbH

In deze zelf studie leert u hoe u SAML SSO kunt integreren voor confluence door Solution GmbH met Azure Active Directory (Azure AD). Wanneer u SAML SSO integreert voor confluence door de oplossing GmbH met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot SAML SSO voor confluence by Solution GmbH.
* Stel in dat uw gebruikers automatisch worden aangemeld bij SAML SSO voor confluence door de oplossing GmbH te gebruiken met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAML SSO voor confluence door de oplossing voor eenmalige aanmelding in het abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO for Confluence by resolution GmbH ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>SAML SSO voor confluence toevoegen door de oplossing van de galerie

Als u de integratie van SAML SSO for Confluence by resolution GmbH met Azure AD wilt configureren, moet u SAML SSO for Confluence by resolution GmbH vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAML SSO voor confluence by Solution GmbH** in het zoekvak.
1. Selecteer **SAML SSO voor confluence by Solution GmbH** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Azure AD SSO voor SAML SSO configureren en testen voor confluence door de oplossing GmbH

Azure AD SSO configureren en testen met SAML SSO voor confluence by Solution GmbH met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAML SSO voor confluence door de oplossing GmbH.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SAML SSO voor confluence door Solution GmbH:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[SAML SSO configureren voor confluence door de oplossing GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** -om de instellingen voor één Sign-On te configureren aan de kant van de toepassing.
    1. **[Testgebruiker van SAML SSO for Confluence by resolution GmbH maken](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)**: als u een tegenhanger van Britta Simon in SAML SSO for Confluence by resolution GmbH wilt hebben die is gekoppeld aan de Azure AD-versie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor het oplossen van problemen met de integratie **van de** confluence-app voor de SAML- **SSO voor de oplossing** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan SAML SSO voor confluence door de oplossing GmbH.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAML SSO voor confluence by resolution GmbH**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>SAML SSO configureren voor confluence met de oplossing GmbH SSO

1. Meld u in een ander webbrowservenster aan bij uw **SAML SSO for Confluence by resolution GmbH-beheerportal** als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.
    
    ![Schermopname met het tandwielpictogram geselecteerd en 'Invoegtoepassingen' geselecteerd in de vervolgkeuzelijst.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. U wordt omgeleid naar de pagina voor beheerderstoegang. Voer het wachtwoord in en klik op **Bevestigen**.

    ![Schermopname van de pagina 'Beheerderstoegang' met de knop 'Bevestigen' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Op het tabblad **ATLASSIAN MARKETPLACE** klikt u op **Nieuwe invoegtoepassingen zoeken**. 

    ![Schermopname van het tabblad 'Attlassian Marketplace' met 'Nieuwe invoegtoepassingen zoeken' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Zoek **SAML Single Sign On (SSO) for Confluence** en klik op de knop **Installeren** om de nieuwe SAML-invoegtoepassing te installeren.

    ![Schermopname waarin de pagina 'Nieuwe invoegtoepassingen zoeken' wordt weergegeven met 'SAML Single Sign On (SSO) for Confluence' in het zoekvak en de knop 'Installeren' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. De installatie van de invoegtoepassing wordt gestart. Klik op **Sluiten**.

    ![Schermopname van het dialoogvenster 'Installeren'.](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Schermopname van het dialoogvenster 'Geïnstalleerd en klaar voor gebruik!' met de actie 'Sluiten' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Klik op **Beheren**.

    ![Schermopname waarin de app-pagina 'S A M L Single Sign On (S S O) for Confluence' wordt weergegeven met de knop 'Beheren' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname van de pagina 'Beheren' met de knop 'Configureren' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Deze nieuwe invoegtoepassing is ook terug te vinden op het tabblad **GEBRUIKERS EN BEVEILIGING**.

    ![Schermopname van het tabblad 'Gebruikers en beveiliging' met 'S A M L SingleSignOn' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Klik op de pagina **SAML SSO-invoegtoepassing configureren** op de knop **Nieuwe IDP toevoegen** om de instellingen van de id-provider te configureren.

    ![Schermopname van de pagina 'SAML SSO-invoegtoepassing configureren' met de knop 'Nieuwe IDP toevoegen' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Voer op de pagina **Choose your SAML Identity Provider** (Uw SAML-id-provider kiezen) de volgende stappen uit:

    ![Schermopname van de pagina 'Uw SAML-id-provider kiezen' met de tekstvakken 'Type IdP', 'Naam' en 'Beschrijving' gemarkeerd.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **naam** van de id-provider toe (bijvoorbeeld Azure AD).
    
    c. Voeg de **omschrijving** van de id-provider toe (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
12. Op de pagina voor het **configureren van de id-provider** klikt u op de knop **Volgende**.

    ![Schermopname van de pagina voor het configureren van de id-provider met de knop 'Volgende' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Schermopname van de pagina 'SAML-IDP-metagegevens importeren' met de knoppen 'Importeren', 'Bestand laden' en 'Volgende' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Klik op de knop **Bestand laden** en kies het XML-metagegevensbestand dat u in stap 5 hebt gedownload.

    b. Klik op **Importeren**.
    
    c. Wacht totdat het importeren is geslaagd.
    
    d. Klik op de knop **Volgende**.
    
14. Op de pagina **Gebruikers-id-kenmerk en transformatie** klikt u op de knop **Volgende**.

    ![Schermopname van de pagina 'Gebruikers-id-kenmerk en transformatie' met de knop 'Volgende' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Op de pagina **Gebruiker maken en bijwerken** klikt u op **Opslaan en volgende** om de instellingen op te slaan.   
    
    ![Schermopname van de pagina 'Gebruiker maken en bijwerken' met de knop 'Opslaan en volgende' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Op de pagina **Uw instellingen testen** klikt u op **Test overslaan en handmatig configureren** om de gebruikerstest nu over te slaan. Deze test wordt uitgevoerd in de volgende sectie en daarvoor zijn bepaalde instellingen in de Azure-portal vereist. 
    
    ![Schermopname van de pagina 'Uw instellingen testen' met de knop 'Test overslaan en handmatig configureren' geselecteerd.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Klik in het dialoogvenster waarin staat **Als u de test overslaat, betekent dit...** op **OK**.
    
    ![Eenmalige aanmelding configureren](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Testgebruiker voor SAML SSO for Confluence by resolution GmbH maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAML SSO for Confluence by resolution GmbH, moeten deze worden ingericht in SAML SSO for Confluence by resolution GmbH.  
Inrichten is een handmatige taak in SAML SSO for Confluence by resolution GmbH.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij de bedrijfssite van SAML SSO for Confluence by resolution GmbH als een beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Schermopname met het tandwielpictogram geselecteerd en 'Gebruikersbeheer' geselecteerd in het menu.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Klik onder de sectie Users op het tabblad **Add users**. Voer de volgende stappen uit in het dialoogvenster **Add a User**:

    ![Werknemer toevoegen](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Typ in het tekstvak **Username** het e-mailadres van de gebruiker, zoals Britta Simon.

    b. Typ in het tekstvak **Full Name** de volledige naam van de gebruiker, zoals Britta Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. Typ in het tekstvak **Password** het wachtwoord van Britta Simon.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.
    
    f. Klik op de knop **Add**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar SAML SSO voor confluence by Solution GmbH sign on URL, waar u de aanmeldings stroom kunt initiëren.  

* Ga naar SAML SSO voor confluence by resolution GmbH sign-on URL direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de SAML SSO voor confluence door de oplossing GmbH waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel SAML SSO voor confluence by Solution GmbH in de groep mijn apps klikt, wordt u als geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de SAML SSO voor confluence door de oplossing GmbH waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u de SAML SSO voor confluence hebt geconfigureerd door Solution GmbH, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
