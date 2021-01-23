---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met JIRA SAML SSO by Microsoft | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JIRA SAML SSO by Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 75b8230a1027bbf3ff3d73fb35ce65107c2db7e9
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Confluence SAML SSO by Microsoft

In deze zelfstudie leert u hoe u JIRA SAML SSO kunt integreren met Azure Active Directory (Azure AD). Wanneer u JIRA SAML SSO by Microsoft integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot JIRA SAML SSO by Microsoft.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij JIRA SAML SSO by Microsoft.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="description"></a>Beschrijving

Gebruik uw Microsoft Azure Active Directory-account met JIRA Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen alle gebruikers in uw organisatie zich met hun Azure AD-referenties aanmelden bij de JIRA-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met JIRA SAML SSO by Microsoft hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
- JIRA Core and Software 6.4 tot 8.14.0 of JIRA Service Desk 3.0 tot 4.11.1 moeten zijn geïnstalleerd en geconfigureerd op een 64-bits versie van Windows
- JIRA-server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de JIRA-invoegtoepassing.
- De JIRA-server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet het token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in JIRA
- WebSudo is uitgeschakeld in JIRA
- Testgebruiker gemaakt in de JIRA-servertoepassing

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, raden wij u aan niet een productieomgeving van JIRA te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

U hebt het volgende nodig om aan de slag te gaan:

* Gebruik niet de productieomgeving, tenzij dit echt nodig is.
* Een abonnement op JIRA SAML SSO by Microsoft waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="supported-versions-of-jira"></a>Ondersteunde versies van JIRA

* JIRA Core en Software: 6.4 tot 8.14.0
* JIRA Service Desk 3.0.0 tot 4.11.1
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Houd er rekening mee dat onze JIRA-invoegtoepassing ook werkt op Ubuntu versie 16.04 en Linux.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JIRA SAML SSO by Microsoft ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>JIRA SAML SSO by Microsoft toevoegen vanuit de galerie

Voor het configureren van de integratie van JIRA SAML SSO by Microsoft in Azure AD, moet u JIRA SAML SSO by Microsoft uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **JIRA SAML SSO by Microsoft** in het zoekvak.
1. Selecteer **JIRA SAML SSO by Microsoft** vanuit het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Eenmalige aanmelding van Azure AD voor JIRA SAML SSO by Microsoft configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met JIRA SAML SSO by Microsoft met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in JIRA SAML SSO by Microsoft.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met JIRA SAML SSO by Microsoft te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor JIRA SAML SSO by Microsoft configureren](#configure-jira-saml-sso-by-microsoft-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Een testgebruiker maken voor JIRA SAML SSO by Microsoft](#create-jira-saml-sso-by-microsoft-test-user)** : als u een tegenhanger van B.Simon in JIRA SAML SSO by Microsoft wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **JIRA SAML SSO by Microsoft** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van de JIRA-invoegtoepassing, wat later in de zelfstudie wordt uitgelegd.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In dit gedeelte geeft u B.Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot JIRA SAML SSO by Microsoft.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JIRA SAML SSO by Microsoft** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Eenmalige aanmelding voor JIRA SAML SSO by Microsoft configureren

1. Meld u in een ander browservenster als beheerder aan bij uw JIRA-instantie.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Schermopname met Add-ons (Invoegtoepassingen) geselecteerd in het menu Instellingen.](./media/jiramicrosoft-tutorial/addon1.png)

3. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56506). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Schermopname van Manage add-ons (Invoegtoepassingen beheren) met de koppeling Upload add-on (Invoegtoepassing uploaden) uitgelicht.](./media/jiramicrosoft-tutorial/addon12.png)

4. Voer de volgende stappen uit voor het uitvoeren van het omgekeerde-proxyscenario of load balancer-scenario van JIRA:

    > [!NOTE]
    > U moet de server eerst configureren met de onderstaande instructies en vervolgens de invoegtoepassing installeren.

    a. Voeg het onderstaande kenmerk toe in de **connector**-poort in het bestand **server.xml** van de JIRA-servertoepassing.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Schermopname van het bestand server.xml in een editor met de nieuwe toegevoegde regel.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Wijzig **Base URL** in **System Settings** overeenkomstig de proxy/load balancer.

    ![Schermopname van de beheerinstellingen waar u de basis-URL kunt wijzigen.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven bij **User Installed** in de sectie **Manage add-ons**. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Schermopname van de sectie SAML Single Sign-on for Jira van Azure AD met Configure geselecteerd.](./media/jiramicrosoft-tutorial/addon14.png)

6. Voer de volgende stappen uit op de configuratiepagina:

    ![Schermopname van de configuratiepagina voor eenmalige aanmelding van Microsoft Azure Active Directory voor Jira.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    a. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    b. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Domein- en URL-gegevens voor JIRA SAML SSO** in de Azure-portal.

    c. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.
    
    d. Typ bij **Beschrijving knop voor Aanmelden** de beschrijving van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.

    e. Selecteer bij **SAML User ID Locations** het keuzerondje **User ID is in the NameIdentifier element of the Subject statement** of **User ID is in an Attribute element**.  Deze id moet de gebruikers-id van JIRA zijn. Als de gebruikers-id niet overeenkomt, kunnen gebruikers zich niet aanmelden.

    > [!Note]
    > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    f. Als u de optie **User ID is in an Attribute element** selecteert, typt u in het tekstvak **Attribute Name** de naam van het kenmerk waaruit de gebruikers-id moet worden opgehaald.

    g. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    h. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    i. Selecteer **Eenmalige afmelding inschakelen** als een gebruiker moet worden afgemeld bij Azure AD op het moment dat hij of zij zich afmeldt bij JIRA.
    
    j. Schakel het selectievakje **Aanmelding bij Azure afdwingen** in als u zich alleen wilt aanmelden via Azure AD-referenties.
    
    > [!Note]
    > Als het selectievakje Aanmelding bij Azure afdwingen is ingeschakeld en u het standaardaanmeldingsformulier wilt inschakelen voor de aanmelding van beheerders op de aanmeldingspagina, voegt u de queryparameter toe aan de browser-URL.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. Klik op **Save** om de instellingen op te slaan.

    > [!NOTE]
    > Raadpleeg de [beheerdershandleiding voor MS JIRA SSO Connector](./ms-confluence-jira-plugin-adminguide.md) voor meer informatie over de installatie en probleemoplossing. U kunt ook [Veelgestelde vragen](./ms-confluence-jira-plugin-adminguide.md) bezoeken voor hulp.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Een testgebruiker maken in JIRA SAML SSO by Microsoft

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij de on-premises server van JIRA, moeten ze worden ingericht in JIRA SAML SSO by Microsoft. In het geval van JIRA SAML SSO by Microsoft is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de on-premises server van JIRA.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Schermopname met User management (Gebruikersbeheer) geselecteerd in het menu Instellingen.](./media/jiramicrosoft-tutorial/user1.png)

3. U wordt omgeleid naar de toegangspagina voor beheerders. Voer uw **wachtwoord** in en klik op de knop **Bevestigen**.

    ![Schermopname van de toegangspagina voor beheerders waar u uw referenties invoert.](./media/jiramicrosoft-tutorial/user2.png)

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Schermopname van het tabblad Gebruikersbeheer met de optie Gebruiker maken.](./media/jiramicrosoft-tutorial/user3.png) 

5. Op de pagina **Nieuwe gebruiker maken** voert u de volgende stappen uit:

    ![Schermopname met het dialoogvenster Nieuwe gebruiker maken waar u de gegevens voor deze stap kunt invoeren.](./media/jiramicrosoft-tutorial/user4.png) 

    a. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld B.simon@contoso.com.

    b. In het tekstvak **Volledige naam** typt u de volledige naam van de gebruiker, bijvoorbeeld B.Simon.

    c. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld B.simon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Klik op **Gebruiker maken**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van JIRA SAML SSO by Microsoft, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van JIRA SAML SSO by Microsoft en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel JIRA SAML SSO by Microsoft klikt, wordt u omgeleid naar de aanmeldings-URL van JIRA SAML SSO by Microsoft. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u JIRA SAML SSO by Microsoft hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)