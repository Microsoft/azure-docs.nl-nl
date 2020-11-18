---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Sonarqube | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Sonarqube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: 455c15ec97d5621b51a4d8af87cc3a2968dd65dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095969"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Sonarqube

In deze zelfstudie leert u hoe u Sonarqube met Azure Active Directory (Azure AD) integreert. Wanneer u Sonarqube integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Sonarqube.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Sonarqube.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Sonarqube waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sonarqube ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-sonarqube-from-the-gallery"></a>Sonarqube toevoegen vanuit de galerie

Om de integratie van Sonarqube te configureren in Azure AD, moet u Sonarqube vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Sonarqube** in het zoekvak.
1. Selecteer **Sonarqube** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Eenmalige aanmelding van Microsoft Azure Active Directory voor Sonarqube configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Sonarqube met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sonarqube.

Voer de volgende stappen uit om eenmalige aanmelding van Microsoft Azure AD bij Sonarqube te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren in Sonarqube](#configure-sonarqube-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker maken voor Sonarqube](#create-sonarqube-test-user)** : als u een equivalent van B.Simon in Sonarqube wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de integratiepagina van de app **Sonarqube**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL: 

    * **Voor een productieomgeving**

        `https://servicessonar.corp.microsoft.com/`

    * **Voor een ontwikkelomgeving**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Sonarqube instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Sonarqube.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Sonarqube** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.

1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sonarqube-sso"></a>Eenmalige aanmelding configureren voor Sonarqube

1. Open een nieuw browservenster en meld u als beheerder aan bij de bedrijfssite van Sonarqube.

1. Klik op **Beheer > Configuratie > Beveiliging** en ga naar de **SAML-invoegtoepassing** om de volgende stappen uit te voeren.

1. Kopieer de volgende gegevens uit de IdP-metagegevens en plak ze in de bijbehorende tekstvelden in de SonarQube-invoegtoepassing.
    1. IdP-entiteits-id
    2. Aanmeldings-URL
    3. X.509-certificaat 
1. Sla alle gegevens op.
    ![IDP SAML-invoegtoepassing](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Voer op de pagina **SAML** de volgende stappen uit:

    ![Configuratie van Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Zet de optie **Enabled** op **yes**.

    b. Typ in het tekstvak **Application ID** een naam, zoals **sonarqube**.

    c. Typ in het tekstvak **Provider Name** een naam, zoals **SAML**.

    d. Plak in het tekstvak **Provider ID** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **SAML login url** de waarde van **Aanmeldings-URL**, die u hebt gekopieerd uit de Azure-portal.

    f. Open het met Base64 gecodeerde certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Provider certificate**.

    g. Typ de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **SAML user login attribute**.

    h. Typ de waarde `http://schemas.microsoft.com/identity/claims/displayname` in het tekstvak **SAML user name attribute**.

    i. Typ de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **SAML user email attribute**.

    j. Klik op **Opslaan**.

### <a name="create-sonarqube-test-user"></a>Testgebruiker maken voor Sonarqube

In deze sectie maakt u een gebruiker met de naam B.Simon in Sonarqube. Neem contact op met het [ondersteuningsteam van Sonarqube](https://www.sonarsource.com/support/) om de gebruikers toe te voegen in het Sonarqube-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Sonarqube, waar u de aanmeldingsstroom kunt starten. 

2. Ga rechtstreeks naar de aanmeldings-URL van Sonarqube en start daar de aanmeldingsstroom.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in Toegangsvenster op de Sonarqube-tegel klikt, wordt u omgeleid naar de aanmeldings-URL voor Sonarqube. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

* Wanneer u Sonarqube hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor realtime beveiliging wordt geboden tegen exfiltratie en infiltratie van gevoelige gegevens van uw organisatie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
