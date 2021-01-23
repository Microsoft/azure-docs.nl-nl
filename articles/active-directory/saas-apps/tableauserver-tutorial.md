---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Server | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Tableau Server kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 3c9d79ef4fd73adbe3ba376f1723693ea8e85197
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Server

In deze zelfstudie leert u hoe u Tableau Server kunt integreren met Azure AD (Active Directory). Wanneer u Tableau Server integreert met Azure AD, kunt u:

* In Azure AD de toegang beheren tot Tableau Server.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Tableau Server.
* Uw accounts op één centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Ingeschakeld Tableau Server-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau Server ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-tableau-server-from-the-gallery"></a>Tableau Server toevoegen vanuit de galerie

Voor het configureren van de integratie van Tableau Server met Azure AD moet u Tableau Server uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Tableau Server** in het zoekvak.
1. Selecteer **Tableau Server** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Eenmalige aanmelding van Azure AD voor Tableau Server configureren en testen

Configureer en test Azure AD-eenmalige aanmelding met Tableau Server met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Tableau Server.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Tableau Server te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Tableau Server configureren](#configure-tableau-server-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Tableau Server-testgebruiker maken](#create-tableau-server-test-user)** : als tegenhanger van B.Simon in Tableau Server die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Tableau Server** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > De bovenstaande waarden zijn geen echte waarden. Werk de waarden bij met de werkelijke URL en id van de Tableau Server-configuratiepagina die verderop in de zelfstudie wordt uitgelegd.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Tableau Server instellen** de juiste URL(‘s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Tableau Server.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Tableau Server** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-server-sso"></a>Eenmalige aanmelding van Tableau Server configureren

1. Als u wilt dat eenmalige aanmelding voor uw toepassing wordt geconfigureerd, moet u zich bij uw Tableau Server-tenant aanmelden als beheerder.

2. Selecteer op het tabblad **CONFIGURATION** **Gebruikers-id en -toegang** en selecteer vervolgens het tabblad **Verificatiemethode**.

    ![Schermopname van Verificatie geselecteerd in Gebruikers-id en toegang.](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Voer de volgende stappen uit op de pagina **CONFIGURATION**:

    ![Schermopname van de configuratiepagina waarin u de beschreven waarden kunt invoeren.](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Selecteer SAML bij **Verificatiemethode**.

    b. Schakel het selectievakje in bij **SAML-verificatie inschakelen voor de server**.

    c. Retour-URL Tableau Server: de URL waarmee Tableau Server-gebruikers toegang krijgen, zoals `http://tableau_server`. Het gebruik van `http://localhost` is niet aanbevolen. Het gebruik van een URL met een afsluitende slash (bijvoorbeeld `http://tableau_server/`) wordt niet ondersteund. Kopieer de **Retour-URL van Tableau Server** en plak deze in het tekstvak **URL voor aanmelden** in de sectie **Standaard SAML-configuratie** in Azure Portal

    d. SAML-entiteit-id: de entiteit-id is een unieke aanduiding voor uw Tableau Server-installatie voor de IdP. U kunt de Tableau Server-URL hier opnieuw opgeven, maar dit hoeft niet uw Tableau Server-URL te zijn. Kopieer de **SAML-entiteit-id** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in Azure Portal

    e. Klik op de optie **XML-metagegevensbestand downloaden** en open het in de teksteditortoepassing. Zoek de URL voor Assertion Consumer Service met HTTP POST en index 0 en kopieer de URL. Plak deze nu in het tekstvak **antwoord-URL** in de sectie **Standaard SAML-configuratie** in Azure Portal

    f. Zoek het bestand met federatieve metagegevens op dat is gedownload van Azure Portal, en upload het in het bestand met het **metagegevensbestand van de SAML-Idp**.

    g. Voer de namen in voor de kenmerken die de IdP gebruikt om de gebruikersnamen, weergavenamen en e-mailadressen op te slaan.

    h. Klik op **Opslaan**.

    > [!NOTE]
    > De klant moet een PEM gecodeerd x509-certificaatbestand uploaden met de extensie CRT en een met de RSA-privésleutelbestand, of een DSA-privésleutelbestand met de extensie KEY als certificaatsleutelbestand. Raadpleeg [dit](https://help.tableau.com/current/server/en-us/saml_requ.htm) document voor meer informatie over het certificaatbestand en het certificaatsleutelbestand. Als u hulp nodig hebt bij het configureren van SAML op Tableau Server, raadpleegt u dit artikel [Serverbrede SAML configureren](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Een testgebruiker voor Tableau Server maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in Tableau Server. U moet alle gebruikers in Tableau Server inrichten.

De gebruikersnaam van de gebruiker moet overeenkomen met de waarde die u hebt geconfigureerd in het aangepaste Azure AD-kenmerk van **gebruikersnaam**. Met de juiste toewijzing moet de integratie werken met het configureren van Azure AD-eenmalige aanmelding.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, moet u contact opnemen met de beheerder van Tableau Server in uw organisatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Tableau Server, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Tableau Server en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Tableau Server klikt, wordt u omgeleid naar de aanmeldings-URL van Tableau Server. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Tableau Server hebt geconfigureerd, kunt u sessiebesturingselementen afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)