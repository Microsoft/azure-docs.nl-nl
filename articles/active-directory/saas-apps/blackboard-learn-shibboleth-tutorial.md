---
title: 'Zelfstudie: Azure Active Directory-integratie met Blackboard Learn - Shibboleth| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Blackboard Learn - Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649889"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Zelfstudie: Azure Active Directory-integratie met Blackboard Learn - Shibboleth

In deze zelf studie leert u hoe u school bord shibboleth kunt integreren met Azure Active Directory (Azure AD). Wanneer u school bord leren-shibboleth met Azure AD integreert, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot school bord informatie-shibboleth.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij school-shibboleth met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een school bord meer informatie-shibboleth single sign-on (SSO)-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Blackboard Learn - Shibboleth ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>School bord-informatie toevoegen-shibboleth uit de galerie

Voor het configureren van de integratie van Blackboard Learn - Shibboleth in Azure AD, moet u Blackboard Learn - Shibboleth vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** **school bord meer informatie-shibboleth** in het zoekvak.
1. Selecteer **school bord meer informatie-shibboleth** uit het paneel resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Meer informatie over Azure AD SSO voor school bord configureren en testen-shibboleth

Azure AD SSO met school bord-shibboleth configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in school leer meer informatie-shibboleth.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met school bord Learn-shibboleth:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[School bord leren-SHIBBOLETH SSO configureren](#configure-blackboard-learn---shibboleth-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Meer informatie over school bord maken-shibboleth test User](#create-blackboard-learn---shibboleth-test-user)** : als u wilt dat er een tegen hanger is van B. Simon in school leer leren-shibboleth die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Blackboard Learn - Shibboleth moet u de volgende stappen uitvoeren:

1. Selecteer in de Azure Portal op de pagina **school leer-shibboleth** Application Integration de optie **eenmalige aanmelding**.

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het potloodpictogram om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Blackboard Learn - Shibboleth instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan school leren-shibboleth.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Blackboard Learn - Shibboleth** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-blackboard-learn---shibboleth-sso"></a>School bord-informatie configureren-shibboleth SSO

Als u eenmalige aanmelding aan de zijde van **Blackboard Learn - Shibboleth** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Testgebruiker maken in Blackboard Learn - Shibboleth

In deze sectie gaat u in Blackboard Learn - Shibboleth een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) om de gebruikers toe te voegen in het Blackboard Learn - Shibboleth-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar school leer informatie-shibboleth-aanmeld-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar school leer informatie-shibboleth sign-on URL en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel school leer-shibboleth in de mijn apps klikt, moet u automatisch worden aangemeld bij het school bord Learn-shibboleth waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u school bord meer hebt geconfigureerd-shibboleth kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).