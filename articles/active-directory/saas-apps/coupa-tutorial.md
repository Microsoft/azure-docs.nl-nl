---
title: 'Zelfstudie: Azure Active Directory-integratie met Coupa | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652977"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Zelfstudie: Azure Active Directory-integratie met Coupa

In deze zelf studie leert u hoe u bijsnijdt kunt integreren met Azure Active Directory (Azure AD). Wanneer u bijsnijdt integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot versnijda.
* Stel in dat uw gebruikers automatisch worden aangemeld voor het versnijden van de Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Coupa ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-coupa-from-the-gallery"></a>Bijsnijden toevoegen vanuit de galerie

Om de integratie van Coupa in Azure AD te configureren, moet u Coupa vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **snijdt** in het zoekvak.
1. Selecteer **koppeling** naar het paneel met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Azure AD SSO configureren en testen voor versnijda

Azure AD SSO configureren en testen met versnijda met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Snijda.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met verwerkend:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. De instellingen voor de eenmalige **[aanmelding configureren aan](#configure-coupa-sso)** de kant van de toepassing.
    1. **[Maak een gebruiker](#create-coupa-test-user)** van reslaga test om een tegen hanger te hebben van B. Simon inkoppelinga dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal **naar de pagina** **koppeling** voor toepassings integratie en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Coupa](https://success.coupa.com/Support/Contact_Us?) om deze waarde op te vragen.

    b. Typ in het vak **Id** de URL:

    | Omgeving  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Productie | `sso-prd1.coupahost.com`|
    | | |

    c. Typ in het tekstvak **Antwoord-URL** de URL:

    | Omgeving | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Productie | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Coupa instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de koppeling.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Coupa** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-coupa-sso"></a>Versnijda-SSO configureren

1. Meld u als beheerder aan bij de bedrijfssite van Coupa.

2. Ga naar **Setup \> Security controls**.

    ![Beveiligingsmaatregelen](./media/coupa-tutorial/setup.png "Beveiligingsmaatregelen")

3. Voer de volgende stappen uit in het gedeelte **Log in using Coupa credentials**:

    ![Metagegevens van Coupa SP](./media/coupa-tutorial/login.png "Metagegevens van Coupa SP")

    a. Selecteer **Log in using SAML**.

    b. Klik op **Browse** om de metagegevens te uploaden die u hebt gedownload van de Azure-portal.

    c. Klik op **Opslaan**.

### <a name="create-coupa-test-user"></a>Een testgebruiker voor Coupa maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Coupa, moeten ze worden ingericht in Coupa.  

* In het geval van Coupa is dat een handmatige taak.

**Voer de volgende stappen uit om de gebruikersinrichting te configureren:**

1. Meld u als beheerder aan bij de bedrijfssite van **Coupa**.

2. Klik in het menu bovenaan op **Setup** en klik vervolgens op **Users**.

    ![Gebruikers](./media/coupa-tutorial/user.png "Gebruikers")

3. Klik op **Create**.

    ![Gebruikers maken](./media/coupa-tutorial/create.png "Gebruikers maken")

4. Voer in het gedeelte **User Create** de volgende stappen uit:

    ![Gebruikersdetails](./media/coupa-tutorial/details.png "Gebruikersdetails")

    a. Typ in de vakken **Login**, **First name**, **Last name**, **Single Sign-On ID** en **Email** kenmerken van een geldig Azure Active Directory-account dat u wilt inrichten.

    b. Klik op **Create**.

    >[!NOTE]
    >De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    >

>[!NOTE]
>U kunt andere hulpprogramma's of API's van Coupa gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL voor koppelings gegevens, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de koppeling en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel koppeling maken in de mijn apps klikt, moet u automatisch worden aangemeld bij de versnijdt waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u bijsnijdt hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)