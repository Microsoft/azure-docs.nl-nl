---
title: 'Zelfstudie: Azure Active Directory-integratie met Autotask Workplace | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Autotask Workplace configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649937"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Zelfstudie: Azure Active Directory-integratie met Autotask Workplace

In deze zelf studie leert u hoe u de werk plek autotask integreert met Azure Active Directory (Azure AD). Wanneer u de werk plek voor de taak samen met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de werk plek van de taak.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden bij een werk plek met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Eenmalige aanmelding (SSO) van de werk plek.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Autotask Workplace biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="add-autotask-workplace-from-the-gallery"></a>Werk plek voor het toevoegen van een taak vanuit de galerie

Om de integratie van Autotask Workplace in Azure AD te configureren, moet u Autotask Workplace vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **werk plek** in het zoekvak.
1. Selecteer **werk plek** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Azure AD SSO configureren en testen voor de werk plek voor automatisch taken

Azure AD SSO configureren en testen met een werk plek met een test gebruiker met de naam **B. Simon**. Voor eenmalige aanmelding moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de werk plek voor taak gebruik.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met de werk plek autotasks:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SSO van de werk plek configureren](#configure-autotask-workplace-sso)** : als u de instellingen voor eenmalige aanmelding aan de kant van de toepassing wilt configureren.
    1. Een gebruiker voor de **[werk plek](#create-autotask-workplace-test-user)** voor het maken van een taak en een equivalent van B. Simon in de werk plek autotasks die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina **werk plek** voor de integratie van de taak voor de Multi Task-toepassing de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **Set up Autotask Workplace** (Autotask Workplace instellen) kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de werk plek voor de taak.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Autotask Workplace**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-autotask-workplace-sso"></a>Eenmalige aanmelding voor de werk plek configureren

1. Meld u in een ander browservenster met de administratorreferenties aan bij Workplace Online.

    > [!Note]
    > Bij het configureren van de id-provider moet een subdomein worden opgegeven. Om het juiste subdomein te bevestigen, meldt u zich aan bij Workplace Online. Noteer nadat u bent aangemeld het subdomein in de URL. Het subdomein is het gedeelte tussen 'https://' en '.awp.autotask.net/' en moet us, eu, ca, of au zijn.

2. Ga naar **Configuratie** > **Eenmalige aanmelding** en voer de volgende stappen uit:

    ![Configuratie voor eenmalige aanmelding bij Autotask](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Selecteer de optie **XML-bestand met metagegevens** en upload het gedownloade **XML-bestand met federatieve metagegevens** vanuit de Azure-portal.

    b. Klik op **SSO INSCHAKELEN**.

    ![Configuratie voor de goedkeuring van een eenmalige aanmelding bij Autotask](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Selecteer het selectievakje **I confirm this information is correct and I trust this IdP** (Ik bevestig dat deze informatie juist is en dat ik deze id-provider vertrouw).

    d. Klik op **GOEDKEUREN**.

> [!Note]
> Hebt u hulp nodig bij het configureren van Autotask Workplace? Raadpleeg [deze pagina](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) voor hulp bij uw Workplace-account.

### <a name="create-autotask-workplace-test-user"></a>Testgebruiker voor Autotask Workplace maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in Autotask Workplace. Werk samen met het [ondersteuningsteam van Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) om de gebruikers aan het Autotask Workplace-platform toe te voegen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van de auto Task-werk plek waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van de werk plek en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de werk plek voor automatische taken waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel werk plek in de groep mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de werk plek voor automatische taken waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de werk plek voor automatisch taken configureert, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).