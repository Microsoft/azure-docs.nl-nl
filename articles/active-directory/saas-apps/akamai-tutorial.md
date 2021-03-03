---
title: 'Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met Akamai | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Akamai.
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
ms.openlocfilehash: 8838e3c92a2c7ccc77794973b3cb8e67128e3c71
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Akamai

In deze zelfstudie leert u hoe u Akamai kunt integreren met Azure Active Directory (Azure AD). Wanneer u Akamai integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie toegang heeft tot Akamai.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Akamai.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Integratie van Azure Active Directory met Akamai Enterprise Application Access biedt naadloze toegang tot verouderde toepassingen die in de cloud of on-premises worden gehost. De geïntegreerde oplossing profiteert van alle moderne functies van Azure Active Directory zoals [voorwaardelijke toegang van Azure AD](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) en [Azure AD Identity Governance](../governance/identity-governance-overview.md) voor toegang tot verouderde toepassingen zonder dat de toepassing moet worden aangepast of er agents moeten worden geïnstalleerd.

In de onderstaande afbeelding wordt beschreven waar Akamai EAA past in het uitgebreide hybride-scenario voor beveiligde toegang.

![Akamai EAA past in het bredere scenario voor hybride beveiligde toegang](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Belangrijkste verificatiescenario's

Naast dat het systeemeigen integratie van Azure Active Directory voor moderne verificatieprotocollen zoals Open ID Connect, SAML en WS-Fed ondersteunt, breidt Akamai EAA beveiligde toegang uit voor op verouderde verificatietoepassingen voor zowel interne als externe toegang met Azure AD, waardoor moderne scenario's (zoals toegang zonder wachtwoord) mogelijk worden voor deze toepassingen. Dit omvat:

* Op headers gebaseerde verificatietoepassingen
* Extern bureaublad
* SSH (Secure Shell)
* Kerberos-verificatietoepassingen
* VNC (Virtual Network Computing)
* Anonieme verificatie of geen ingebouwde verificatietoepassingen
* NTLM-verificatietoepassingen (beveiliging met dubbele prompts voor de gebruiker)
* Op formulieren gebaseerde toepassing (beveiliging met dubbele prompts voor de gebruiker)

### <a name="integration-scenarios"></a>Integratiescenario's

Samen bieden Microsoft en Akamai EAA de flexibiliteit om aan uw bedrijfsvereisten te voldoen door meerdere integratiescenario's op basis van uw bedrijfsbehoeften te ondersteunen. Deze kunnen worden gebruikt om een zero-day dekking voor alle toepassingen te bieden en om de juiste beleidsclassificaties geleidelijk te classificeren en te configureren.

#### <a name="integration-scenario-1"></a>Integratiescenario 1

Akamai EAA is geconfigureerd als één toepassing in Azure AD. De beheerder kan het beleid voor voorwaardelijke toegang voor de toepassing configureren en wanneer aan de voorwaarden wordt voldaan, kunnen gebruikers toegang krijgen tot de Akamai EAA-portal.

**Voordelen**:

* U hoeft slechts één keer IDP te configureren.

**Nadelen**:

* Gebruikers eindigen op twee toepassings portals.

* Eén gemeenschappelijke dekking voor beleid voor voorwaardelijke toegang voor alle toepassingen.

![Integratiescenario 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Integratiescenario 2

De Akamai EAA-toepassing wordt afzonderlijk ingesteld in de Azure AD-portal. De beheerder kan afzonderlijke instanties van het beleid voor voorwaardelijke toegang configureren voor de toepassing(en) en zodra aan de voorwaarden wordt voldaan, kunnen gebruikers rechtstreeks worden omgeleid naar de specifieke toepassing.

**Voordelen**:

* U kunt afzonderlijke CA-beleids regels definiëren.

* Alle apps worden weergegeven in het 0365-wafelmenu en het deelvenster op myApps.microsoft.com.


**Nadelen**:

* U moet meerdere IDP configureren.

![Integratiescenario 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Akamai waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

- Akamai ondersteunt door IDP geïnitieerde SSO.

#### <a name="important"></a>Belangrijk

Alle hieronder genoemde instellingen zijn hetzelfde voor **Integratiescenario 1** en **Integratiescenario 2**. Voor **Integratiescenario 2** moet u afzonderlijke IDP in de Akamai EAA instellen en moet de URL-eigenschap worden gewijzigd om naar de URL van de toepassing te verwijzen.

![Schermopname van het tabblad Algemeen voor AZURESSO-SP in Akamai Enterprise Application Access. Het veld met de configuratie-URL voor de verificatie is gemarkeerd.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Akamai toevoegen vanuit de galerie

Als u de integratie van Akamai in Azure AD wilt configureren, moet u Akamai vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Akamai** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Akamai** in het deelvenster met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Azure AD SSO voor Akamai configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Akamai met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Akamai.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Akamai:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Akamai configureren](#configure-akamai-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[IDP instellen](#setting-up-idp)**
    * **[Verificatie op basis van headers](#header-based-authentication)**
    * **[Extern bureaublad](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos-verificatie](#kerberos-authentication)**
    * **[Testgebruiker voor Akamai maken](#create-akamai-test-user)** : als u een tegenhanger van B.Simon in Akamai wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Akamai** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [Ondersteuningsteam voor Akamai](https://www.akamai.com/us/en/contact-us/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Akamai instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Akamai.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Akamai** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-akamai-sso"></a>Eenmalige aanmelding bij Akamai configureren

### <a name="setting-up-idp"></a>IDP instellen

**IDP-configuratie voor AKAMAI EAA**

1. Meld u aan bij de **Akamai Enterprise Application Access**-console.
1. Selecteer in de **Akamai EAA-console** de optie **Identity** > **Identity Providers** en klik op **Add Identity Provider**.

    ![Schermopname van het venster Identity Providers in de Akamai EAA-console. Selecteer Identity Providers in het menu Identity en selecteer vervolgens Add Identity Provider.](./media/header-akamai-tutorial/configure-1.png)

1. Voer de volgende stappen uit bij **Create New Identity Provider**:

    ![Schermopname van het dialoogvenster Create New Identity Providers in de Akamai EAA-console.](./media/header-akamai-tutorial/configure-2.png)

    a. Geef de **Unique Name** op.

    b. Kies **Third Party SAML** en klik op **Create Identity Provider and Configure**.

### <a name="general-settings"></a>Algemene instellingen

1. **Identity-interceptie** : Geef de naam op van de (SP Base-URL) die wordt gebruikt voor de Azure AD-configuratie.

    > [!NOTE]
    > U kunt ervoor kiezen om uw eigen aangepaste domein te gebruiken (een DNS-vermelding en een certificaat zijn vereist). In dit voorbeeld gaan we het Akamai-domein gebruiken.

1. **Akamai Cloud Zone**: selecteer de juiste cloudzone.
1. **Certificaat validatie** : Raadpleeg de Akamai-documentatie (optioneel).

    ![Schermopname van het tabblad General in de Akamai EAA-console met de instellingen voor Identity Intercept, Akamai Cloud Zone en Certificate Validation.](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Verificatie configuratie

1. URL: geef dezelfde URL op als voor Identity Intercept (dit is de locatie waarnaar gebruikers worden omgeleid na verificatie).
2. Logout URL: werk de afmeldings-URL bij.
3. Sign SAML Request: standaard uitgeschakeld.
4. Voeg voor het IDP-metagegevensbestand de toepassing toe aan de Azure AD-console.

    ![Schermopname van de verificatieconfiguratie in de Akamai EAA-console met de instellingen voor URL, Logout URL, Sign SAML Request en IDP Metadata File.](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Sessie-instellingen

Laat de instellingen op standaard staan.

![Schermopname van het dialoogvenster Session settings in de Akamai EAA-console.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Mappen

Sla de mappenconfiguratie over.

![Schermopname van het tabblad Directories in de Akamai EAA-console.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>UI voor aanpassing

U kunt aanpassingen toevoegen aan IDP.

![Schermopname van het tabblad Customization in de Akamai EAA-console met instellingen voor Customize UI, Language settings en Themes.](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Geavanceerde instellingen

Sla de geavanceerde instellingen over/raadpleeg de Akamai-documentatie voor meer informatie.

![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console met instellingen voor EAA Client, Advanced en OIDC to SAML bridging.](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Implementatie

1. Klik op Deploy Identity Provider.

    ![Schermopname van het tabblad Deployment in de Akamai EAA-console met de knop Deploy identity provider.](./media/header-akamai-tutorial/deployment.png)

2. Controleer of de implementatie is geslaagd.

### <a name="header-based-authentication"></a>Verificatie op basis van headers

Verificatie op basis van Akamai-headers

1. Kies **Custom HTTP** in de wizard Add Applications.

    ![Schermopname van de wizard Add Applications in de Akamai EAA-console met CustomHTTP die in de sectie Access Apps staat vermeld.](./media/header-akamai-tutorial/configure-5.png)

2. Voer de **Application Name** en **Description** in.

    ![Schermopname van het dialoogvenster Custom HTTP App met instellingen voor Application Name en Description.](./media/header-akamai-tutorial/configure-6.png)

    ![Schermopname van het tabblad General in de Akamai EAA-console met algemene instellingen voor MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Schermopname van de Akamai EAA-console met instellingen voor Certificate en Location.](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Verificatie

1. Selecteer het tabblad **Authentication**.

    ![Schermopname van de Akamai EAA-console waarin het tabblad Authentication is geselecteerd.](./media/header-akamai-tutorial/configure-9.png)

2. Wijs de **ID-provider** toe.

    ![Schermopname van het tabblad Authentication in de Akamai EAA-console voor MYHEADERAPP waarin Identity provider is ingesteld op Azure AD SSO.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Services

Klik op Save and Go to Authentication.

![Schermopname van het tabblad Services in de Akamai EAA-console voor MYHEADERAPP met de knop Save and go to AdvancedSettings in de rechteronderhoek.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Geef onder de **Customer HTTP Headers** de **CustomerHeader** en het **SAML Attribute** op.

    ![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console waarin het veld SSO Logged URL onder Authentication is gemarkeerd.](./media/header-akamai-tutorial/configure-12.png)

1. Klik op de knop **Save and go to Deployment**.

    ![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console met de knop Save and go to Deployment in de rechteronderhoek.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>De toepassing implementeren

1. Klik op de knop **Deploy Application**.

    ![Schermopname van het tabblad Deployment in de Akamai EAA-console met de knop Deploy application.](./media/header-akamai-tutorial/configure-14.png)

1. Controleer of de toepassing is geïmplementeerd.

    ![Schermopname van het tabblad Deployment in de Akamai EAA-console met het statusbericht van de toepassing: Application Successfully Deployed.](./media/header-akamai-tutorial/configure-15.png)

1. De ervaring voor de eindgebruiker.

    ![Schermopname van het openingsscherm voor myapps.microsoft.com met een achtergrondafbeelding en het dialoogvenster Aanmelden.](./media/header-akamai-tutorial/end-user-1.png)

    ![Schermopname van een deel van een Apps-venster met pictogrammen voor Invoegtoepassing, HRWEB, Akamai - CorpApps, Onkosten, Groepen en Toegangsbeoordelingen. ](./media/header-akamai-tutorial/end-user-2.png)

1. Voorwaardelijke toegang.

    ![Schermopname van het bericht: Aanmeldingsaanvraag goedkeuren. Er wordt een melding verzonden naar uw mobiele apparaat. Antwoord om door te gaan.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Schermopname van een venster Applications met een pictogram voor MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Extern bureaublad

1. Kies **RDP** in de wizard Add Applications.

    ![Schermopname van de wizard Add Applications in de Akamai EAA-console met RDP tussen de apps in de sectie Access Apps.](./media/header-akamai-tutorial/configure-16.png)

1. Voer de **Application Name** en **Description** in.

    ![Schermopname van het dialoogvenster RDP App met instellingen voor Application Name en Description.](./media/header-akamai-tutorial/configure-17.png)

    ![Schermopname van het tabblad General in de Akamai EAA-console met de instellingen voor Application identity voor MYHEADERAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Geef de connector op die dit gaat verwerken.

    ![Schermopname van de Akamai EAA-console met instellingen voor Certificate en Location. Gekoppelde connectors zijn ingesteld op USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Verificatie

Klik op **Save and go to Services**.

![Schermopname van het tabblad Authentication in de Akamai EAA-console voor SECRETRDPAPP met de knop Save and go to Services in de rechteronderhoek.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Services

Klik op **Save and go to Advanced Settings**.

![Schermopname van het tabblad Services in de Akamai EAA-console voor SECRETRDPAPP met de knop Save and go to AdvancedSettings in de rechteronderhoek.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Klik op **Save and go to Deployment**.

    ![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console voor SECRETRDPAPP met de instellingen voor Remote desktop configuration.](./media/header-akamai-tutorial/configure-22.png)

    ![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console voor SECRETRDPAPP met de instellingen voor Authentication en Health check configuration.](./media/header-akamai-tutorial/configure-23.png)

    ![Schermopname van de instellingen voor Custom HTTP headers in de Akamai EAA-console voor SECRETRDPAPP met de knop Save and go to Deployment in de rechteronderhoek.](./media/header-akamai-tutorial/configure-24.png)

1. De ervaring voor de eindgebruiker

    ![Schermopname van een venster op myapps.microsoft.com met een achtergrondafbeelding en het dialoogvenster Aanmelden.](./media/header-akamai-tutorial/end-user-3.png)

    ![Schermopname van het Apps-venster van myapps.microsoft.com met pictogrammen voor Invoegtoepassing, HRWEB, Akamai - CorpApps, Onkosten, Groepen en Toegangsbeoordelingen.](./media/header-akamai-tutorial/end-user-2.png)

1. Voorwaardelijke toegang

    ![Schermopname van het bericht over voorwaardelijke toegang: Aanmeldingsaanvraag goedkeuren. Er wordt een melding verzonden naar uw mobiele apparaat. Antwoord om door te gaan.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Schermopname van een venster Applications met een pictogram voor MyHeaderApp en SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Schermopname van een Windows Server 2012 RS-venster met algemene gebruikerspictogrammen. De pictogrammen voor beheerder, gebruiker0 en gebruiker1 geven aan dat ze zijn aangemeld.](./media/header-akamai-tutorial/conditional-access-6.png)

1. U kunt ook rechtstreeks de URL van de RDP-toepassing intypen.

#### <a name="ssh"></a>SSH

1. Ga naar Add Applications en kies **SSH**.

    ![Schermopname van de wizard Add Applications in de Akamai EAA-console met SSH tussen de apps in de sectie Access Apps.](./media/header-akamai-tutorial/configure-25.png)

1. Voer de **Application Name** en **Description** in.

    ![Schermopname van het dialoogvenster SSH App met instellingen voor Application Name en Description.](./media/header-akamai-tutorial/configure-26.png)

1. Configureer de toepassings-id.

    ![Schermopname van het tabblad General in de Akamai EAA-console met de instellingen voor Application identity voor SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Geef een naam/beschrijving op.

    b. Geef het IP/de FQDN en de poort van de toepassingsserver op voor SSH.

    c. Geef de gebruikersnaam/toegangscode voor SSH op * Controleer Akamai EAA.

    d. Geef de naam van de externe host op.

    e. Geef de locatie voor de connector op en kies de connector.

#### <a name="authentication"></a>Verificatie

Klik op **Save and go to Services**.

![Schermopname van het tabblad Authentication in de Akamai EAA-console voor SSH-SECURE met de knop Save and go to Services in de rechteronderhoek.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Services

Klik op **Save and go to Advanced Settings**.

![Schermopname van het tabblad Services in de Akamai EAA-console voor SSH-SECURE met de knop Save and go to AdvancedSettings in de rechteronderhoek.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

Klik op opslaan en om de implementatie te starten.

![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console voor SSH-SECURE met de instellingen voor Authentication en Health check configuration.](./media/header-akamai-tutorial/configure-30.png)

![Schermopname van de instellingen voor Custom HTTP headers in de Akamai EAA-console voor SSH-SECURE met de knop Save and go to Deployment in de rechteronderhoek.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Implementatie

1. Klik op **Deploy application**.

    ![Schermopname van het tabblad Deployment in de Akamai EAA-console voor SSH-SECURE met de knop Deploy application.](./media/header-akamai-tutorial/configure-32.png)

1. De ervaring voor de eindgebruiker

    ![Schermopname van een dialoogvenster Aanmelden in een scherm op myapps.microsoft.com.](./media/header-akamai-tutorial/end-user-3.png)

    ![Schermopname van het Apps-venster voor myapps.microsoft.com met pictogrammen voor Invoegtoepassing, HRWEB, Akamai - CorpApps, Onkosten, Groepen en Toegangsbeoordelingen.](./media/header-akamai-tutorial/end-user-4.png)

1. Voorwaardelijke toegang

    ![Schermopname met het bericht: Aanmeldingsaanvraag goedkeuren. Er wordt een melding verzonden naar uw mobiele apparaat. Antwoord om door te gaan.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Schermopname van een venster Applications met een pictogram voor MyHeaderApp, SSH Secure en SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Schermopname van een opdrachtvenster voor ssh-secure-go.akamai-access.com waarin een wachtwoordprompt wordt weergegeven.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Schermopname van een opdrachtvenster voor ssh-secure-go.akamai-access.com met informatie over de toepassing en waarin een opdrachtprompt wordt weergegeven.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos-verificatie

In het onderstaande voor beeld publiceren we een interne webserver <code>http://frp-app1.superdemo.live</code> en maken ze SSO met behulp van KCD.

#### <a name="general-tab"></a>Tabblad General

![Schermopname van het tabblad General in de Akamai EAA-console voor MYKERBOROSAPP.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Tabblad Verificatie

Wijs de ID-provider toe.

![Schermopname van het tabblad Authentication in de Akamai EAA-console voor MYKERBOROSAPP waarin Identity provider is ingesteld op Azure AD SSO.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Tabblad Services

![Schermopname van het tabblad Services in de Akamai EAA-console voor MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

![Schermopname van het tabblad Advanced Settings in de Akamai EAA-console voor MYKERBOROSAPP met instellingen voor Related Applications en Authentication.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> De SPN voor de webserver heeft de notatie SPN@Domain ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` voor deze demo. Laat de overige instellingen op de standaardwaarde staan.

#### <a name="deployment-tab"></a>Tabblad Deployment

![Schermopname van het tabblad Deployment in de Akamai EAA-console voor MYKERBOROSAPP met de knop Deploy application.](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Map toevoegen

1. Selecteer **AD** in de vervolgkeuzelijst.

    ![Schermopname van het venster Directories in de Akamai EAA-console met het dialoogvenster Create New Directory, waarin AD is geselecteerd in de vervolgkeuzelijst Directory Type.](./media/header-akamai-tutorial/configure-33.png)

1. Geef de benodigde gegevens op.

    ![Schermopname van het venster SUPERDEMOLIVE in de Akamai EAA-console met instellingen voor DirectoryName, Directory Service, Connector en Attribute mapping.](./media/header-akamai-tutorial/configure-34.png)

1. Controleer of de map is gemaakt.

    ![Schermopname van het venster Directories in de Akamai EAA-console waarin wordt getoond dat de map superdemo.live is toegevoegd.](./media/header-akamai-tutorial/directory-domain.png)

1. Voeg de groepen/organisatie-eenheden toe waarvoor toegang is vereist.

    ![Schermopname van de instellingen voor de map superdemo.live. Het pictogram dat u selecteert voor het toevoegen van groepen of organisatie-eenheden, is gemarkeerd.](./media/header-akamai-tutorial/add-group.png)

1. Hieronder wordt de groep EAAGroup genoemd, en deze heeft één lid.

    ![Schermopname van het venster GROUPS ON SUPERDEMOLIVE DIRECTORY in de Akamai EAA-console. EAAGroup met 1 gebruiker wordt vermeld onder Groups.](./media/header-akamai-tutorial/eaagroup.png)

1. Voeg de map toe aan uw id-provider door te klikken op **Identity** > **Identity Providers** en klik op het tabblad **Directories** en vervolgens op **Assign directory**.

    ![Schermopname van het tabblad Directories in de Akamai EAA-console voor Azure AD SSO, met superdemo.live in de lijst Currently assigned directories.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>KCD-overdracht configureren voor kennismaking met EAA

#### <a name="step-1-create-an-account"></a>Stap 1: Een account maken 

1. In het voorbeeld wordt een account gebruikt met de naam **EAADelegation**. U kunt dit uitvoeren met behulp van de Snappin **Active Directory: gebruikers en computers**.

    ![Schermopname van het tabblad Directories in de Akamai EAA-console voor Azure AD SSO. De map superdemo.live wordt vermeld onder Currently assigned directories.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > De gebruikersnaam moet een specifieke indeling hebben op basis van de **Identity Intercept Name**. In afbeelding 1 ziet u dat deze **corpapps.login.go.akamai-access.com** is

1. De aanmeldingsnaam van de gebruiker is:`HTTP/corpapps.login.go.akamai-access.com`

    ![Schermopname met EAADelegation Properties waarin First name is ingesteld op EAADelegation en User logon name op HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Stap 2: De SPN voor dit account configureren

1. Op basis van dit voorbeeld wordt de SPN als volgt weergegeven.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Schermopname van een Administrator: Command Prompt-scherm met de resultaten van de opdracht setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Stap 3: Delegering configureren

1. Klik voor het EAADelegation-account op het tabblad Delegation.

    ![Schermopname van een Administrator: Command Prompt-scherm met de opdracht voor het configureren van de SPN.](./media/header-akamai-tutorial/spn.png)

    * Geef een protocol voor verificatie gebruiken op.
    * Klik op Add en voeg het app-pool-account voor de Kerberos-website toe. Het moet automatisch worden omgezet naar de juiste SPN als deze correct is geconfigureerd.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Stap 4: Een Keytab-bestand maken voor AKAMAI EAA

1. Hier volgt de algemene syntaxis.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Uitleg van het voorbeeld

    | Codefragment | Uitleg |
    | - | - |
    | Ktpass /out EAADemo.keytab | // De naam van het Keytab-uitvoerbestand |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Account voor EAA delegering |
    | /pass RANDOMPASS | // Wachtwoord voor EAA-delegeringsaccount |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Raadpleeg de documentatie van Akamai EAA |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Schermopname van een Administrator: Command Prompt-scherm met de resultaten van de opdracht voor het maken van een Keytab-bestand voor AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Stap 5: Keytab importeren in de AKAMAI EAA-console

1. Klik op **System** > **Keytabs**.

    ![Schermopname van de Akamai EAA-console waarin Keytabs is geselecteerd in het menu System.](./media/header-akamai-tutorial/keytabs.png)

1. Kies voor het type Keytab **Kerberos Delegation**.

    ![Schermopname van het venster EAAKEYTAB in de Akamai EAA-console met de instellingen voor Keytab. Keytab Type is ingesteld op Kerberos Delegation.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Zorg ervoor dat de Keytab wordt weer gegeven als geïmplementeerd en geverifieerd.

    ![Schermopname van het scherm KEYTABS in de Akamai EAA-console waarin EAA Keytab als 'Keytab deployed and verified' staat vermeld.](./media/header-akamai-tutorial/keytabs-2.png)

1. Gebruikerservaring

    ![Schermopname van het dialoogvenster Aanmelden op myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Schermopname van het venster Apps voor myapps.microsoft.com waarin app-pictogrammen worden weergegeven.](./media/header-akamai-tutorial/end-user-4.png)

1. Voorwaardelijke toegang

    ![Schermopname van het bericht 'Approve sign in request'. het bericht.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Schermopname van een venster Applications met pictogrammen voor MyHeaderApp, SSH Secure, SecretRDPApp en myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Schermopname van het welkomstscherm voor myKerberosApp. Het bericht 'Welcome superdemo\user1' wordt weergegeven over een achtergrondafbeelding.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Akamai-testgebruiker maken

In dit gedeelte maakt u in Akamai een gebruiker met de naam B.Simon. Werk samen met het [ondersteuningsteam van Akamai](https://www.akamai.com/us/en/contact-us/) om de gebruikers toe te voegen in het Akamai-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Akamai waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Akamai in de mijn apps klikt, moet u automatisch worden aangemeld bij de Akamai waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Akamai hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).