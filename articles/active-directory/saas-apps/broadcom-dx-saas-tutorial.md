---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Broadcom DX SaaS | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 5aa13598133b55547484609fc6d26d9375c04045
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055431"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Broadcom DX SaaS

In deze zelf studie leert u hoe u Broadcom DX SaaS integreert met Azure Active Directory (Azure AD). Wanneer u Broadcom DX SaaS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Broadcom DX SaaS.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Broadcom DX SaaS met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Broadcom DX SaaS-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Broadcom DX SaaS ondersteunt **IDP** geïnitieerde SSO.

* Broadcom DX SaaS ondersteunt **just-in-time** -gebruikers inrichting.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Broadcom DX SaaS toevoegen vanuit de galerie

Als u de integratie van Broadcom DX SaaS wilt configureren in azure AD, moet u Broadcom DX SaaS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Broadcom DX SaaS** in het zoekvak.
1. Selecteer **Broadcom DX SaaS** van het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Azure AD SSO configureren en testen voor Broadcom DX SaaS

Azure AD SSO configureren en testen met Broadcom DX SaaS met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Broadcom DX SaaS.

Als u Azure AD SSO wilt configureren en testen met Broadcom DX SaaS, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Broadcom DX SaaS SSO configureren](#configure-broadcom-dx-saas-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Broadcom DX SaaS-test gebruiker](#create-broadcom-dx-saas-test-user)** -om een tegen hanger te hebben van B. Simon in Broadcom DX SaaS die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Broadcom DX SaaS** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `DXI_<TENANT_NAME>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team voor Broadcom DX SaaS-client](mailto:dxi-na1@saas.broadcom.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Broadcom DX SaaS-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Naast de bovenstaande stappen verwacht de Broadcom DX SaaS-toepassing nog enkele kenmerken die worden door gegeven aan het SAML-antwoord. deze worden hieronder weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | --------------- | --------- |
    | Groep | user.groups |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Broadcom DX SaaS instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Broadcom DX SaaS.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Broadcom DX SaaS**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-broadcom-dx-saas-sso"></a>Broadcom DX SaaS SSO configureren

1. Meld u aan bij uw site met uw Broadcom DX SaaS-bedrijfs netwerk als beheerder.

2. Ga naar de **instellingen** en klik op het tabblad **gebruikers** .

    ![Gebruikers](./media/broadcom-dx-saas-tutorial/settings.png "Gebruikers")

3. Klik in de sectie **gebruikers beheer** op weglatings tekens en selecteer **SAML**.

    ![Gebruikersbeheer](./media/broadcom-dx-saas-tutorial/local.png "Gebruikersbeheer")

4. Voer de volgende stappen uit in de sectie **SAML-account identificeren** .
   
    ![Account](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Account") 

    a. Plak in het tekstvak **Verlener** de waarde van de **Azure AD-id** die u hebt gekopieerd in de Azure Portal.

    b. Plak in het tekstvak **ID-provider (IDP) aanmeldings-URL** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak voor de **Afmeldings-URL van de identiteits provider (IDP)** de waarde voor de **afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    d. Open het gedownloade **certificaat (base64)** van de Azure Portal in Klad blok en plak de inhoud in het tekstvak **ID-provider certificaat** .

    e. Klik op **volgende**.

5. In de sectie **kenmerken toewijzen** vult u de vereiste SAML-kenmerken in op de volgende pagina en klikt u op **volgende**.

    ![Kenmerken](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Kenmerken") 


6. Voer in de sectie **gebruikers groep identificeren** de object-id van die groep in en klik op **volgende**.

    ![Groep toevoegen](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Groep toevoegen") 

7. Controleer in de sectie **SAML-account vullen** de gevulde waarden en klik op **Opslaan**.

    ![SAML-account](./media/broadcom-dx-saas-tutorial/broadcom-4.png "SAML-account") 

### <a name="create-broadcom-dx-saas-test-user"></a>Een Broadcom DX SaaS-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Broadcom DX SaaS. Broadcom DX SaaS ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Broadcom DX SaaS, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Broadcom DX SaaS waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Broadcom DX SaaS-tegel in de mijn apps klikt, moet u automatisch worden aangemeld bij de Broadcom DX SaaS waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Broadcom DX SaaS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
