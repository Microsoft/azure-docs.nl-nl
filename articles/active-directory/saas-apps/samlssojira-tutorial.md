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
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 827a05a8dfbf05b0dacb0bd812fb964567f39b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954204"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Zelfstudie: Azure Active Directory-integratie met SAML SSO for Jira by resolution GmbH

In deze zelf studie leert u hoe u SAML SSO kunt integreren voor Jira door Solution GmbH met Azure Active Directory (Azure AD). Wanneer u SAML SSO integreert voor Jira door de oplossing GmbH met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot SAML SSO voor Jira by Solution GmbH.
* Stel in dat uw gebruikers automatisch worden aangemeld bij SAML SSO voor Jira door de oplossing GmbH te gebruiken met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAML SSO voor Jira door de oplossing voor eenmalige aanmelding in het abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO voor Jira by Solution GmbH ondersteunt door **SP** en **IDP** geïnitieerde SSO.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>SAML SSO voor Jira toevoegen door de oplossing van de galerie

Voor het configureren van de integratie van SAML SSO for Jira by resolution GmbH in Azure AD moet u SAML SSO for Jira by resolution GmbH vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAML SSO voor Jira by Solution GmbH** in het zoekvak.
1. Selecteer **SAML SSO voor Jira by Solution GmbH** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Azure AD SSO voor SAML SSO configureren en testen voor Jira door de oplossing GmbH

Azure AD SSO configureren en testen met SAML SSO voor Jira by Solution GmbH met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAML SSO voor Jira door de oplossing GmbH.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met SAML SSO voor Jira door Solution GmbH:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAML SSO configureren voor Jira met de oplossing GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak SAML SSO voor Jira door de Solution GmbH-test gebruiker](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** : als u een equivalent van B. Simon in SAML SSO wilt hebben voor Jira door de oplossing GmbH die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina voor het oplossen van problemen met de integratie **van de** Jira-app voor de SAML- **SSO voor de oplossing** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Vervang **\<server-base-url>** met de basis-URL van uw Jira-exemplaar voor de id, de antwoord-URL en de aanmeldings-URL. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal. Als u een probleem hebt, neem contact op met het [Klantondersteuningsteam voor SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** downloadt u **Federatieve metagegevens XML** en slaat u dit op uw computer op.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan SAML SSO voor Jira door de oplossing GmbH.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SAML SSO voor Jira by resolution GmbH**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>SAML SSO configureren voor Jira met de oplossing GmbH SSO 

1. Meld u in een ander browservenster als beheerder aan bij uw Jira-instantie.

2. Beweeg de muis aanwijzer over het tandwiel aan de rechterkant en klik op **Apps beheren**.
    
    ![Schermopname met een pijl die wijst naar het tandwielpictogram en met 'Apps beheren' geselecteerd in de vervolgkeuzelijst.](./media/samlssojira-tutorial/add-on-1.png)

3. Als u wordt omgeleid naar de toegangspagina voor beheerders, voer het **Wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de toegangspagina voor beheerders.](./media/samlssojira-tutorial/add-on-2.png)

4. Jira leidt u normaal gesproken over naar de Atlassian marketplace. Als dat niet het geval is, klikt u op **Nieuwe apps zoeken** in het linkerpaneel. Zoek **SAML Single Sign On (SSO) for JIRA** en klik op de knop **Installeren** om de SAML-invoegtoepassing te installeren.

    ![Schermopname waarop de pagina 'Atlassian Marketplace voor JIRA' wordt weergegeven met een pijl die wijst naar de knop Installeren voor de app 'S A M L Single Sign On (S S O) Jira, S A M L/S S O'.](./media/samlssojira-tutorial/store.png)

5. De installatie van de invoegtoepassing wordt gestart. Wanneer u klaar bent, klikt u op de knop **Sluiten**.

    ![Schermopname van het dialoogvenster 'Installeren'.](./media/samlssojira-tutorial/store-2.png)

    ![Schermopname van het dialoogvenster 'Geïnstalleerd en klaar voor gebruik!' met de knop 'Sluiten' geselecteerd.](./media/samlssojira-tutorial/store-3.png)

6. Klik vervolgens op **Beheren**.

    ![Schermopname waarin de app 'S A M L Single Sign On (S S O) Jira, S A M L/S S O' wordt weergegeven met de knop 'Beheren' geselecteerd.](./media/samlssojira-tutorial/store-4.png)
    
7. Klik daarna op **Configureren** om de zojuist geïnstalleerde invoegtoepassing te configureren.

    ![Schermopname van de pagina 'Apps beheren', met de knop 'Configureren' geselecteerd voor de app 'S A M L SingleSignOn for Jira'.](./media/samlssojira-tutorial/store-5.png)

8. In de wizard **SAML SSO-invoegtoepassing configureren** klik op **Nieuwe IdP toevoegen** om Azure AD als een nieuwe id-provider te configureren.

    ![Schermopname van de pagina 'Welkom', met de knop 'Nieuwe IdP toevoegen' geselecteerd.](./media/samlssojira-tutorial/add-on-4.png) 

9. Voer op de pagina **Uw SAML-id-provider kiezen** de volgende stappen uit:

    ![Schermopname van de pagina 'Uw SAML-id-provider kiezen' met de tekstvakken 'Type IdP' en 'Naam' gemarkeerd en de knop 'Volgende' geselecteerd.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Stel **Azure AD** als het type id-provider.
    
    b. Voeg de **Naam** van de id-provider toe (bijvoorbeeld Azure AD).
    
    c. Voeg een (optionele) **Omschrijving** van de id-provider toe (bijvoorbeeld Azure AD).
    
    d. Klik op **Volgende**.
    
10. Op de pagina **Configuratie id-provider** klikt u op **Volgende**.
 
    ![Schermopname van de pagina 'Configuratie id-provider'.](./media/samlssojira-tutorial/configuration.png)

11. Op de pagina **SAML-IDP-metagegevens importeren** voert u de volgende stappen uit:

    ![Schermopname van de pagina 'SAML-IDP-metagegevens importeren' met de actie 'XML-bestand met metagegevens selecteren' geselecteerd.](./media/samlssojira-tutorial/metadata.png)

    a. Klik op de knop **XML-bestand met metagegevens selecteren** en kies het bestand **Federatieve metagegevens XML** dat u eerder hebt gedownload.

    b. Klik op de knop **Importeren**.
     
    c. Wacht even totdat het importeren is geslaagd.  
     
    d. Klik op de knop **Next**
    
12. Op de pagina **Gebruikers-id-kenmerk en transformatie** klikt u op de knop **Volgende**.

    ![Schermopname van de pagina 'Gebruikers-id-kenmerk en transformatie' met de knop 'Volgende' geselecteerd.](./media/samlssojira-tutorial/transformation.png)
    
13. Op de pagina **Gebruiker maken en bijwerken** klikt u op **Opslaan en volgende** om de instellingen op te slaan.
    
    ![Schermopname van de pagina 'Gebruiker maken en bijwerken' met de knop 'Opslaan en volgende' geselecteerd.](./media/samlssojira-tutorial/update.png)
    
14. Op de pagina **Uw instellingen testen** klikt u op **Test overslaan en handmatig configureren** om de gebruikerstest nu over te slaan. Dit wordt uitgevoerd in de volgende sectie en daarvoor zijn bepaalde instellingen in de Azure-portal vereist.
    
    ![Schermopname van de pagina 'Uw instellingen testen' met de knop 'Test overslaan en handmatig configureren' geselecteerd.](./media/samlssojira-tutorial/test.png)
    
15. Klik op **OK** om de waarschuwing over te slaan.
    
    ![Schermopname van het waarschuwingsvenster waarin de knop 'OK' is geselecteerd.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Testgebruiker voor SAML SSO for Jira by resolution GmbH maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAML SSO for Jira by resolution GmbH, moeten ze worden ingericht in SAML SSO for Jira by resolution GmbH. In het geval van deze zelfstudie moet u de inrichting handmatig uitvoeren. Er zijn echter ook andere inrichtingsmodellen beschikbaar voor de SAML SSO-invoegtoepassing per resolutie, bijvoorbeeld **Just In Time** inrichten. Raadpleeg de documentatie bij [SAML by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Als u hierover een vraag hebt, neemt u contact op met in [Resolutie-ondersteuning](https://www.resolution.de/go/support).

**Voor het handmatig inrichten van een gebruikersaccount moet u de volgende stappen uitvoeren:**

1. Meld u aan bij Jira-exemplaar als beheerder.

2. Beweeg de muisaanwijzer over het tandwiel en selecteer **Gebruikersbeheer**.

   ![Schermopname van een pijl die wijst naar het tandwielpictogram en met 'Gebruikersbeheer' geselecteerd in de vervolgkeuzelijst.](./media/samlssojira-tutorial/user-1.png)

3. Als u wordt omgeleid naar de pagina Toegang voor beheerders, voer het **Wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de pagina 'Beheerderstoegang' met het tekstvak 'Wachtwoord' gemarkeerd.](./media/samlssojira-tutorial/user-2.png) 

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Schermopname van het tabblad 'Gebruikersbeheer' met 'Gebruiker maken' geselecteerd.](./media/samlssojira-tutorial/user-3-new.png) 

5. In het dialoogvenster **Nieuwe gebruiker maken** voert u de volgende stappen uit. U moet de gebruiker op dezelfde manier maken als in Azure AD:

    ![Werknemer toevoegen](./media/samlssojira-tutorial/user-4-new.png) 

    a. Typ in het tekstvak **Emailadres** het e-mailadres van de gebruiker: <b>BrittaSimon@contoso.com</b>.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker: **Britta Simon**.

    c. Typ in het tekstvak **Gebruikersnaam** het e-mailadres van de gebruiker <b>BrittaSimon@contoso.com</b>. 

    d. Typ in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker.

    e. Klik op **Gebruikers maken** om het maken van de gebruiker te voltooien.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar SAML SSO voor Jira by Solution GmbH sign on URL, waar u de aanmeldings stroom kunt initiëren.  

* Ga naar SAML SSO voor Jira by resolution GmbH sign-on URL direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de SAML SSO voor Jira door de oplossing GmbH waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel SAML SSO voor Jira by resolution GmbH in het gedeelte mijn apps klikt, wordt u als geconfigureerd in de SP-modus, omgeleid naar de aanmeldings pagina van de toepassing voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de SAML SSO voor Jira door de oplossing GmbH waarvoor u de SSO instelt. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="enable-sso-redirection-for-jira"></a>SSO-omleiding inschakelen voor Jira

Zoals vermeld in de sectie voor, zijn er momenteel twee manieren om eenmalige aanmelding te activeren. U kunt met behulp van de **Azure-portal** of **een speciale koppeling naar uw Jira-exemplaar** gebruiken. Met de SAML SSO-invoegtoepassing van de oplossing GmbH kunt u eenmalige aanmelding activeren door eenvoudigweg **toegang te krijgen tot een URL die verwijst naar uw Jira-exemplaar**.

In essentie worden alle gebruikers die toegang hebben tot Jira, omgeleid naar de eenmalige aanmelding na het activeren van een optie in de invoegtoepassing.

Als u SSO-omleiding wilt activeren, gaat u als volgt te werk in **uw Jira-exemplaar**:

1. Open de configuratie pagina van de SAML SSO-invoegtoepassing in Jira.
1. Klik op **Omleiding** in het linkerpaneel.

   ![Gedeeltelijke schermafbeelding van de configuratiepagina van de Jira SAML SingleSignOn-invoegtoepassing met markering van de omleidingskoppeling in de linkernavigatiebalk.](./media/samlssojira-tutorial/configure-1.png)

1. Tik op **SSO-omleiding inschakelen**.

   ![Gedeeltelijke schermopname van de configuratiepagina van de Jira SAML SingleSignOn-invoegtoepassing met markering van het geselecteerde selectievakje "SSO-omleiding inschakelen".](./media/samlssojira-tutorial/configure-2.png) 

1. Klik in de rechterbovenhoek op de knop **Instellingen opslaan**.

Nadat u de optie hebt geactiveerd, kunt u nog steeds de prompt voor gebruikersnaam en wachtwoord bereiken als de optie **Nosso inschakelen** is aangevinkt door naar `https://<server-base-url>/login.jsp?nosso`te navigeren. Zoals gewoonlijk, vervang **\<server-base-url>** met de basis-URL.

## <a name="next-steps"></a>Volgende stappen

Nadat u de SAML SSO voor Jira hebt geconfigureerd door Solution GmbH, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).