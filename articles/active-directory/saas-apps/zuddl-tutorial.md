---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Zuddl | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zuddl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 80e4de168bea5a60b0c3a30d06239af5971c0aa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654290"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zuddl"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Zuddl

In deze zelf studie leert u hoe u Zuddl integreert met Azure Active Directory (Azure AD). Wanneer u Zuddl integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Zuddl.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Zuddl met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Zuddl-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zuddl ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-zuddl-from-the-gallery"></a>Zuddl toevoegen uit de galerie

Als u de integratie van Zuddl in azure AD wilt configureren, moet u Zuddl uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Zuddl** in het zoekvak.
1. Selecteer **Zuddl** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-zuddl"></a>Azure AD SSO voor Zuddl configureren en testen

Azure AD SSO met Zuddl configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zuddl.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Zuddl:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ZUDDL SSO configureren](#configure-zuddl-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Zuddl-test gebruiker](#create-zuddl-test-user)** -om een equivalent van B. Simon in Zuddl te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Zuddl** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://app.zuddl.com/<CUSTOM_URL>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://auth.zuddl.com/<CUSTOM_ID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://auth.workos.com/sso/saml/acs/<CUSTOM_ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteunings team van Zuddl-clients](mailto:support@zuddl.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Zuddl instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zuddl.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Zuddl**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zuddl-sso"></a>Zuddl SSO configureren

Als u eenmalige aanmelding wilt configureren op **Zuddl** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [Zuddl ondersteunings team](mailto:support@zuddl.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-zuddl-test-user"></a>Zuddl-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Zuddl. Werk samen met [Zuddl-ondersteunings team](mailto:support@zuddl.com) om de gebruikers toe te voegen in het Zuddl-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Zuddl-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Zuddl-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Zuddl in de mijn apps klikt, wordt dit omgeleid naar de Zuddl-aanmeldings-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u Zuddl hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).