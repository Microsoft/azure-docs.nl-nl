---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MS Azure SSO Access for Ethidex Compliance Office™ | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en MS Azure SSO Access for Ethidex Compliance Office™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520911"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MS Azure SSO Access for Ethidex Compliance Office™

In deze zelfstudie leert u hoe u MS Azure SSO Access for Ethidex Compliance Office™ kunt integreren met Azure Active Directory (Azure AD). Wanneer u MS Azure SSO Access for Ethidex Compliance Office™ integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot MS Azure SSO Access for Ethidex Compliance Office™.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij MS Azure SSO Access for Ethidex Compliance Office™.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op MS Azure SSO Access for Ethidex Compliance Office™ waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MS Azure SSO Access for Ethidex Compliance Office™ ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>MS Azure tot Ethidex Compliance Office™ toevoegen vanuit de galerie

Voor het configureren van de integratie van MS Azure SSO Access for Ethidex Compliance Office™ in Azure AD moet u MS Azure SSO Access for Ethidex Compliance Office™ uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **MS Azure SSO Access for Ethidex Compliance Office™** in het zoekvak.
1. Selecteer **MS Azure SSO Access for Ethidex Compliance Office™** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Eenmalige aanmelding van Azure AD configureren en testen voor MS Azure SSO Access for Ethidex Compliance Office™

Configureer en test eenmalige aanmelding van Azure AD met MS Azure SSO Access for Ethidex Compliance Office™ met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MS Azure SSO Access for Ethidex Compliance Office™.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met MS Azure SSO Access for Ethidex Compliance Office™ wilt configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor MS Azure SSO Access for Ethidex Compliance Office™ configureren](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor MS Azure SSO Access for Ethidex Compliance Office™ maken](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** : als u in MS Azure SSO Access for Ethidex Compliance Office™ een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **MS Azure SSO Access for Ethidex Compliance Office™** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `com.ethidex.prod.<CLIENTID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [MS Azure SSO Access for Ethidex Compliance Office™](mailto:support@ethidex.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de toepassing MS Azure SSO Access for Ethidex Compliance Office™ worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de toepassing MS Azure SSO Access for Ethidex Compliance Office™ wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Raw)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **MS Azure SSO Access for Ethidex Compliance Office™ instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot MS Azure SSO Access for Ethidex Compliance Office™.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **MS Azure tot Ethidex Compliance Office™** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Eenmalige aanmelding voor MS Azure for Ethidex Compliance Office™ configureren

Voor het configureren van eenmalige aanmelding aan de kant van **MS Azure SSO Access for Ethidex Compliance Office™**, moet u het gedownloade **certificaat (RAW)** en de juiste gekopieerde URL's vanuit Azure Portal verzenden naar [het ondersteuningsteam van MS Azure SSO Access for Ethidex Compliance Office™](mailto:support@ethidex.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Een testgebruiker maken voor MS Azure SSO Access for Ethidex Compliance Office™

In deze sectie maakt u een gebruiker met de naam B.Simon in MS Azure SSO Access for Ethidex Compliance Office™. Neem contact op met het [ondersteuningsteam van MS Azure SSO Access for Ethidex Compliance Office™](mailto:support@ethidex.com) om de gebruikers toe te voegen in het MS Azure SSO Access for Ethidex Compliance Office™-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel MS Azure SSO Access for Ethidex Compliance Office™ in het toegangsvenster klikt, wordt u automatisch aangemeld bij de app MS Azure SSO Access for Ethidex Compliance Office™ waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [MS Azure SSO Access for Ethidex Compliance Office™ proberen met Azure AD](https://aad.portal.azure.com/)