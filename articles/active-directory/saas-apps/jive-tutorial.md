---
title: 'Zelfstudie: Azure Active Directory-integratie met Jive | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 6f2d151ac5110a504d17e1c9e2835a339f31da8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581776"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Jive

In deze zelfstudie leert u hoe u Jive integreert met Azure AD (Azure Active Directory). Wanneer u Jive integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot Jive.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Jive.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Jive-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Jive ondersteunt door **SP** geïnitieerde SSO.
* Jive ondersteunt [ **geautomatiseerde** gebruikers inrichting](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Jive toevoegen vanuit de galerie

Als u de integratie van Jive in Azure AD wilt configureren, moet u Jive vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Jive** in het zoekvak van de sectie **Toevoegen uit de galerie**.
1. Selecteer **Jive** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Azure AD SSO voor Jive configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Jive met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jive.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Jive:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Jive configureren](#configure-jive-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Jive-testgebruiker maken](#create-jive-test-user)** : om een tegenhanger voor B.Simon in Jive te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Jive** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

   a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instance name>.jivecustom.com`

   b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantenondersteuningsteam van Jive](https://www.jivesoftware.com/services-support/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Jive instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Jive.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Jive** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jive-sso"></a>Eenmalige aanmelding bij Jive configureren

1. Als u eenmalige aanmelding aan de **Jive**-zijde wilt configureren, moet u zich als beheerder aanmelden bij de Jive-tenant.

1. Klik in het menu bovenaan op **SAML**.

    ![Schermopname van het tabblad SAML met Ingeschakeld geselecteerd.](./media/jive-tutorial/jive-2.png)

    a. Selecteer op het tabblad **Algemeen** de optie **Ingeschakeld**.

    b. Klik op de knop **ALLE SAML-INSTELLINGEN OPSLAAN**.

1. Ga naar het tabblad **IDP-METAGEGEVENS**.

    ![Schermopname van het geselecteerde SAML-tabblad IDP-METAGEGEVENS.](./media/jive-tutorial/jive-3.png)

    a. Kopieer de inhoud van het gedownloade XML-bestand met metagegevens, en plak deze vervolgens in het tekstvak **Uw IDP-metagegevens (Identity Provider)** .

    b. Klik op de knop **ALLE SAML-INSTELLINGEN OPSLAAN**.

1. Selecteer het tabblad **TOEWIJZING VAN GEBRUIKERSKENMERK**.

    ![Schermopname van het SAML-tabblad met TOEWIJZING VAN GEBRUIKERSKENMERK geselecteerd.](./media/jive-tutorial/jive-4.png)

    a. Kopieer en plak de kenmerknaam van de waarde **mail** in het tekstvak **E-mail**.

    b. Kopieer en plak de kenmerknaam van de waarde **givenname** in het tekstvak **Voornaam**.

    c. Kopieer en plak de kenmerknaam van de waarde **surname** in het tekstvak **Achternaam**.

### <a name="create-jive-test-user"></a>Jive-testgebruiker maken

Het doel van deze sectie is om in Jive een gebruiker met de naam Britta Simon te maken. Jive biedt ondersteuning voor het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](jive-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

Neem contact op met het [ondersteuningsteam van Jive](https://www.jivesoftware.com/services-support/) als u handmatig een gebruiker wilt maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Jive-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Jive-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Jive in de mijn apps klikt, wordt dit omgeleid naar de Jive-aanmeldings-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Jive hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).