---
title: 'Zelfstudie: Integratie van Azure Active Directory met PagerDuty | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 9a3117b64c516120f8556b7b63b24e5ef906f973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met PagerDuty

In deze zelfstudie leert u hoe u PagerDuty integreert met Azure Active Directory (Azure AD). Wanneer u PagerDuty integreert met Azure AD, kunt u het volgende:

* Beheren in Azure AD wie toegang heeft tot PagerDuty.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij PagerDuty.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* PagerDuty-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

> [!NOTE]
> Als u MFA of verificatie zonder wachtwoord met Azure AD gebruikt, schakelt u de AuthnContext-waarde in de SAML-aanvraag uit. Anders wordt door Azure AD de fout gemeld dat de AuthnContext-waarde niet overeenkomt en wordt het token niet teruggestuurd naar de toepassing.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PagerDuty ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-pagerduty-from-the-gallery"></a>PagerDuty toevoegen vanuit de galerie

Als u de integratie van PagerDuty in Azure AD wilt configureren, moet u PagerDuty vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **PagerDuty** in the search box.
1. Selecteer **PagerDuty** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Eenmalige aanmelding van Azure AD configureren en testen voor PagerDuty

Configureer en test eenmalige aanmelding van Azure AD met PagerDuty met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PagerDuty.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met PagerDuty te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor PagerDuty configureren](#configure-pagerduty-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken in PagerDuty](#create-pagerduty-test-user)** : om een tegenhanger voor B.Simon te maken in PagerDuty die is gekoppeld aan de voorstelling van de gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **PagerDuty** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van PagerDuty](https://www.pagerduty.com/support/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **PagerDuty instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot PagerDuty.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **PagerDuty** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name=&quot;configure-pagerduty-sso&quot;></a>Eenmalige aanmelding voor PagerDuty configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in PagerDuty.

2. Klik in het menu bovenaan op **Accountinstellingen**.

    ![Accountinstellingen](./media/pagerduty-tutorial/ic778535.png &quot;Accountinstellingen")

3. Klik op **Eenmalige aanmelding**.

    ![Eenmalige aanmelding](./media/pagerduty-tutorial/ic778536.png "Eenmalige aanmelding")

4. Voer op de pagina **Eenmalige aanmelding inschakelen** de volgende stappen uit:

    ![Eenmalige aanmelding inschakelen](./media/pagerduty-tutorial/ic778537.png "Eenmalige aanmelding inschakelen")

    a. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit Azure Portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**
  
    b. Plak in het tekstvak **Aanmeldings-URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.
  
    c. Plak in het tekstvak **Afmeldings-URL** de **afmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    d. Selecteer **Allow username/password login**.

    e. Schakel het selectievakje **Exacte verificatiecontextvergelijking vereisen** in.

    f. Klik op **Wijzigingen opslaan**.

### <a name="create-pagerduty-test-user"></a>Testgebruiker maken in PagerDuty

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij PagerDuty, moeten ze worden ingericht in PagerDuty. In het geval van PagerDuty wordt het inrichten handmatig uitgevoerd.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van PagerDuty-gebruikersaccounts of API's van PagerDuty gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **PagerDuty**-tenant.

2. Klik in het menu bovenaan op **Gebruikers**.

3. Klik op **Gebruikers toevoegen**.
   
    ![Gebruikers toevoegen](./media/pagerduty-tutorial/ic778539.png "Gebruikers toevoegen")

4.  Voer in het dialoogvenster **Uw team uitnodigen** de volgende stappen uit:
   
    ![Uw team uitnodigen](./media/pagerduty-tutorial/ic778540.png "Uw team uitnodigen")

    a. Typ in **Voor- en achternaam** de naam van de gebruiker, bijvoorbeeld **B.Simon**. 
   
    b. Voer in **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld **b.simon\@contoso.com**.
   
    c. Klik op **Toevoegen** en vervolgens op **Uitnodigingen verzenden**.
   
    > [!NOTE]
    > Alle toegevoegde gebruikers ontvangen een uitnodiging voor het maken van een PagerDuty-account.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de PagerDuty-aanmeldings-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor PagerDuty-aanmelding en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel PagerDuty in de mijn apps klikt, wordt dit omgeleid naar de PagerDuty-aanmeldings-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u PagerDuty hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).