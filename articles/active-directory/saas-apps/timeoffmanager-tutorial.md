---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met TimeOffManager | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TimeOffManager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 849236b9ac33cec92cc145bb32b4271b73476057
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608813"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met TimeOffManager

In deze zelfstudie leert u hoe u TimeOffManager kunt integreren met Azure AD (Active Directory). Wanneer u TimeOffManager integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot TimeOffManager.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TimeOffManager.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een TimeOffManager-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* TimeOffManager ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

* TimeOffManager biedt ondersteuning voor het **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-timeoffmanager-from-the-gallery"></a>TimeOffManager toevoegen vanuit de galerie

Voor het configureren van de integratie van TimeOffManager in Azure AD, moet u TimeOffManager vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **TimeOffManager**.
1. Selecteer **TimeOffManager** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Eenmalige aanmelding van Azure AD configureren en testen voor TimeOffManager

Configureer en test eenmalige aanmelding van Azure AD met TimeOffManager met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TimeOffManager.

Voltooi de volgende bouwstenen om Azure AD-eenmalige aanmelding met TimeOffManager te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met TimeOffManager configureren](#configure-timeoffmanager-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[TimeOffManager-testgebruiker maken](#create-timeoffmanager-test-user)** : als tegenhanger van B.Simon in TimeOffManager die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **TimeOffManager** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. U kunt deze waarde vinden op de **pagina met instellingen voor eenmalige aanmelding** die verderop in de zelfstudie wordt beschreven of contact opnemen met het [TimeOffManager-ondersteuningsteam](https://www.purelyhr.com/contact-us). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de TimeOffManager-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de toepassing TimeOffManager nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | --- | --- |
    | Voornaam |User.givenname |
    | Achternaam |User.surname |
    | Email |User.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **TimeOffManager instellen** de juiste URL(‘s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TimeOffManager.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **TimeOffManager**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-timeoffmanager-sso&quot;></a>Eenmalige aanmelding voor TimeOffManager configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in TimeOffManager.

2. Ga naar **Account \> Accountopties \> Instellingen voor eenmalige aanmelding**.
   
    ![Schermopname met Single Sign-On Settings geselecteerd in Account Options.](./media/timeoffmanager-tutorial/ic795917.png &quot;Instellingen voor eenmalige aanmelding")

3. Voer de volgende stappen uit in het gedeelte **Instellingen voor eenmalige aanmelding**:
   
    ![Schermopname van de sectie Single Sign-On Settings, waarin u de beschreven waarden kunt invoeren.](./media/timeoffmanager-tutorial/ic795918.png "Instellingen voor eenmalige aanmelding")
   
    a. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het klembord en plak het hele certificaat in het tekstvak **X.509 Certificate**.
   
    b. Plak in het tekstvak **Idp-verlener** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.
   
    c. Plak in het tekstvak **Idp-eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
    d. Selecteer **Nee** bij **SAML afdwingen**.
   
    e. Selecteer **Ja** bij **Automatisch gebruikers maken**.
   
    f. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
   
    g. Klik op **Wijzigingen opslaan**.

4. Kopieer op de pagina **Instellingen voor eenmalige aanmelding** de waarde van de **URL voor Assertion Consumer Service** en plak deze in het tekstvak **Antwoord-URL** onder de sectie **Standaard SAML-configuratie** in de Azure-portal. 

    ![Schermopname met de koppeling naar Assertion Consumer Service U R L.](./media/timeoffmanager-tutorial/ic795915.png "Instellingen voor eenmalige aanmelding")

### <a name="create-timeoffmanager-test-user"></a>TimeOffManager-testgebruiker maken

In deze sectie maakt u in TimeOffManager een gebruiker met de naam Britta Simon. TimeOffManager biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in TimeOffManager bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het creëren van TimeOffManager-gebruikersaccounts of API's van TimeOffManager gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de TimeOffManager-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van TimeOffManager waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer TimeOffManager met Azure AD](https://aad.portal.azure.com/)