---
title: 'Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met AcquireIO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AcquireIO.
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
ms.openlocfilehash: 5fe070bc1abe0592b3082c597c1812781335448a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673179"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met AcquireIO

In deze zelfstudie leert u hoe u AcquireIO kunt integreren met Azure Active Directory (Azure AD). Wanneer u AcquireIO integreert met Azure AD, kunt u het volgende:

* Bepaal in Azure AD wie toegang heeft tot AcquireIO.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij AcquireIO.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op AcquireIO waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AcquireIO biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-acquireio-from-the-gallery"></a>AcquireIO toevoegen vanuit de galerie

Om de integratie van AcquireIO in Azure AD te configureren, moet u AcquireIO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **AcquireIO** in het zoekvak in het gedeelte **Toevoegen vanuit de galerie**.
1. Selecteer **AcquireIO** in het deelvenster met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Eenmalige aanmelding van Azure AD configureren en testen voor AcquireIO

Configureer en test eenmalige aanmelding van Azure AD met AcquireIO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AcquireIO.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met AcquireIO te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij AcquireIO configureren](#configure-acquireio-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor AcquireIO maken](#create-acquireio-test-user)** : als u een tegenhanger van B.Simon in AcquireIO wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **AcquireIO** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > De waarde is niet echt. U krijgt de daadwerkelijke antwoord-URL die verderop wordt beschreven in de sectie **AcquireIO configureren** van deze zelfstudie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **AcquireIO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot AcquireIO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AcquireIO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-acquireio-sso"></a>Eenmalige aanmelding voor AcquireIO configureren

1. Als u de configuratie in AcquireIO wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **AcquireIO instellen**, waarmee u naar de AcquireIO-toepassing wordt geleid. Geef hier de Administrator-referenties op om u aan te melden bij AcquireIO. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u AcquireIO handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan bij AcquireIO als beheerder.

1. Klik aan de linkerkant van het menu op **App Store**.

    ![Schermopname waarin App Store is gemarkeerd.](./media/acquireio-tutorial/config01.png)

1. Schuif omlaag naar **Active Directory** en klik op **installeren**.

    ![Schermopname waarin de sectie Active Directory en de knop Installeren zijn gemarkeerd.](./media/acquireio-tutorial/config02.png)

1. Voer in het pop-upvenster Active Directory de volgende stappen uit:

    ![Schermopname van het scherm Active Directory.](./media/acquireio-tutorial/config03.png)

    a. Klik op **Kopiëren** om de Antwoord-URL voor uw exemplaar te kopiëren en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    c. Open het met Base64 gecodeerde certificaat in Kladblok, kopieer de inhoud en plak het in het tekstvak **X.509 Certificate**.

    d. Klik op **Nu verbinden**.

### <a name="create-acquireio-test-user"></a>AcquireIO-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij AcquireIO, moeten ze worden ingericht in AcquireIO. In het geval van AcquireIO is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u in een ander browservenster bij AcquireIO aan als beveiligingsbeheerder.

1. Klik aan de linkerkant van het menu op **Profielen** en navigeer naar **Profiel toevoegen**.

    ![Schermopname met Profileren gemarkeerd in het menu aan de linkerkant van het scherm en Profiel toevoegen ook gemarkeerd.](./media/acquireio-tutorial/config04.png)

1. Voer de volgende stappen uit in het pop-upvenster **Klant toevoegen**:

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config05.png)

    a. Voer in het tekstvak **Name** de naam van de gebruiker in, bijvoorbeeld **B.simon**.

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals **B.simon@contoso.com** .

    c. Klik op **Submit**

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel AcquireIO in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van AcquireIO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer AcquireIO met Azure AD](https://aad.portal.azure.com/)