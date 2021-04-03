---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met PurelyHR | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PurelyHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met PurelyHR

In deze zelfstudie leert u hoe u PurelyHR integreert met Azure AD (Azure Active Directory). Wanneer u PurelyHR integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie er toegang heeft tot PurelyHR.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij PurelyHR.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* een PurelyHR-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PurelyHR ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)
* PurelyHR biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-purelyhr-from-the-gallery"></a>PurelyHR uit de galerie toevoegen

Als u de integratie van PurelyHR in Azure AD wilt configureren, moet u PurelyHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **PurelyHR**.
1. Selecteer **PurelyHR** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Eenmalige aanmelding van Azure AD configureren en testen voor PurelyHR

Configureer en test eenmalige aanmelding van Azure AD met PurelyHR met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PurelyHR.

Voltooi de volgende stappen om eenmalige aanmelding bij PurelyHR met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij PurelyHR configureren](#configure-purelyhr-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Testgebruiker voor PurelyHR maken](#create-purelyhr-test-user)** : als u een equivalent van B.Simon in PurelyHR wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **PurelyHR** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<companyID>.purelyhr.com/sso-consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het ondersteuningsteam van de PurelyHR-client](https://support.purelyhr.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **PurelyHR instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot PurelyHR.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **PurelyHR** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-purelyhr-sso"></a>Eenmalige aanmelding voor PurelyHR configureren

1. Als u de configuratie in PurelyHR wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **PurelyHR instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de PurelyHR-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij PurelyHR. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u PurelyHR handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van PurelyHR. Voer hierna de volgende stappen uit:

1. Open het **Dashboard** vanuit de opties in de werkbalk en klik op **SSO-instellingen**.

1. Plak de waarden in de vakken zoals hieronder beschreven.

    ![Eenmalige aanmelding configureren](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Open het **Certificaat (Bas64)** dat u gedownload heeft van het Azure-portaal in Kladblok en kopieer de waarde van het certificaat. Plak de gekopieerde waarde in het vak **X.509-certificaat**.

    b. Plak de **Azure AD-id** die u in de Azure-portal hebt gekopieerd in het vak **Idp Issuer URL**.

    c. Plak in het vak **IDP Endpoint URL** de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd. 

    d. Schakel het selectievakje **Automatisch gebruikers maken** in om automatisch gebruikers inrichten in PurelyHR in te schakelen.

    e. Klik op **Wijzigingen opslaan** om de instellingen op te slaan.

### <a name="create-purelyhr-test-user"></a>PurelyHR-testgebruiker maken

Deze stap is meestal niet vereist omdat de toepassing just-in-time gebruikersinrichting ondersteunt. Als de automatische inrichting van gebruikers niet is ingeschakeld, dan kunt u gebruikers handmatig inrichten zoals hieronder beschreven.

Meld u als beheerder aan bij uw Velpic SAML-bedrijfssite en voer de volgende stappen uit:

1. Klik op het tabblad Beheren, ga naar de sectie Gebruikers en klik vervolgens op de knop Nieuw om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. In het dialoogvenster **'Nieuwe gebruiker maken'** voert u de volgende stappen uit.

    ![gebruiker](./media/velpicsaml-tutorial/velpic_8.png)

    a. Typ in het tekstvak **Voornaam** de voornaam B.

    b. Typ in het tekstvak **Achternaam** de achternaam Simon.

    c. Typ in het tekstvak **Gebruikersnaam** de gebruikersnaam B.Simon.

    d. Typ in het tekstvak **E-mail** het e-mailadres van het B.Simon@contoso.com-account.

    e. De rest van de informatie is optioneel. u kunt deze invullen indien nodig.

    f. Klik op **OPSLAAN**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van PurelyHR klikt, zou u automatisch moeten worden aangemeld bij de instantie van PurelyHR waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [PurelyHR met Azure AD uitproberen](https://aad.portal.azure.com/)