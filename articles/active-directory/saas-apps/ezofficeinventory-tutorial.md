---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met EZOfficeInventory | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en EZOfficeInventory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: dcf64e6538140905e00e0fcd9d0383f15e8ad95a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453659"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met EZOfficeInventory

In deze zelfstudie leert u hoe u EZOfficeInventory integreert met Azure Active Directory (Azure AD). Wanneer u EZOfficeInventory integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot EZOfficeInventory.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij EZOfficeInventory.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een EZOfficeInventory-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* EZOfficeInventory ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* EZOfficeInventory biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers
* Zodra u EZOfficeInventory hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beveiligd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>EZOfficeInventory toevoegen uit de galerie

Om de integratie van EZOfficeInventory in Azure AD te configureren, moet u EZOfficeInventory uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **EZOfficeInventory**.
1. Selecteer **EZOfficeInventory** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Eenmalige aanmelding van Azure AD configureren en testen voor EZOfficeInventory

Configureer en test eenmalige aanmelding van Azure AD met EZOfficeInventory met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in EZOfficeInventory.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met EZOfficeInventory, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij EZOfficeInventory configureren](#configure-ezofficeinventory-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Een testgebruiker voor EZOfficeInventory maken](#create-ezofficeinventory-test-user)** : als u in EZOfficeInventory een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **EZOfficeInventory** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van EZOfficeInventory](mailto:support@ezofficeinventory.com) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de toepassing EZOfficeInventory worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de toepassing EZOfficeInventory nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | e-mail | user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **EZOfficeInventory instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot EZOfficeInventory.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **EZOfficeInventory** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ezofficeinventory-sso"></a>Eenmalige aanmelding bij EZOfficeInventory configureren

1. Als u de configuratie in EZOfficeInventory wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **EZOfficeInventory instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de toepassing EZOfficeInventory. Geef hier de beheerdersreferenties op om u aan te melden bij EZOfficeInventory. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u EZOfficeInventory handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de EZOfficeInventory-bedrijfssite. Voer daarna de volgende stappen uit:

1. Klik in de rechterbovenhoek van de pagina op **Profiel** en navigeer vervolgens naar **Instellingen** > **Invoegtoepassingen**.

    ![Schermopname met de pagina "Instellingen" met het tabblad "Invoegtoepassingen" geselecteerd.](./media/ezofficeinventory-tutorial/configure01.png)

1. Schuif omhoog naar de sectie **SAML-integratie** en voer de volgende stappen uit:

    ![EZOfficeInventory-configuratie](./media/ezofficeinventory-tutorial/configure02.png)

    a. Selecteer de optie **Enabled**.

    b. Plak in het tekstvak **URL van id-provider** de waarde voor **Aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Open het met base64 gecodeerde certificaat in Kladblok, kopieer de inhoud en plak het in het tekstvak **Identity Provider Certificate**.

    d. Voer in het tekstvak **Login Button Text** de tekst van de aanmeldknop in.

    e. Voer in het tekstvak **First Name** de tekst **first_name** in.

    f. Voer in het tekstvak **Last Name** de tekst **last_name** in.

    g. Voer in het tekstvak **Email** de tekst **email** in.

    h. Selecteer uw gewenste rol bij de optie **EZOfficeInventory Role By default**.

    i. Klik op **Update**.

### <a name="create-ezofficeinventory-test-user"></a>Een EZOfficeInventory-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in EZOfficeInventory. EZOfficeInventory biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in EZOfficeInventory bestaat, wordt na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel EZOfficeInventory in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van EZOfficeInventory waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [EZOfficeInventory proberen met Azure AD](https://aad.portal.azure.com/)