---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Druva | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Druva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: a7868d702ff3f1190d7f51e4ad7316508d453015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454521"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Druva

In deze zelfstudie leert u hoe u Druva kunt integreren met Azure Active Directory (Azure AD). Wanneer u Druva integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Druva.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Druva.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Druva waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Druva biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding
* Zodra u eenmalige aanmelding voor Druva hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-druva-from-the-gallery"></a>Druva uit de galerie toevoegen

Om de integratie van Druva in Azure Active Directory te configureren, moet u Druva uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Druva**.
1. Selecteer **Druva** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Druva

Configureer en test eenmalige aanmelding van Azure AD met Druva met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Druva.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Druva te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Druva configureren](#configure-druva-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Druva maken](#create-druva-test-user)** : als u een tegenhanger van B.Simon in Druva wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Druva**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ de waarde van de tekenreeks in het tekstvak **Id (Entiteits-id)** : `DCP-login`.
    
    b. Typ in het tekstvak **Antwoord-URL (URL voor Assertion Consumer Service)** de volgende URL: `https://cloud.druva.com/wrsaml/consume`.

1. Klik op **Opslaan**.

1. In de Druva-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Druva-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | SSO-token gegenereerd uit DCP-beheerconsole, zonder aanhalingstekens.  Bijvoorbeeld: X-XXXXX-XXXX-V-O-O-R-B-E-E-L-D+TXOXKXEXNX=. Azure voegt automatisch aanhalingstekens rondom het verificatietoken toe. |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Druva instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Druva.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Druva**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-druva-sso&quot;></a>Eenmalige aanmelding voor Druva configureren

1. Meld u in een ander browservenster aan bij uw bedrijfssite van Druva als een beheerder.

1. Klik op het Druva-logo in de linkerbovenhoek en klik vervolgens op **Druva Cloud-instellingen**.

    ![Instellingen](./media/druva-tutorial/ic795091.png &quot;Instellingen")

1. Klik op het tabblad **Eenmalige aanmelding** op **Bewerken**.

    ![Schermopname met het tabblad "Access Settings - Single Sign-On" met de knop "Edit" geselecteerd.](./media/druva-tutorial/ic795092.png "Instellingen voor eenmalige aanmelding")

1. Voer op de pagina **Instellingen voor eenmalige aanmelding bewerken** de volgende stappen uit:

    ![Instellingen voor eenmalige aanmelding](./media/druva-tutorial/ic795095.png "Instellingen voor eenmalige aanmelding")

    1. Plak in het tekstvak **Aanmeldings-URL van ID-provier** de waarde van de **Aanmeldings-URL** in die u hebt gekopieerd uit de Azure Portal.

    1. Open uw base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze naar het tekstvak **Certificaat ID-provider**

       > [!NOTE]
       > Om eenmalige aanmelding in te schakelen voor beheerders, schakelt u de selectievakjes **Beheerders melden zich aan bij Druva Cloud via provider van eenmalige aanmelding** en **Faalveilige toegang voor Druva Cloud-beheerders toestaan (aanbevolen)** in. Druva raadt aan **Faalveilig voor beheerders** in te schakelen, zodat ze toegang tot de DCP-console moeten krijgen in geval van eventuele fouten in IdP. Op die manier kunnen beheerders ook zowel een wachtwoord voor eenmalige aanmelding als een DCP-wachtwoord gebruiken om toegang tot de DCP-console te krijgen.

    1. Klik op **Opslaan**. Er kan nu toegang tot Druva Cloud Platform worden verkregen met behulp van eenmalige aanmelding.

### <a name="create-druva-test-user"></a>Druva-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Druva. Druva biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Druva bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Druva in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Druva waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Druva uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)