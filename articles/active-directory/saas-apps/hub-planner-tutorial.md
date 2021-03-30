---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Hub Planner | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Hub Planner.
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
ms.openlocfilehash: 2d9dc483a9d60dc395c0aff52b721690d4fc701d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442419"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Hub Planner

In deze zelfstudie leert u hoe u Hub Planner integreert met Azure Active Directory (Azure AD). Wanneer u Hub Planner integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot Hub Planner.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Hub Planner.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Hub Planner-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Hub Planner ondersteunt door **SP** geïnitieerde eenmalige aanmelding.
* Zodra u Hub Planner hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-hub-planner-from-the-gallery"></a>Hub Planner toevoegen vanuit de galerie

Voor het configureren van de integratie van Hub Planner met Microsoft Azure Active Directory moet u Hub Planner vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** de tekst **Hub Planner** in het zoekvak.
1. Selecteer **Hub Planner** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Hub Planner

Configureer en test eenmalige aanmelding van Azure AD met Hub Planner met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Hub Planner.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Hub Planner te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Hub Planner configureren](#configure-hub-planner-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Hub Planner-testgebruiker maken](#create-hub-planner-test-user)** : als tegenhanger van B.Simon in Hub Planner die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Hub Planner**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.hubplanner.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://app.hubplanner.com/sso/metadata`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.hubplanner.com/sso/callback`

    > [!NOTE]
    > Dit zijn de waarden die u gaat gebruiken. De enige wijziging die u hoeft aan te brengen, is \<SUBDOMAIN\> in de **Aanmeldings-URL** vervangen door het subdomein dat u hebt ontvangen tijdens het registreren bij Hub Planner. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Hub Planner instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Hub Planner.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Hub Planner** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-hub-planner-sso"></a>Eenmalige aanmelding voor Hub Planner configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van **Hub Planner**, moet u zich aanmelden bij uw Hub Planner-account en de volgende taken voltooien. 

### <a name="install-the-extension-in-hub-planner"></a>De uitbreiding installeren in Hub Planner

Als u SSO-functionaliteit wilt inschakelen, moet u eerst de uitbreiding inschakelen. Als eigenaar van het account of met gelijkwaardige machtigingen, voert u de volgende stappen uit:

1. Ga naar **Settings**.
1. Selecteer in het menu aan de zijkant **Uitbreidingen beheren** > **Extensies toevoegen/verwijderen**.
1. Zoek de uitbreiding voor eenmalige aanmelding en Toevoegen of Gratis uitproberen.
1. Wanneer u hierom wordt gevraagd, gaat u akkoord met de voorwaarden en selecteert u **Nu toevoegen**.

### <a name="enable-sso"></a>SSO inschakelen

Nadat de uitbreiding is ingeschakeld, moet u SSO inschakelen voor uw account. 

1. Ga naar **Settings**.
1. Selecteer in het menu aan de zijkant **Verificatie**.
1. Selecteer **SSO (eenmalige aanmelding)** .
1. Voer aanvullende verificatie gegevens in, zoals wordt weergegeven in de volgende afbeelding, en selecteer **Opslaan**.

![Schermopname van SSO-instellingen](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Een Hub Planner-testgebruiker maken

Als u andere gebruikers wilt toevoegen, gaat u naar **Instellingen** > **Resources beheren** en gebruikers toevoegen. Zorg ervoor dat u het e-mailadres toevoegt en ze vervolgens uitnodigt. Zodra ze zijn uitgenodigd, ontvangen ze een e-mail en kunnen ze via SSO toegang krijgen. 

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Hub Planner in het toegangsvenster klikt, wordt u automatisch aangemeld bij het Hub Planner-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Hub Planner met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Hub Planner beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)