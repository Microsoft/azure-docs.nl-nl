---
title: 'Zelfstudie: Azure Active Directory-integratie met LogicMonitor | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en LogicMonitor configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649402"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Zelfstudie: Azure Active Directory-integratie met LogicMonitor

In deze zelf studie leert u hoe u LogicMonitor integreert met Azure Active Directory (Azure AD). Wanneer u LogicMonitor integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot LogicMonitor.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij LogicMonitor met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met LogicMonitor hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding LogicMonitor ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LogicMonitor ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-logicmonitor-from-the-gallery"></a>LogicMonitor toevoegen vanuit de galerie

Voor het configureren van de integratie van LogicMonitor in Azure AD, moet u LogicMonitor vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **LogicMonitor** in het zoekvak.
1. Selecteer **LogicMonitor** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Azure AD SSO voor LogicMonitor configureren en testen

Azure AD SSO met LogicMonitor configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LogicMonitor.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met LogicMonitor:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LOGICMONITOR SSO configureren](#configure-logicmonitor-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een LogicMonitor-test gebruiker](#create-logicmonitor-test-user)** -om een equivalent van B. Simon in LogicMonitor te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **LogicMonitor** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij LogicMonitor](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.logicmonitor.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.logicmonitor.com`
    
    c. Typ in het tekstvak **antwoord-URL (assertion Consumer Service-URL)** een URL met het volgende patroon: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van LogicMonitor](https://www.logicmonitor.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **LogicMonitor instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan LogicMonitor.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LogicMonitor** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-logicmonitor-sso"></a>LogicMonitor SSO configureren

1. Meld u aan bij uw **LogicMonitor**-bedrijfssite als beheerder.

2. Klik in het menu aan de bovenkant op **Settings**.

    ![Instellingen](./media/logicmonitor-tutorial/ic790052.png "Instellingen")

3. Klik in het navigatie bat aan de linkerkant op **eenmalige aanmelding**.

    ![Single Sign-On](./media/logicmonitor-tutorial/ic790053.png "Eenmalige aanmelding")

4. Voer de volgende stappen uit in het gedeelte **Instellingen voor eenmalige aanmelding**:

    ![Instellingen voor eenmalige aanmelding](./media/logicmonitor-tutorial/ic790054.png "Instellingen voor eenmalige aanmelding")

    a. Schakel het selectievakje **Eenmalige aanmelding inschakelen** in.

    b. Selecteer voor **Standaard roltoewijzing** de optie **Alleen-lezen**.

    c. Open het gedownloade metagegevensbestand in Kladblok en plak de inhoud van het bestand in het tekstvak **Metagegevens van ID-provider** .

    d. Klik op **Wijzigingen opslaan**.

### <a name="create-logicmonitor-test-user"></a>LogicMonitor-testgebruiker maken

Azure AD-gebruikers moeten worden ingericht voor de LogicMonitor-toepassing met hun Azure Active Directory-gebruikersnaam om zich te kunnen aanmelden.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u aan bij uw LogicMonitor-bedrijfssite als beheerder.

2. Klik in de menubalk bovenaan op **Instellingen** en klik vervolgens op **Rollen en gebruikers**.

    ![Rollen en gebruikers](./media/logicmonitor-tutorial/ic790056.png "Rollen en gebruikers")

3. Klik op **Add**.

4. Voer in de sectie **Een account toevoegen** de volgende stappen uit:

    ![Een account toevoegen](./media/logicmonitor-tutorial/ic790057.png "Een account toevoegen")

    a. Typ waarden voor **Gebruikersnaam**, **E-mail**, **Wachtwoord** en **Herhaal wachtwoord** voor de Azure Active Directory-gebruiker die u wilt inrichten in de bijbehorende tekstvakken.

    b. Selecteer **Rollen**, **Weergavemachtigingen** en de **Status**.

    c. Klik op **Submit**

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van LogicMonitor-gebruikersaccounts of API's van LogicMonitor gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de LogicMonitor-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor LogicMonitor-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel LogicMonitor in de mijn apps klikt, moet u automatisch worden aangemeld bij de LogicMonitor waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u LogicMonitor hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).