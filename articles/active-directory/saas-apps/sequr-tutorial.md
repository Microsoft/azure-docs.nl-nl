---
title: 'Zelf studie: integratie Azure Active Directory met Gena Access Control | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Gena Access Control.
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
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669857"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Zelf studie: integratie Azure Active Directory met Gena Access Control

In deze zelf studie leert u hoe u Gena-Access Control integreert met Azure Active Directory (Azure AD). Wanneer u Gena-Access Control integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Gena Access Control.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Gena Access Control met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Gena Access Control wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Gena Access Control abonnement voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Gena Access Control ondersteunt SSO die **met SP en IDP** is gestart.
> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-genea-access-control-from-the-gallery"></a>Gena Access Control toevoegen vanuit de galerie

Als u de integratie van Gena-Access Control wilt configureren in azure AD, moet u Gena Access Control toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Gena Access Control** in het zoekvak.
1. Selecteer **gena Access Control** uit het paneel resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Azure AD SSO configureren en testen voor Gena-Access Control

Azure AD SSO configureren en testen met Gena Access Control met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Gena Access Control.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Gena Access Control:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Gena Access Control-SSO configureren](#configure-genea-access-control-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak gena Access Control test gebruiker](#create-genea-access-control-test-user)** : als u een equivalent van B. Simon wilt hebben in gena Access Control dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **gena Access Control** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    Typ de volgende URL in het tekstvak **Id**: `https://login.sequr.io`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://login.sequr.io`

    b. In het tekstvak **Relaystatus** krijgt u deze waarde die verderop in de zelfstudie wordt uitgelegd.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer de gewenste URL ('s) volgens uw vereiste in de sectie een **Access Control instellen** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Gena Access Control.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **gena Access Control**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.
## <a name="configure-genea-access-control-sso"></a>Gena Access Control-SSO configureren

1. Meld u in een ander webbrowser venster aan bij de bedrijfs site van uw Gena-Access Control als beheerder.

1. Klik op **Integraties** in het linkernavigatievenster.

    ![Schermopname van Integraties geselecteerd in het navigatiedeelvenster.](./media/sequr-tutorial/configure-1.png)

1. Schuif omlaag naar de sectie **Eenmalige aanmelding** en klik op **Beheren**.

    ![Schermopname van de sectie Eenmalige aanmelding met de knop Beheren geselecteerd.](./media/sequr-tutorial/configure-2.png)

1. Voer in het gedeelte **Eenmalige aanmelding beheren** de volgende stappen uit:

    ![Schermopname van de sectie Eenmalige aanmelding beheren, waarin u de beschreven waarden kunt invoeren.](./media/sequr-tutorial/configure-3.png)

    a. Plak in het tekstvak **URL van id-provider voor eenmalige aanmelding** de **aanmeldings-URL** die u hebt gekopieerd in de Microsoft Azure-portal.

    b. Versleep het bestand **Certificaat** dat u gedownload heeft vanuit het Azure-portaal of voer de inhoud van het certificaat handmatig in.

    c. Wanneer u de configuratie heeft opgeslagen, wordt de waarde voor de relaystatus gegenereerd. Kopieer de waarde van de **relaystatus** en plak deze in het tekstvak **Relaystatus** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    d. Klik op **Opslaan**.

### <a name="create-genea-access-control-test-user"></a>Gena-Access Control test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Gena Access Control. Werk samen met [gena Access Control client ondersteunings team](mailto:support@sequr.io) om de gebruikers toe te voegen aan het product gena Access Control. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar Gena Access Control-aanmeldings-URL, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar Gena Access Control URL voor aanmelden en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij het Access Control Gena waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Gena Access Control in de mijn apps klikt, wordt u naar de aanmeldings pagina van de toepassing omgeleid voor het initiëren van de aanmeldings stroom en als deze is geconfigureerd in de modus IDP, dan moet u automatisch worden aangemeld bij de Gena-Access Control waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Als u Gena Access Control configureert, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).