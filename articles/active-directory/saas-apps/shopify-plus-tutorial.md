---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Shopify Plus | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en Shopify Plus configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: d7a5d160fa8a0209478fe81d2880e8956828350d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993496"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Shopify Plus

In deze zelfstudie leert u hoe u Shopify Plus kunt integreren met Azure Active Directory (Azure AD). Wanneer u Shopify Plus integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot Shopify Plus.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Shopify Plus.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Shopify Plus-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Shopify Plus ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* Nadat u Shopify Plus hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>Shopify Plus toevoegen vanuit de galerie

Voor het configureren van de integratie van Shopify Plus met Azure AD moet u Shopify Plus vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Shopify Plus** in het zoekvak in de sectie **Toevoegen vanuit de galerie**.
1. Selecteer **Shopify Plus** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Eenmalige aanmelding via Azure AD configureren en testen voor Shopify Plus

Configureer en test eenmalige aanmelding van Azure AD met Shopify Plus met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Shopify Plus.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Shopify Plus te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding in Shopify Plus configureren](#configure-shopify-plus-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Shopify Plus-testgebruiker maken](#create-shopify-plus-test-user)** : als u een equivalent van B.Simon in Shopify Plus wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Shopify Plus** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://shopify.plus/login`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met [het ondersteuningsteam van Shopify Plus](mailto:plus-user-management@shopify.com) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Shopify Plus-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing Shopify Plus nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---- | --------------- |
    | e-mail | user.mail |

1. Wijzig de notatie van de **Naam-id** in **Permanent**. Selecteer de optie **Unieke gebruikers-id (Naam-id)** en selecteer vervolgens de notatie **Naam-id**. Selecteer **Permanent** voor deze optie. Sla uw wijzigingen op.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de knop Kopiëren om de **URL voor federatieve metagegevens van de app** te kopiëren. Sla deze URL op de computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Shopify Plus.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Shopify Plus** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-shopify-plus-sso"></a>Eenmalige aanmelding configureren voor Shopify Plus

Zie [Shopify-documentatie over het instellen van SAML-integraties](https://help.shopify.com/en/manual/shopify-plus/saml) voor een overzicht van de volledige stappen.

Als u eenmalige aanmelding wilt configureren voor de zijde **Shopify plus**, kopieert u de **Metagegevens-URL van de app-federatie** van Azure Active Directory. Meld u vervolgens aan bij de [organisatiebeheerder](https://shopify.plus) en ga naar **Gebruikers** > **Beveiliging**. Selecteer **Configuratie instellen** en plak vervolgens uw metagegevens-URL voor de app-federatie in de sectie **Metagegevens-URL van de identiteitsprovider**. Selecteer **Toevoegen** om deze stap te voltooien.

### <a name="create-shopify-plus-test-user"></a>Een Shopify Plus-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam B.Simon in Shopify Plus. Ga terug naar de sectie **Gebruikers** en voeg een gebruiker toe door het e-mailadres en de machtigingen van de gebruiker in te voeren. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="enforce-saml-authentication"></a>SAML-verificatie afdwingen

> [!NOTE]
> Het is raadzaam om de integratie te testen door afzonderlijke gebruikers te gebruiken voordat u dit breed toepast.

Afzonderlijke gebruikers:
1. Ga naar de pagina van een afzonderlijke gebruiker in Shopify Plus met een e-maildomein dat wordt beheerd door Azure AD en dat is geverifieerd in Shopify Plus.
1. Selecteer in de sectie SAML-verificatie **Bewerken**, selecteer **Vereist** en selecteer vervolgens **Opslaan**.
1. Test of deze gebruiker zich kan aanmelden via de door idP geïnitieerde en door SP geïnitieerde stromen.

Voor alle gebruikers onder een e-maildomein:
1. Ga terug naar de pagina **Beveiliging**.
1. Selecteer **Vereist** voor de instelling van uw SAML-verificatie. Hiermee wordt SAML afgedwongen voor alle gebruikers met dat e-maildomein op Shopify Plus.
1. Selecteer **Opslaan**.

> [!IMPORTANT]
> Het inschakelen van SAML voor alle gebruikers onder een e-maildomein is van invloed op alle gebruikers die deze toepassing gebruiken. Gebruikers kunnen zich niet aanmelden met behulp van hun reguliere aanmeldingspagina. Ze hebben alleen toegang tot de app via Azure Active Directory. Shopify biedt geen aanmeldings-URL als back-up waarmee gebruikers zich kunnen aanmelden met hun normale gebruikersnaam en wachtwoord. U kunt contact opnemen met Shopify-ondersteuning om SAML uit te schakelen indien nodig.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Shopify Plus klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Shopify Plus waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Shopify Plus proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Shopify Plus beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)