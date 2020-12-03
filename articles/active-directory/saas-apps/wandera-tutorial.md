---
title: 'Zelfstudie: Integratie van Azure Active Directory met Wandera RADAR Admin | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Wandera RADAR Admin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: d13619b818e18c64d9882f9e3181824173403859
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181387"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Zelfstudie: Wandera RADAR Admin integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Wandera RADAR Admin integreert met Azure AD (Active Directory). Wanneer u Wandera RADAR Admin integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Wandera RADAR Admin.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Wandera RADAR Admin.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Wandera RADAR Admin waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wandera RADAR Admin biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding
* Zodra u Wandera RADAR Admin hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Wandera RADAR Admin toevoegen vanuit de galerie

Als u de integratie van Wandera RADAR Admin wilt configureren in Azure AD, moet u Wandera RADAR Admin vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Wandera RADAR Admin**.
1. Selecteer **Wandera RADAR Admin** in het resultatenpaneel en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Wandera RADAR Admin met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Wandera RADAR Admin.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Wandera RADAR Admin te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Wandera RADAR Admin configureren](#configure-wandera-radar-admin-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
   * **[Een Wandera RADAR Admin-testgebruiker maken](#create-wandera-radar-admin-test-user)** : als u een tegenhanger van B.Simon in Wandera RADAR Admin wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Wandera RADAR Admin** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van Wandera RADAR Admin](https://www.wandera.com/about-wandera/contact/#supportsection) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **SAML-handtekeningcertificaat** om de instellingen te bewerken.

    ![Optie voor ondertekening](common/signing-option.png)

    1. Selecteer **Optie voor ondertekening** voor **SAML-antwoord en -assertie ondertekenen**.

    1. Selecteer een **Ondertekeningsalgoritme** als **SHA-256**.

1. Kopieer in de sectie **Wandera RADAR Admin instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Wandera RADAR Admin.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Wandera RADAR Admin** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wandera-radar-admin-sso"></a>Eenmalige aanmelding bij Wandera RADAR Admin configureren

1. Als u de configuratie in Wandera RADAR Admin wilt automatiseren, moet u **Mijn Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Wandera RADAR Admin instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Wandera RADAR Admin-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Wandera RADAR Admin. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Wandera RADAR Admin handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Wandera-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op **Instellingen** > **Beheer** > **Eenmalige aanmelding** en controleer vervolgens de optie **SAML 2.0 inschakelen** om de volgende stappen uit te voeren.

    ![Wandera RADAR Admin-configuratie](./media/wandera-tutorial/config01.png)

    a. Klik op **of voer de vereiste velden handmatig in**.

    b. Plak in het tekstvak voor de **IdP-entiteits-id** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    c. Open het XML-bestand met federatieve metagegevens in Kladblok, kopieer de inhoud en plak deze in het tekstvak **IdP Public X.509 Certificaat**.

    d. Klik op **Opslaan**.

### <a name="create-wandera-radar-admin-test-user"></a>Een Wandera RADAR Admin-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam B.Simon in Wandera RADAR Admin. Neem contact op met het [ondersteuningsteam van Wandera RADAR Admin](https://www.wandera.com/about-wandera/contact/#supportsection) om de gebruikers toe te voegen in het Wandera RADAR Admin-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Wandera RADAR Admin klikt, wordt u automatisch aangemeld bij de instellen van Wandera RADAR Admin waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)