---
title: 'Zelfstudie: Azure Active Directory-integratie met Greenhouse | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731917"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Zelfstudie: Azure Active Directory-integratie met Greenhouse

In deze zelfstudie leert u hoe u Greenhouse kunt integreren met Azure Active Directory (Azure AD). Wanneer u Greenhouse integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Greenhouse.
* Zorg ervoor dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Greenhouse.
* Uw accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een[gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Greenhouse waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE] 
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud. 

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Greenhouse ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-greenhouse-from-the-gallery"></a>Greenhouse vanuit de galerie toevoegen

Voor het configureren van de integratie van Greenhouse in Azure AD moet u Greenhouse vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Greenhouse** in het zoekvak.
1. Selecteer **Greenhouse** in het resultatenvenster en voeg daarna de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Greenhouse

Eenmalige aanmelding van Azure AD configureren en testen voor Greenhouse met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Greenhouse.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Greenhouse:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor Greenhouse configureren](#configure-greenhouse-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Greenhouse maken](#create-greenhouse-test-user)**: als u een tegenhanger van Britta Simon in Greenhouse wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Greenhouse**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.greenhouse.io`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Greenhouse-ondersteuningsteam](https://www.greenhouse.io/contact) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Greenhouse instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Greenhouse.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Greenhouse** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-greenhouse-sso"></a>Eenmalige aanmelding voor Greenhouse configureren

1. Meld u, in een ander browservenster, als beheerder aan bij de website van Greenhouse.

1. Ga naar **Configureren > Ontwikkelaarscentrum > Eenmalige aanmelding configureren**.

    ![Schermopname van de pagina voor eenmalige aanmelding](./media/greenhouse-tutorial/configure.png)

1. Voer op de pagina 'Eenmalige aanmelding' de volgende stappen uit.

    ![Schermopname van de configuratiepagina voor eenmalige aanmelding](./media/greenhouse-tutorial/sso-page.png)

    a. Kopieer de waarde van **Assertion Consumer-URL voor eenmalige aanmelding** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** van de Azure Portal.

    b. Plak in het tekstvak **Entiteits-id/verlener** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **Eenmalige aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u uit de Azure Portal hebt gekopieerd.

    d. Open het gedownloade **Federation Metadata XML** van de Azure Portal in Kladblok. Plak vervolgens de inhoud in het tekstvak **IdP Certificate Fingerprint**.

    e. Selecteer de waarde **Indeling voor naam-id** uit de vervolgkeuzelijst.

    f. Klik op **Starten met testen**.

    >[!NOTE]
    >U kunt ook het **XML-bestand met federatieve metagegevens** uploaden door op de optie **Choose File** te klikken.

### <a name="create-greenhouse-test-user"></a>Greenhouse-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Greenhouse, moeten ze worden ingericht bij Greenhouse. In het geval van Greenhouse is het inrichten een handmatige taak.

>[!NOTE]
>U kunt de Azure AD-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van Greenhouse voor het maken van gebruikersaccounts. 

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u aan bij uw **Greenhouse**-bedrijfssite als beheerder.

2. Ga naar de **Configureren > Gebruikers > Nieuwe gebruikers**
   
    ![Gebruikers](./media/greenhouse-tutorial/create-user-1.png "Gebruikers")

4. Voer in het gedeelte **Nieuwe gebruikers toevoegen** de volgende stappen uit:
   
    ![Nieuwe gebruiker toevoegen](./media/greenhouse-tutorial/create-user-2.png "Nieuwe gebruiker toevoegen")

    a. Typ in het tekstvak **E-mails gebruiker invoeren** het e-mailadres van een geldig Azure Active Directory-account dat u wilt inrichten.

    b. Klik op **Opslaan**.    
   
      >[!NOTE]
      >De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Greenhouse, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Greenhouse en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in 'Mijn apps' op de tegel 'Greenhouse' klikt, wordt u omgeleid naar de aanmeldings-URL van Greenhouse. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Greenhouse hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).