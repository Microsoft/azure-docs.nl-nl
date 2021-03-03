---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het beheer van Parkalot-auto Park | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Parkalot-Park beheer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651407"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het beheer van Parkalot-auto Park

In deze zelf studie leert u hoe u beheer van Parkalot-Car Park integreert met Azure Active Directory (Azure AD). Wanneer u Parkalot-Car Park integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het beheer van Parkalot-auto Park.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Parkalot met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Parkalot-het abonnement voor eenmalige aanmelding (SSO) voor het beheer van de open Park.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Parkalot-auto Park-beheer ondersteunt door **SP** GEÏNITIEERDe SSO

* Parkalot-auto Park-beheer ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Beheer van Parkalot-auto Park toevoegen vanuit de galerie

Als u de integratie van Parkalot-auto Park-beheer wilt configureren in azure AD, moet u Parkalot van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Parkalot-auto Park Management** in het zoekvak.
1. Selecteer **Parkalot-auto Park Management** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Azure AD SSO configureren en testen voor Parkalot-auto Park Management

Configureer en test Azure AD SSO met Parkalot-auto Park Management met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Parkalot-auto Park Management.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Parkalot-auto Park Management:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Parkalot-Car-SSO van Park Management configureren](#configure-parkalot-car-park-management-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Parkalot-Car test gebruiker](#create-parkalot-car-park-management-test-user)** voor het Park-beheer, zodat u een tegen hanger hebt van B. Simon in Parkalot-auto Park Management dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Parkalot-auto Park Management** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met één van de volgende patronen:

    | Id (Entiteits-id) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. In het tekstvak **Antwoord-URL** typt u een URL met één van de volgende patronen:

    | Antwoord-URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. In het tekstvak **Aanmeldings-URL** typt u een URL met een van de volgende notaties:

    | Aanmeldings-URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Parkalot-Car Park Management-client](mailto:contact-us@parkalot.io) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op het gedeelte **beheer van Parkalot-Car Park instellen** .

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het beheer van Parkalot-auto Park.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Parkalot-auto Park Management**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-parkalot-car-park-management-sso"></a>Parkalot-Car-SSO voor Park-beheer configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de Parkalot-site voor het beheer van het park van uw organisatie.

1. Selecteer **Setup SAML** en klik op het pictogram **bewerken** op **nieuwe kaart toevoegen** .

    ![Nieuw bewerkings pictogram toevoegen.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Voer de onderstaande stappen uit op de volgende pagina.

    ![Configureer SSO voor het beheer van Parkalot-Car Park.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Geef in het tekstvak **weergave naam** een geldige naam op.

    b. Plak in het tekstvak **IDP entiteit-id** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **SSO-URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    d. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok en plak de inhoud in het tekstvak **Certificaat**.

    e. Klik op **OPSLAAN**.

### <a name="create-parkalot-car-park-management-test-user"></a>Test gebruiker voor Park Management maken Parkalot-Car

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Parkalot-Car Park Management. Parkalot-Car Park biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in het Parkalot-auto Park-beheer, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL voor Parkalot-auto Park-beheer, waar u de aanmeldings stroom kunt initiëren. 

* Ga naar de aanmeldings-URL voor Parkalot-auto Park Management en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel beheer van Parkalot-auto Park in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van Parkalot-auto Park-beheer. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Nadat u Parkalot hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).