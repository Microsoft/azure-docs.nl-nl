---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SmartDraw | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: b4b9aa16dfc474575bd522238f06fe487508e7e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SmartDraw

In deze zelfstudie leert u hoe u SmartDraw integreert met Azure Active Directory (Azure AD). Wanneer u SmartDraw integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD wie toegang heeft tot SmartDraw.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SmartDraw.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een SmartDraw-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SmartDraw biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* SmartDraw biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-smartdraw-from-the-gallery"></a>SmartDraw toevoegen vanuit de galerie

Om de integratie van SmartDraw te configureren in Azure AD, moet u SmartDraw uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **SmartDraw**.
1. Selecteer **SmartDraw** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SmartDraw

Configureer en test eenmalige aanmelding van Azure AD met SmartDraw met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SmartDraw.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SmartDraw te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij SmartDraw configureren](#configure-smartdraw-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een SmartDraw-testgebruiker maken](#create-smartdraw-test-user)** : als u een equivalent van B.Simon in SmartDraw wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SmartDraw** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Vervang de waarde voor de aanmeldings-URL door de echte URL. Dit wordt later in de zelfstudie uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op **Opslaan**.

1. De SmartDraw-toepassing verwacht dat de SAML-asserties een specifieke indeling hebben. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing SmartDraw nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Groepen | user.groups |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **SmartDraw instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SmartDraw.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **SmartDraw**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-smartdraw-sso"></a>Eenmalige aanmelding voor SmartDraw configureren

1. Als u de configuratie in SmartDraw wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **SmartDraw instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de SmartDraw-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij SmartDraw. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u SmartDraw handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de SmartDraw-bedrijfssite. Voer daarna de volgende stappen uit:

1. Klik op **Eenmalige aanmelding** onder Uw SmartDraw-licentie beheren.

    ![Schermopname van het dialoogvenster voor het beheren van uw SmartDraw-licentie waar u eenmalige aanmelding kunt selecteren.](./media/smartdraw-tutorial/configure01.png)

1. Voer de volgende stappen uit op de pagina Configuratie:

    ![Schermopname van de configuratiepagina waarin u de beschreven waarden kunt invoeren.](./media/smartdraw-tutorial/configure02.png)

    a. Typ uw domein in het tekstvak **Uw domein (bijvoorbeeld acme.com)** .

    b. Kopieer **Uw door SP geïnitieerde aanmeldings-URL** is voor uw exemplaar en plak deze in het tekstvak Aanmeldings-URL in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    c. Typ **Iedereen** in het tekstvak **Beveiligingsgroepen voor het toestaan van toegang tot SmartDraw**.

    d. Plak in het tekstvak **Uw URL SAML-uitgever** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    e. Open in Kladblok het XML-bestand met metagegevens dat u hebt gedownload vuit de Azure-portal, kopieer de inhoud en plak deze in het vak **Uw SAML-metagegevens**.

    f. Klik op **Configuratie opslaan** 

### <a name="create-smartdraw-test-user"></a>SmartDraw-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in SmartDraw. SmartDraw biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in SmartDraw bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SmartDraw in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van SmartDraw waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer SmartDraw met Azure AD](https://aad.portal.azure.com/)