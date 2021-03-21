---
title: 'Zelfstudie: Integratie van Azure Active Directory met Ivanti Service Manager (ISM) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Ivanti Service Manager (ISM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 16aceeddcba574953ac03ccf04d099762b0000c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648880"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Zelfstudie: Integratie van Azure Active Directory met Ivanti Service Manager (ISM)

In deze zelf studie leert u hoe u Ivanti Service Manager (ISM) integreert met Azure Active Directory (Azure AD). Wanneer u Ivanti Service Manager (ISM) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Ivanti-Service Manager (ISM).
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor Ivanti Service Manager (ISM) met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Ivanti Service Manager (ISM) eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ivanti Service Manager (ISM) biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* Ivanti Service Manager (ISM) biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="add-ivanti-service-manager-ism-from-the-gallery"></a>Ivanti Service Manager (ISM) toevoegen vanuit de galerie

Om de integratie van Ivanti Service Manager (ISM) te configureren in Azure AD, moet u Ivanti Service Manager (ISM) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **IVANTI Service Manager (ISM)** in het zoekvak.
1. Selecteer **Ivanti Service Manager (ISM)** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-ivanti-service-manager-ism"></a>Azure AD SSO configureren en testen voor Ivanti Service Manager (ISM)

Azure AD SSO met Ivanti Service Manager (ISM) configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Ivanti Service Manager (ISM).

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Ivanti Service Manager (ISM):

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Ivanti Service Manager (ISM) SSO configureren](#configure-ivanti-service-manager-ism-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Ivanti Service Manager (ISM) test gebruiker](#create-ivanti-service-manager-ism-test-user)** : als u een equivalent van B. Simon in Ivanti Service Manager (ISM) wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Ivanti Service Manager (ISM)** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met een van de volgende notaties:
    
    ```http
    https://<customer>.saasit.com/
    https://<customer>.saasiteu.com/
    https://<customer>.saasitau.com/
    ```

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

7. Kopieer in het gedeelte **Ivanti Service Manager (ISM) instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Ivanti Service Manager (ISM).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Ivanti Service Manager (ISM)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ivanti-service-manager-ism-sso"></a>Ivanti Service Manager-SSO (ISM) configureren

Als u eenmalige aanmelding van **Ivanti Service Manager** wilt configureren, moet u het gedownloade **Certificaat (Raw)** en de correcte, uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Ivanti Service Manager](https://www.ivanti.com/support/contact). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Testgebruiker maken voor Ivanti Service Manager (ISM)

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Ivanti Service Manager. Ivanti Service Manager biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er geen gebruiker bestaat in Ivanti Service Manager, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Neem contact op met het [ondersteuningsteam van Ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Hiermee wordt omgeleid naar de URL voor Ivanti Service Manager (ISM), waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van de Ivanti-Service Manager (ISM) en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Ivanti Service Manager (ISM) waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Ivanti Service Manager (ISM) in de groep mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Ivanti Service Manager (ISM) waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Ivanti Service Manager (ISM) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).