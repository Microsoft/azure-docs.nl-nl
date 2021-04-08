---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Watch by Colors | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Watch by Colors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636779"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Watch by Colors

In deze zelfstudie leert u hoe u Watch by Colors kunt integreren met Azure Active Directory (Azure AD). Wanneer u Watch by Colors integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Watch by Colors.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Watch by Colors.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Watch by Colors waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Watch by Colors ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-watch-by-colors-from-the-gallery"></a>Watch by Colors toevoegen vanuit de galerie

Om de integratie van Watch by Colors te configureren in Azure AD, moet u Watch by Colors vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Watch by Colors** in het zoekvak.
1. Selecteer **Watch by Colors** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Watch by Colors

Configureer en test eenmalige aanmelding van Azure AD voor Watch by Colors met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Watch by Colors.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Watch by Colors te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Watch by Colors configureren](#configure-watch-by-colors-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Een testgebruiker voor Watch by Colors maken](#create-watch-by-colors-test-user)** : als u een tegenhanger van B. Simon in Watch by Colors wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Watch by Colors**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.colorscorporation.com/login`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot Watch by Colors.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Watch by Colors** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-watch-by-colors-sso"></a>Eenmalige aanmelding configureren voor Watch by Colors

1. Als u de configuratie met Watch by Colors wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Watch by Colors instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Watch by Colors-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Watch by Colors. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Watch by Colors handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Watch by Colors-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik in de rechterbovenhoek van de pagina op **Profile** > **Account Settings** > **SSO (Single Sign On)** .

    ![Schermopname met de pagina Account Settings, waarop SSO is uitgeschakeld.](./media/watch-by-colors-tutorial/config01.png)

5. Voer op de pagina **SSO (Single Sign On)** de volgende stappen uit:

    ![Schermopname van het tabblad SAML Setup, waar u SAML kunt inschakelen.](./media/watch-by-colors-tutorial/config02.png)

    a. Zet **Enable SAML** op **ON**.

    b. Plak in het tekstvak **URL** de waarde van **URL voor federatieve metagegevens** die u uit Azure Portal hebt gekopieerd.

    c. Klik op **Import**. De volgende velden worden automatisch ingevuld op de pagina.

    d. Klik op **Opslaan**.

### <a name="create-watch-by-colors-test-user"></a>Testgebruiker maken voor Watch by Colors

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Watch by Colors, moeten ze worden ingericht bij Watch by Colors. In het geval van Watch by Colors is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beveiligingsbeheerder aan bij Watch by Colors.

1. Klik in de rechterbovenhoek van de pagina op **Profile** > **Users** > **Add User**.

    ![Schermopname met de pagina Users.](./media/watch-by-colors-tutorial/config03.png)

1. Voer op de pagina **User Details** de volgende stappen uit:

    ![Schermopname van User Details, waar u de beschreven waarden kunt invoeren.](./media/watch-by-colors-tutorial/config04.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Voer in het tekstvak **Password** het wachtwoord in.

    e. Selecteer **accountmachtigingen** volgens uw organisatie.

    f. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Watch by Colors klikt, wordt u automatisch aangemeld bij het exemplaar van Watch by Colors waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Watch by Colors met Azure AD uitproberen](https://aad.portal.azure.com/)