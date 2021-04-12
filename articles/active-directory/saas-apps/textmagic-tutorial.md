---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met TextMagic | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TextMagic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: f1e6cd222c9ee8f40f81d4db3750956e8e698e3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607666"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met TextMagic

In deze zelfstudie leert u hoe u TextMagic integreert met Azure Active Directory (Azure AD). Wanneer u TextMagic integreert met Azure AD, kunt u het volgende doen:

* Controleer in Azure AD wie toegang heeft tot TextMagic.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TextMagic.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* TextMagic-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TextMagic ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

* TextMagic biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-textmagic-from-the-gallery"></a>TextMagic toevoegen uit de galerie

Om de integratie van TextMagic in Azure AD te configureren, moet u TextMagic vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **TextMagic**.
1. Selecteer **TextMagic** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Azure AD-eenmalige aanmelding configureren en testen voor TextMagic

Configureer en test eenmalige aanmelding van Azure AD met TextMagic met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TextMagic.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met TextMagic te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij TextMagic configureren](#configure-textmagic-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[TextMagic-testgebruiker maken](#create-textmagic-test-user)** : om een tegenhanger voor B.Simon in TextMagic te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/), op de integratiepagina voor de **TextMagic**-toepassing, naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Id** typt u een URL: `https://my.textmagic.com/saml/metadata`

5. In de TextMagic-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de TextMagic-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de toepassing TextMagic nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |   Bronkenmerk| Naamruimte  |
    | --------------- | --------------- | --------------- |
    | bedrijf | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | voornaam | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | achternaam | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefoon | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **TextMagic instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TextMagic.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TextMagic** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-textmagic-sso"></a>Eenmalige aanmelding voor TextMagic configureren

1. Als u de configuratie in TextMagic wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **TextMagic instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de TextMagic-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij TextMagic. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u TextMagic handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de TextMagic-bedrijfssite. Voer hierna de volgende stappen uit:

4. Selecteer **Accountinstellingen** onder de gebruikersnaam.

    ![Schermopname met Accountinstellingen geselecteerd door de gebruiker.](./media/textmagic-tutorial/config1.png)

5. Klik op het tabblad **Eenmalige aanmelding (SSO)** en vul de volgende velden in:  

    ![Schermopname van het tabblad Eenmalige aanmelding (SSO), waarin u de beschreven waarden kunt invoeren.](./media/textmagic-tutorial/config2.png)

    a. Plak in het tekstvak **Identity Provider Issuer:** de waarde van **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    b. Plak in het tekstvak voor de **SSO-URL van ID-provider:** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **SLO-URL van ID-provider** de waarde van de **Afmeldings-URL** in die u hebt gekopieerd uit het Azure Portal.

    d. Open in Kladblok het **met Base 64 gecodeerde certificaat** dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Public x509 certificate:** .

    e. Klik op **Opslaan**.

### <a name="create-textmagic-test-user"></a>TextMagic-testgebruiker maken

De toepassing biedt ondersteuning voor **just in time-gebruikersinrichting**. Dit betekent dat gebruikers na verificatie automatisch worden aangemaakt in de toepassing. U moet de gegevens eenmaal bij de eerste aanmelding invullen om de subaccount in het systeem te activeren.
Er is geen actie-item voor u in deze sectie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel TextMagic klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van TextMagic waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer TextMagic met Azure AD](https://aad.portal.azure.com/)