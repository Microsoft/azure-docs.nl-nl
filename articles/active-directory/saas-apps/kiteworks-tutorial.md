---
title: 'Zelfstudie: Azure Active Directory-integratie met Kiteworks | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Kiteworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 55c1208e7a689e88d9db7fb352c556b5eca00437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458962"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Zelfstudie: Kiteworks integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Kiteworks kunt integreren met Azure AD (Active Directory). Wanneer u Kiteworks integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Kiteworks.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Kiteworks.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een Kiteworks-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kiteworks biedt ondersteuning voor met **SP** geïnitieerde SSO
* Kiteworks biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-kiteworks-from-the-gallery"></a>Kiteworks toevoegen vanuit de galerie

Als u de integratie van Kiteworks in Azure AD wilt configureren, moet u Kiteworks vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Kiteworks**.
1. Selecteer **Kiteworks** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Kiteworks met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Kiteworks.

Als u eenmalige aanmelding van Azure AD met Kiteworks wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Kiteworks configureren](#configure-kiteworks-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Kiteworks maken](#create-kiteworks-test-user)** : als u een tegenhanger van Britta Simon in Kiteworks wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Kiteworks** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<kiteworksURL>.kiteworks.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Kiteworks](https://accellion.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Kiteworks instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>Eenmalige aanmelding met Kiteworks configureren

1. Meld u als beheerder aan bij de bedrijfssite van Kiteworks.

1. Klik in de werkbalk bovenaan op **Settings** (Instellingen).

    ![Schermopname waarin het pictogram 'Settings' op de werkbalk is geselecteerd.](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. Klik in de sectie **Verificatie en autorisatie** op **Eenmalige aanmelding instellen**.

    ![Schermopname met de instelling 'SSO Setup' geselecteerd in de sectie 'Verificatie en autorisatie'.](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. Voer op de pagina Eenmalige aanmelding instellen de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. Selecteer **Verifiëren via eenmalige aanmelding**.

    b. Selecteer **AuthnRequest initiëren**.

    c. Plak in het tekstvak **IDP-entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    d. Plak in het tekstvak **URL van de service Eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    e. Plak in het tekstvak **URL van de service Eenmalige afmelding** de waarde van **Afmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    f. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Openbaar RSA-sleutelcertificaat**.

    g. Klik op **Opslaan**.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Kiteworks.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Kiteworks** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kiteworks-test-user"></a>Een testgebruiker voor Kiteworks maken

Het doel van deze sectie is om in Kiteworks een gebruiker met de naam Britta Simon te maken.

Kiteworks biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Kiteworks, wordt er een nieuwe gemaakt na tijdens een poging om toegang te krijgen.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het [ondersteuningsteam van Kiteworks](https://accellion.com/support).

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Kiteworks klikt, wordt u automatisch aangemeld bij de instantie van Kiteworks waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)