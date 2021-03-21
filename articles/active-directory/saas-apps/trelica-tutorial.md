---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Trelica | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517749"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Trelica

In deze zelfstudie leert u hoe u Trelica kunt integreren met Azure Active Directory (Azure AD).

Met deze integratie kunt u het volgende doen:

* In Azure AD bepalen wie toegang krijgt tot Trelica.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Trelica.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Trelica met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Trelica ondersteunt door IDP geïnitieerde eenmalige aanmelding.
* Trelica biedt ondersteuning voor het Just-In-Time inrichten van gebruikers.
* Nadat u Trelica hebt geconfigureerd, kunt u sessiebeheer afdwingen. Deze vorm van beheer beschermt in real time tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Trelica uit de galerie toevoegen

Voor het configureren van de integratie van Trelica in Azure AD moet u Trelica uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatievenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer **Trelica** in.
1. Selecteer **Trelica** in de zoekresultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Trelica

Configureer en test eenmalige aanmelding van Azure AD met Trelica met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Trelica.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Trelica te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Trelica configureren](#configure-trelica-sso)** als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Een testgebruiker voor Trelica maken](#create-a-trelica-test-user)** om een tegenhanger voor B.Simon te hebben in Trelica. Deze tegenhanger is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **Trelica** en zoek het gedeelte **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![De pagina Eenmalige aanmelding instellen met SAML, met het potloodpictogram voor Standaard-SAML-configuratie gemarkeerd](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende waarden in:

    1. Voer in het vak **Id** de URL **https://app.trelica.com** in.

    1. Voer in het vak **Antwoord-URL** een URL met het patroon `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` in.

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de werkelijke antwoord-URL (ook wel de ACS genoemd).
    > U vindt deze door u aan te melden bij Trelica, en naar de [configuratiepagina met SAML-id-providers](https://app.trelica.com/Admin/Profile/SAML) te gaan (Beheerder > Account > SAML). Klik op de knop Kopiëren naast de **ASC-URL (Assertion Consumer Service)** om deze op het klembord te plaatsen, klaar om in het tekstvak **Antwoord-URL** te worden geplakt in Azure AD.
    > Lees de [Help-documentatie voor Trelica](https://docs.trelica.com/admin/saml/azure-ad) of neem contact op met het [ondersteuningsteam voor de Trelica-client](mailto:support@trelica.com) als u vragen hebt.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de knop Kopiëren om de **URL voor federatieve metagegevens van de app** te kopiëren. Sla deze URL op de computer op.

    ![Het gedeelte SAML-handtekeningcertificaat, met de knop Kopiëren gemarkeerd naast App-URL voor federatieve metagegevens](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linker deelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer bovenaan het scherm **Nieuwe gebruiker**.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Typ **B.Simon** in het veld **Naam**.
   1. Voer in het veld **Gebruikersnaam** **B.Simon@** _bedrijfsdomein_ **.** _extensie_ in. Bijvoorbeeld B.Simon@contoso.com.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Trelica.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Trelica** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar het gedeelte **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![Het venster Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers **B.Simon**. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-trelica-sso"></a>Eenmalige aanmelding configureren voor Trelica

Als u eenmalige aanmelding wilt configureren aan de **Trelica**-zijde, gaat u naar de [configuratiepagina met SAML-id-providers](https://app.trelica.com/Admin/Profile/SAML) (Beheerder > Account >SAML). Klik op de knop **Nieuw**. Voer **Azure AD** in bij Naam, en kies **Metagegevens van URL** voor Type metagegevens. Plak de **App-URL voor federatieve metagegevens** die u hebt opgehaald uit Azure AD, in het veld **URL voor metagegevens** in Trelica.

Lees de [Help-documentatie voor Trelica](https://docs.trelica.com/admin/saml/azure-ad) of neem contact op met het [ondersteuningsteam voor de Trelica-client](mailto:support@trelica.com) als u vragen hebt.

### <a name="create-a-trelica-test-user"></a>Een testgebruiker voor Trelica maken

Trelica ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. U hoeft geen handelingen uit te voeren in dit gedeelte. Als er nog geen gebruiker in Trelica bestaat, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u de tegel Trelica selecteert in de portal Mijn apps, wordt u automatisch aangemeld bij de Trelica waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the My Apps portal](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot de portal Mijn apps) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Trelica met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Trelica beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)