---
title: 'Zelf studie: integratie Azure Active Directory met Cisco paraplu admin SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco paraplu admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592503"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Zelf studie: integratie Azure Active Directory met Cisco paraplu admin SSO

In deze zelf studie leert u hoe u de Cisco paraplu admin SSO integreert met Azure Active Directory (Azure AD). Wanneer u Cisco paraplu admin SSO integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de beheer-SSO van Cisco paraplu.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Cisco paraplu admin SSO met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Cisco paraplu admin SSO-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco paraplu admin SSO ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Cisco paraplu admin SSO toevoegen vanuit de galerie

Als u de integratie van Cisco paraplu admin SSO wilt configureren in azure AD, moet u de Cisco paraplu admin SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Cisco paraplu admin SSO** in het zoekvak.
1. Selecteer **Cisco paraplu admin SSO** van het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Azure AD SSO configureren en testen voor Cisco paraplu admin SSO

Azure AD SSO configureren en testen met Cisco paraplu admin SSO met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de SSO van de beheerder van Cisco paraplu.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Cisco paraplu admin SSO:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Cisco paraplu admin SSO SSO configureren](#configure-cisco-umbrella-admin-sso-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een ' User SSO test '-gebruiker van Cisco paraplu](#create-cisco-umbrella-admin-sso-test-user)** -voor een soort equivalent van B. Simon in Cisco paraplu admin SSO die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de integratie pagina van de **Cisco paraplu-beheer** toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    a. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stappen uit:

    b. Klik op **Extra URL's instellen**.

    c. Typ in het tekstvak **URL voor aanmelding** de URL: `https://login.umbrella.com/sso`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste in de sectie **Cisco paraplu admin SSO instellen** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de beheer-SSO van Cisco paraplu.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Cisco paraplu admin SSO**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Cisco paraplu admin SSO SSO configureren

1. Meld u in een ander browser venster aan bij uw SSO-bedrijfs site van Cisco paraplu als beheerder.

2. Klik in het linkermenu op **Beheerder** en ga naar **Verificatie**. Klik vervolgens op **SAML**.

    ![De Beheerder](./media/cisco-umbrella-tutorial/admin.png)

3. Kies **Andere** en klik op **VOLGENDE**.

    ![De Andere](./media/cisco-umbrella-tutorial/other.png)

4. Klik op de pagina **Cisco paraplu admin SSO meta data**, op **volgende**.

    ![De Metagegevens](./media/cisco-umbrella-tutorial/metadata.png)

5. Als u op het tabblad **Upload Metadata** SAML vooraf hebt geconfigureerd, selecteert u de optie **Click here to change them** en voert u de onderstaande stappen uit.

    ![De Volgende](./media/cisco-umbrella-tutorial/next.png)

6. Upload in **Optie A: XML-bestand uploaden** het **XML-bestand met federatieve metagegevens** dat u in de Azure-portal hebt gedownload. Nadat u de metagegevens hebt geüpload, worden de onderstaande waarden automatisch ingevuld. Klik vervolgens op **VOLGENDE**.

    ![De Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. Klik in de sectie **SAML-configuratie valideren** op **TEST YOUR SAML CONFIGURATION**.

    ![De Test](./media/cisco-umbrella-tutorial/test.png)

8. Klik op **OPSLAAN**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Een gebruiker voor de SSO-test van Cisco paraplu maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Cisco paraplu admin SSO, moeten ze worden ingericht in de beheer-SSO van Cisco paraplu.  
In het geval van Cisco paraplu admin SSO is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u in een ander browser venster aan bij uw SSO-bedrijfs site van Cisco paraplu als beheerder.

2. Klik aan de linkerkant van het menu op **Beheerder** en ga naar **Accounts**.

    ![Het Account](./media/cisco-umbrella-tutorial/account.png)

3. Klik in de rechterbovenhoek van de pagina **Accounts** op **Toevoegen** en voer de volgende stappen uit.

    ![De Gebruiker](./media/cisco-umbrella-tutorial/create-user.png)

    a. Voer in het veld **Voornaam** een voornaam in, bijvoorbeeld **Britta**.

    b. Voer in het veld **Achternaam** een achternaam in, bijvoorbeeld **Simon**.

    c. Selecteer uw rol in **Choose Delegated Admin Role**.

    d. Voer in het veld **E-mailadres** de e-mailadressen in van de gebruiker, bijvoorbeeld **brittasimon\@contoso.com**.

    e. Voer in het veld **Wachtwoord** uw wachtwoord in.

    f. Voer in het veld **Wachtwoord bevestigen** uw wachtwoord opnieuw in.

    g. Klik op **MAKEN**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor aanmelding voor de beheerder van Cisco paraplu, waar u de aanmeldings stroom kunt initiëren.  

* Ga naar de URL voor aanmelding voor de beheerder van Cisco paraplu direct en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en meld u automatisch aan bij de Cisco paraplu admin SSO waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in de modus mijn apps op de tegel Cisco paraplu beheer-SSO klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren. Als u dit hebt geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Cisco paraplu admin SSO waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u de Cisco paraplu admin SSO hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).