---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Teamphoria | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92504740"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Teamphoria

In deze zelfstudie leert u hoe u Teamphoria integreert met Azure Active Directory (Azure AD). Wanneer u Teamphoria integreert met Azure AD, kunt u:

* U kunt in Azure AD beheren wie toegang heeft tot Teamphoria.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Teamphoria.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Teamphoria SSO-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Teamphoria ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria toevoegen vanuit de galerie

Als u de integratie van Teamphoria in Azure AD wilt configureren, moet u Teamphoria vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Teamphoria**.
1. Selecteer **Teamphoria** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Azure AD-eenmalige aanmelding configureren en testen voor Teamphoria

Configureer en test Azure AD-eenmalige aanmelding met Teamphoria met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Teamphoria.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Teamphoria te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Teamphoria configureren](#configure-teamphoria-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een Teamphoria-testgebruiker maken](#create-teamphoria-test-user)** : als u een equivalent van B.Simon in Teamphoria wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Teamphoria** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Teamphoria](https://www.teamphoria.com/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Teamphoria instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Teamphoria.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Teamphoria** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-teamphoria-sso"></a>Eenmalige aanmelding van Teamphoria configureren

1. Als u de configuratie in Teamphoria wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Teamphoria instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Teamphoria-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Teamphoria. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Teamphoria handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Teamphoria-bedrijfssite. Voer hierna de volgende stappen uit:

4. Ga naar de optie **BEHEERDERSINSTELLINGEN** op de linkerwerkbalk en klik op het tabblad Configureren op **EENMALIGE AANMELDING** om het SSO-configuratievenster te openen.

    ![Schermopname toont BEHEERINSTELLINGEN. Hier kunt u EENMALIGE AANMELDING selecteren.](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Klik rechtsboven op de optie **NIEUWE ID-PROVIDER TOEVOEGEN** om het formulier te openen voor het toevoegen van de SSO-instellingen.

    ![Schermopname waarin wordt getoond waar u NIEUWE ID-PROVIDER TOEVOEGEN kunt selecteren.](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Voer de details in de velden in, zoals hieronder wordt beschreven.

    ![Schermopname van de pagina waarin u de beschreven waarden kunt invoeren.](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **WEERGAVENAAM**: Voer op de pagina Beheer de weergavenaam van de invoegtoepassing in.

    b. **KNOPNAAM**: De naam van het tabblad dat wordt weergegeven op de aanmeldingspagina voor aanmelding via eenmalige aanmelding.

    c. **CERTIFICAAT**: Open het certificaat dat u eerder hebt gedownload van de Azure-portal, in Kladblok, kopieer de inhoud en plak dit hier in het vak.

    d. **INGANGSPUNT**: Plak de **aanmeldings-URL** die u eerder uit de Azure-portal hebt gekopieerd.

    e. Schakel de optie **in** en klik op **OPSLAAN**.

### <a name="create-teamphoria-test-user"></a>Teamphoria-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Teamphoria, moeten ze worden ingericht in Teamphoria. In het geval van Teamphoria moet dit handmatig gebeuren.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van Teamphoria.

1. Klik op **BEHEERINSTELLINGEN** op de linkerwerkbalk en klik op het tabblad **BEHEREN** op **GEBRUIKERS** om de pagina Beheer voor gebruikers te openen.

    ![Werknemer toevoegen](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klik op de optie **HANDMATIG UITNODIGEN**.

    ![Schermopname met de optie HANDMATIG UITNODIGEN.](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Voer op deze pagina de volgende actie uit.

    ![Schermopname met de pagina HANDMATIG GEBRUIKERS UITNODIGEN, waar u de naam en het e-mailadres kunt invoeren.](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Voer in het tekstvak **E-MAILADRES** het **e-mailadres** van de gebruiker in, bijvoorbeeld B.Simon.

    b. Voer in het tekstvak **VOORNAAM** de voornaam van de gebruiker in, bijvoorbeeld **B**.

    c. Voer in het tekstvak **ACHTERNAAM** de achternaam van de gebruiker in, bijvoorbeeld **Simon**.

    d. Klik op **1 GEBRUIKER UITNODIGEN**. De gebruiker moet de uitnodiging accepteren om te worden gemaakt in het systeem.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Teamphoria klikt, wordt u automatisch aangemeld bij de instantie van Teamphoria waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Teamphoria uitproberen met Azure AD](https://aad.portal.azure.com/)