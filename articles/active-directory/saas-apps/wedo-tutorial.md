---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met WEDO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en WEDO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 529c4e6433d16f9d70530ba516b5ec1426806984
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519228"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met WEDO

In deze zelfstudie leert u hoe u WEDO kunt integreren met Azure Active Directory (Azure AD). Wanneer u WEDO integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot WEDO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij WEDO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op WEDO waarvoor eenmalige aanmelding is ingeschakeld. Neem contact op met het [ondersteuningsteam van WEDO](mailto:info@wedo.swiss) om een abonnement voor eenmalige aanmelding aan te vragen.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* WEDO ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* [Zodra u WEDO hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Meer informatie over het afdwingen van sessiebeheer met WEDO](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>WEDO toevoegen vanuit de galerie

Voor het configureren van de integratie van WEDO in Azure Active Directory, moet u WEDO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **WEDO** in het zoekvak.
1. Selecteer **WEDO** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Eenmalige aanmelding van Azure AD configureren en testen voor WEDO

Configureer en test eenmalige aanmelding van Azure AD met WEDO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in WEDO.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met WEDO te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij WEDO configureren](#configure-wedo-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor WEDO maken](#create-wedo-test-user)** : als u een tegenhanger van B.Simon in WEDO wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **WEDO** en selecteer in de sectie **Beheren** de optie **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van WEDO](mailto:info@wedo.swiss) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In WEDO worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.email |
    | voornaam | user.firstName |
    | achternaam | user.lasttName |
    | userName | user.userName |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **WEDO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot WEDO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **WEDO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wedo-sso"></a>Eenmalige aanmelding configureren voor WEDO

Volg deze stappen om eenmalige aanmelding met Azure AD in te schakelen voor WEDO.

1. Meld u aan bij [WEDO](https://login.wedo.swiss/). U moet de **rol van beheerder** hebben.
1. Selecteer in de profielinstellingen het menu **Authentication** in de sectie **Network settings**.
1. Voer de volgende stappen uit op de pagina **SAML Authentication**:

   ![SAML-verificatie](media/wedo-tutorial/network-security-authentification.png)

   a. Selecteer **Enable SAML Authentication**.

   b. Selecteer het tabblad **Identity Provider metadata (XML)** .

   c. Open in Kladblok het **XML-bestand met federatieve gegevens dat** u hebt gedownload uit de Azure-portal en kopieer de inhoud naar het tekstvak **X.509 Certificate**.

   d. Klik op **Opslaan**

### <a name="create-wedo-test-user"></a>Testgebruiker maken voor WEDO

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in WEDO. De gegevens moeten overeenkomen met die uit *Een Azure AD-testgebruiker maken*.

1. Ga naar de profielinstellingen in WEDO en selecteer **Users** in de sectie *Network settings*.
1. Klik op **Gebruiker toevoegen**.
1. Vul in de pop-up Add user de gegevens van de gebruiker in.

    a. First name `B`.

    b. Last name `Simon`.

    c. Voer het e-mailadres `username@companydomain.extension` in. Bijvoorbeeld `B.Simon@contoso.com`. U moet hier een e-mailadres opgeven met hetzelfde domein als de korte naam van uw bedrijf.

    d. User type `User`.

    e. Klik op **Gebruiker maken**.

    f. Klik op de pagina *Select teams* op **Save**.

    g.  Klik op de pagina *Invite user* op **Yes**.

1. Valideer de gebruiker met behulp van de link die u per e-mail hebt ontvangen.

> [!NOTE]
> Als u een fictieve gebruiker wilt maken (het hierboven opgegeven e-mailadres bestaat niet in uw netwerk), neemt u contact op met [onze support](mailto:info@wedo.swiss) om de gebruiker te valideren*.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel WEDO klikt, zou u automatisch moeten worden aangemeld bij de instantie van WEDO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [WEDO uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [WEDO beveiligen met geavanceerde zichtbaarheid en controles](/cloud-app-security/proxy-intro-aad)