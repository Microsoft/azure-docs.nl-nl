---
title: 'Zelfstudie: Azure Active Directory-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Five9 Plus Adapter (CTI, contactcentermedewerkers).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218716"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Zelfstudie: Azure Active Directory-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers)

In deze zelf studie leert u hoe u Five9 plus-adapter (CTI, contact center-agents) integreert met Azure Active Directory (Azure AD). Wanneer u Five9 plus-adapter (CTI, contact center-agents) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Five9 plus-adapter (CTI, contact center-agents).
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Five9 plus-adapter (CTI, contact center-agents) met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Five9 plus-adapter (CTI, contact center-agents) ingeschakeld abonnement voor eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Five9 plus-adapter (CTI, contact center-agents) ondersteunt door **IDP** geïnitieerde SSO.

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Voeg Five9 plus-adapter (CTI, contact center-agents) toe vanuit de galerie

Om de integratie van Five9 Plus Adapter (CTI, contactcentermedewerkers) te configureren in Azure AD, moet u Five9 Plus Adapter (CTI, contactcentermedewerkers) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Five9 plus adapter (CTI, contact center-agents)** in het zoekvak.
1. Selecteer **Five9 plus adapter (CTI, contact met Center-agents)** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Azure AD SSO configureren en testen voor Five9 plus-adapter (CTI, contact center-agents)

Configureer en test Azure AD SSO met Five9 plus-adapter (CTI, contact center-agents) met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Five9 plus-adapter (CTI, contact center-agents).

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Five9 plus-adapter (CTI, contact center-agents):

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer de Five9 plus-adapter (CTI, contact center-agents) SSO](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een Five9 plus-adapter (CTI, contact center-agents) test gebruiker](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** : als u een equivalent van B. Simon in Five9 plus adapter (CTI, contact center-agents) wilt maken die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina voor de integratie van de **Five9 plus-adapter (CTI, contact center-agents)** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een van de volgende URL's:
    
    |    Omgeving      |       URL      |
    | :-- | :-- |
    | Voor ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’ | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Voor ‘Five9 Plus Adapter voor Zendesk’ | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Voor ‘Five9 Plus Adapter voor Agent Desktop Toolkit’ | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. In het tekstvak **Antwoord-URL** typt u een van de volgende URL's:

    |      Omgeving     |      URL      |
    | :--                  | :--           |
    | Voor ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’ | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Voor ‘Five9 Plus Adapter voor Zendesk’ | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Voor ‘Five9 Plus Adapter voor Agent Desktop Toolkit’ | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in de sectie **Five9 Plus Adapter (CTI, contactcentermedewerkers)** de juiste URL(‘s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Five9 plus-adapter (CTI, contact center-agents).

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Five9 Plus Adapter (CTI, contactcentermedewerkers)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Five9 plus-adapter (CTI, contact center-agents) configureren SSO

1. Als u eenmalige aanmelding aan de zijde van **Five9 Plus Adapter (CTI, contactcentermedewerkers)** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste gekopieerde URL(‘s) naar het [ondersteuningsteam van Five9 Plus Adapter (CTI, contactcentermedewerkers)](https://www.five9.com/about/contact) verzenden. Om eenmalige aanmelding verder te configureren, moet u daarnaast ook de onderstaande stappen volgen, afhankelijk van de adapter:

    a. Beheerdershandleiding ‘Five9 Plus Adapter voor Agent Desktop Toolkit’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Beheerdershandleiding ‘Five9 Plus Adapter voor Microsoft Dynamics CRM’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Beheerdershandleiding ‘Five9 Plus Adapter voor Zendesk’: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Een testgebruiker voor Five9 Plus Adapter (CTI, contactcentermedewerkers) maken

In deze sectie gaat u een gebruiker maken met de naam Britta Simon in Five9 Plus Adapter (CTI, contactcentermedewerkers). Werk samen met het [ondersteuningsteam van Five9 Plus Adapter (CTI, contactcentermedewerkers)](https://www.five9.com/about/contact) om gebruikers toe te voegen op het Five9 Plus Adapter (CTI, contactcentermedewerkers)-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op test deze toepassing in Azure Portal en u moet automatisch worden aangemeld bij de Five9 plus-adapter (CTI, contact center-agents) waarvoor u de SSO hebt ingesteld.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Five9 plus adapter (CTI, contact center-agents) in de mijn apps klikt, moet u automatisch worden aangemeld bij de Five9 plus-adapter (CTI, contact center-agents) waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de Five9 plus-adapter (CTI, contact center-agents) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
