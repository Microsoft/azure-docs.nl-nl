---
title: 'Zelfstudie: Azure Active Directory-integratie met FreshDesk | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en FreshDesk configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651901"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Zelfstudie: Azure Active Directory-integratie met FreshDesk

In deze zelf studie leert u hoe u FreshDesk integreert met Azure Active Directory (Azure AD). Wanneer u FreshDesk integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot FreshDesk.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij FreshDesk met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een FreshDesk-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FreshDesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="add-freshdesk-from-the-gallery"></a>FreshDesk toevoegen vanuit de galerie

Om de integratie van FreshDesk te configureren in Azure AD, moet u FreshDesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **FreshDesk**.
1. Selecteer **FreshDesk** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Eenmalige aanmelding van Azure AD voor FreshDesk configureren en testen

Configureer en test eenmalige aanmelding bij Azure AD met FreshDesk met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in FreshDesk.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met FreshDesk:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[FRESHDESK SSO configureren](#configure-freshdesk-sso)** : als u de instellingen voor één Sign-On wilt configureren aan de kant van de toepassing.
    1. **[Een testgebruiker voor FreshDesk maken](#create-freshdesk-test-user)** : als u een tegenhanger van Britta Simon in FreshDesk wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **FreshDesk** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het potlood pictogram voor de **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door FreshDesk is voorgesteld.

    b. Typ in het tekstvak **Id (Entiteits-id)** een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com` of een andere waarde die door Freshdesk is voorgesteld.
     
    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In FreshDesk worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermopname ziet u de lijst met standaardkenmerken, waarbij **Unieke gebruikers-id** is toegewezen aan **user.userprincipalname** terwijl FreshDesk verwacht dat deze claim moet worden toegewezen aan **user.mail**. Dit betekent dat u de kenmerktoewijzing moet bewerken door op het pictogram Bewerken te klikken en de kenmerktoewijzing aan te passen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **FreshDesk instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan FreshDesk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **FreshDesk**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-freshdesk-sso"></a>FreshDesk SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Freshdesk.

2. Selecteer het pictogram **Settings** en voer in de sectie **Security** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/freshdesk-tutorial/configure-1.png "Eenmalige aanmelding")
  
    a. Selecteer **On** voor **Single Sign On**.

    b. Selecteer **SAML SSO** bij **Login Method**.

    c. Plak in het tekstvak **Entity ID provided by the IdP** de waarde van **Entiteit-id** die u uit de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    e. Selecteer **Only Signed Assertions** in de vervolgkeuzelijst  **Signing Options**.

    f. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    g. Plak in het tekstvak **Security Certificate** de waarde van **Certificaat (Base64)** die u eerder hebt vastgesteld.
  
    h. Klik op **Opslaan**.

## <a name="create-freshdesk-test-user"></a>Testgebruiker maken voor FreshDesk

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij FreshDesk, moeten ze worden ingericht bij FreshDesk.  
In het geval van FreshDesk is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Freshdesk**-tenant.

1. Klik in het menu aan de linkerkant op **Admin** en klik op het tabblad **General Settings** op **Agents**.
  
    ![Agents](./media/freshdesk-tutorial/create-user-1.png "Agents")

1. Klik op **New Agent**.

    ![New Agent](./media/freshdesk-tutorial/create-user-2.png "New Agent")

1. Voer in het dialoogvenster Agent Information de vereiste velden in en klik op **Create agent**.

    ![Agent Information](./media/freshdesk-tutorial/create-user-3.png "Agent Information")

    >[!NOTE]
    >De houder van het Azure AD-account krijgt een e-mailbericht met een koppeling om het account te bevestigen voordat dit wordt geactiveerd.
    >
    >[!NOTE]
    >U kunt de AAD-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van Freshdesk voor het maken van Azure AD-gebruikersaccounts.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de FreshDesk-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor FreshDesk-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel FreshDesk in de mijn apps klikt, moet u automatisch worden aangemeld bij de FreshDesk waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u FreshDesk hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).