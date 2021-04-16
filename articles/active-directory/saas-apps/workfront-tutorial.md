---
title: 'Zelfstudie: Azure Active Directory-integratie met Workfront | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Workfront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478442"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Zelfstudie: Azure Active Directory-integratie met Workfront

In deze zelfstudie leert u hoe u Workfront integreert met Azure Active Directory (Azure AD). Wanneer u Workfront integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Workfront.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Workfront.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure Active Directory-integratie met Workfront te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Workfront ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workfront ondersteunt door **SP geïnitieerde** eenmalige aanmelding.

## <a name="add-workfront-from-the-gallery"></a>Workfront toevoegen vanuit de galerie

Om de integratie van Workfront te configureren in Azure AD, moet u Workfront uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in **de sectie Toevoegen uit** de galerie **Workfront** in het zoekvak.
1. Selecteer **Workfront in** het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Eenmalige aanmelding van Azure AD voor Workfront configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Workfront met behulp van een testgebruiker met de **naam B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workfront.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Workfront te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Workfront configureren](#configure-workfront-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Workfront maken](#create-workfront-test-user)** : als u een tegenhanger van B.Simon in Workfront wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal de sectie Beheren op de integratiepagina van de toepassing **Workfront** en **selecteer Een aanmelding.** 
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.attask-ondemand.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Workfront](https://www.workfront.com/services-and-support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Workfront instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om een aanmelding van Azure te gebruiken door toegang te verlenen tot Workfront.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. In de lijst met toepassingen selecteert u **Workfront**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workfront-sso"></a>Eenmalige aanmelding voor Workfront configureren

1. Meld u als beheerder aan bij de bedrijfssite van Workfront.

2. Ga naar **Single Sign On Configuration**.

3. Voer in het dialoogvenster **Single Sign-On** de volgende stappen uit
    
    ![Eenmalige aanmelding configureren](./media/workfront-tutorial/single-sign-on.png)
   
    a. Selecteer **SAML 2.0** als **type**.
   
    b. Selecteer **Service Provider ID**.
   
    c. Plak de **Aanmeldings-URL** in het tekstvak **Login Portal URL**.
   
    d. Plak de **Afmeldings-URL** in het tekstvak **Sign-Out URL**.
   
    e. Plak de **URL voor het wijzigen van het wachtwoord** in het tekstvak **Change Password URL**.
   
    f. Klik op **Opslaan**.

### <a name="create-workfront-test-user"></a>Testgebruiker voor Workfront maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Workfront.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in Workfront:**

1. Meld u bij de bedrijfssite van Workfront aan als beheerder.
 
2. Klik in het menu bovenaan op **People**.
 
3. Klik op **New Person**. 

4. Voer de volgende stappen uit in het dialoogvenster New Person:
   
    ![Een testgebruiker voor Workfront maken](./media/workfront-tutorial/add-person.png)
   
    a. Typ 'Britta' in het tekstvak **First Name**.
   
    b. Typ 'Simon' in het tekstvak **Last Name**.
   
    c. Typ in het tekstvak **Email Address** het e-mailadres van Britta Simon in Azure Active Directory.
   
    d. Klik op **Add Person**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Workfront, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Workfront en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Workfront in het Mijn apps, wordt u omgeleid naar de aanmeldings-URL van Workfront. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Workfront hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
