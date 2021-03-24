---
title: 'Zelfstudie: Azure Active Directory-integratie met LaunchDarkly | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954799"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Zelfstudie: Azure Active Directory-integratie met LaunchDarkly

In deze zelf studie leert u hoe u LaunchDarkly integreert met Azure Active Directory (Azure AD). Wanneer u LaunchDarkly integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot LaunchDarkly.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij LaunchDarkly met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

    > [!NOTE]
    > De integratie van de LaunchDarkly-Azure Active Directory is in één richting. Nadat u de integratie hebt geconfigureerd, kunt u Azure AD gebruiken voor het beheren van gebruikers, SSO en accounts in LaunchDarkly, maar u **kunt LaunchDarkly niet** gebruiken voor het beheren van gebruikers, SSO en accounts in Azure.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement voor eenmalige aanmelding LaunchDarkly ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LaunchDarkly ondersteunt door **IDP** geïnitieerde SSO.
* LaunchDarkly ondersteunt **just-in-time** -gebruikers inrichting.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-launchdarkly-from-the-gallery"></a>LaunchDarkly toevoegen vanuit de galerie

Voor het configureren van de integratie van LaunchDarkly in Azure AD moet u LaunchDarkly uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **LaunchDarkly** in het zoekvak.
1. Selecteer **LaunchDarkly** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Azure AD SSO voor LaunchDarkly configureren en testen

Azure AD SSO met LaunchDarkly configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LaunchDarkly.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met LaunchDarkly:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LAUNCHDARKLY SSO configureren](#configure-launchdarkly-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een LaunchDarkly-test gebruiker](#create-launchdarkly-test-user)** -om een equivalent van B. Simon in LaunchDarkly te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **LaunchDarkly** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u deze URL: `app.launchdarkly.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. U werkt de waarde bij met de werkelijke antwoord-URL, zoals later in de zelfstudie wordt uitgelegd. Als u de toepassing wilt gebruiken in **IDP**-modus, moet u het veld **Aanmeldings-URL** leeg laten, anders kunt u het aanmelding niet initiëren vanuit de **IDP**. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In het gedeelte **LaunchDarkly instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan LaunchDarkly.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LaunchDarkly** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-launchdarkly-sso"></a>LaunchDarkly SSO configureren

1. Meld u in een ander webbrowservenster bij uw LaunchDarkly-bedrijfssite aan als beheerder.

2. Selecteer **Accountinstellingen** in het linkernavigatievenster.

    ![Schermopname met het item Accountinstellingen geselecteerd in Productie.](./media/launchdarkly-tutorial/configure-1.png)

3. Klik op het tabblad **Beveiliging**.

    ![Schermopname van het tabblad Beveiliging van Accountinstellingen.](./media/launchdarkly-tutorial/configure-2.png)

4. Klik op **EENMALIGE AANMELDING INSCHAKELEN** en vervolgens **SAML-CONFIGURATIE BEWERKEN**.

    ![Schermopname van de pagina voor eenmalige aanmelding waar u S S O kunt inschakelen en de SAML-configuratie kunt bewerken.](./media/launchdarkly-tutorial/configure-3.png)

5. In het gedeelte **Uw SAML-configuratie bewerken** voert u de volgende stappen uit:

    ![Schermopname van de sectie Uw SAML-configuratie bewerken, waar u de hier beschreven wijzigingen kunt aanbrengen.](./media/launchdarkly-tutorial/configure-4.png)

    a. Kopieer de **SAML-consumptieservice-URL** voor uw instantie en plak deze in het tekstvak Antwoord-URL in het gedeelte **Domein en URL's voor LaunchDarkly** van Azure Portal.

    b. Plak in het tekstvak voor de **URL voor eenmalige aanmelding** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    c. Open het van de Azure-portal gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het vak **X.509-certificaat**. U kunt het certificaat ook rechtstreeks uploaden door op **een uploaden** te klikken.

    d. Klik op **Opslaan**.

### <a name="create-launchdarkly-test-user"></a>Een testgebruiker maken in LaunchDarkly

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in LaunchDarkly. LaunchDarkly biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Deze sectie bevat geen actie-item voor u. Als een gebruiker nog niet bestaat in LaunchDarkly, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de LaunchDarkly waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel LaunchDarkly in de mijn apps klikt, moet u automatisch worden aangemeld bij de LaunchDarkly waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u LaunchDarkly hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).