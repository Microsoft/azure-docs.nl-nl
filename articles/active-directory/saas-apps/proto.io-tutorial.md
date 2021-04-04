---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Proto.io | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Proto.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: cdd712495d09f45054d046693833a7f6efee2c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-protoio"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Proto.io

In deze zelfstudie leert u hoe u Proto.io kunt integreren met Azure Active Directory (Azure AD). Door Proto.io te integreren met Azure AD kunt u het volgende doen:

* In Azure AD regelen wie toegang tot Proto.io heeft.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Proto.io.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Proto.io-abonnement met eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Proto.io ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)

* Zodra u Proto.io hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-protoio-from-the-gallery"></a>Proto.io toevoegen vanuit de galerie

Om de integratie van Proto.io in Azure AD te configureren, moet u Proto.io uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Proto.io** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Proto.io** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-protoio"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Proto.io

Configureer en test eenmalige aanmelding van Azure AD met Proto.io met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Proto.io.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Proto.io te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Proto.io configureren](#configure-protoio-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Proto.io maken](#create-protoio-test-user)** : als u een tegenhanger van B.Simon in Proto.io wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Proto.io** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://proto.io/saml/sp/<PROTO-SAML-ACCOUNT-ID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://saml.proto.io/login/callback?code=<PROTO-SAML-ACCOUNT-ID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<PROTO-SAML-ACCOUNT-ID>.proto.io`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [client-ondersteuningsteam van Proto.io](mailto:support@proto.io) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Proto.io-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Proto.io nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | e-mail | user.mail |
    | fname | user.givenname |
    | lname | user.surname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Proto.io instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Proto.io.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Proto.io** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-protoio-sso"></a>SSO voor Proto.io configureren

Als u eenmalige aanmelding aan de zijde van **Proto.io**  wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam voor Proto.io](mailto:support@proto.io). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-protoio-test-user"></a>Testgebruiker voor Proto.io maken

In dit gedeelte gaat u in Proto.io een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Proto.io](mailto:support@proto.io) om de gebruikers toe te voegen in het Proto.io-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Proto.io in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Proto.io waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Proto.io met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Proto.io beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)