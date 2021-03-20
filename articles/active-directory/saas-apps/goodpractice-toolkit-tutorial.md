---
title: 'Zelfstudie: Azure Active Directory-integratie met Mind Tools Toolkit | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Mind Tools Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 57598ea22874cfba772f6cf0e6b2ba779ef7c482
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92448376"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Zelfstudie: Azure Active Directory-integratie met Mind Tools Toolkit

In deze zelfstudie leert u hoe u Mind Tools Toolkit integreert met Azure Active Directory (Azure AD).

Met deze integratie kunt u het volgende doen:

* Bepalen in Azure AD wie er toegang heeft tot Mind Tools Toolkit.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Mind Tools Toolkit (eenmalige aanmelding).
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Mind Tools Toolkit te configureren:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Mind Tools Toolkit met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mind Tools Toolkit ondersteunt door SP geïnitieerde eenmalige aanmelding.
* Mind Tools Toolkit biedt ondersteuning voor het Just In Time inrichten van gebruikers.
* Nadat u Mind Tools Toolkit hebt geconfigureerd, kunt u sessiebeheer afdwingen. Deze vorm van beheer beschermt in real-time tegen exfiltratie en infiltratie van gevoelige gegevens van uw organisatie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Lees meer over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Mind Tools Toolkit toevoegen vanuit de galerie

Om de integratie van Mind Tools Toolkit in Azure AD te configureren, moet u Mind Tools Toolkit vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatievenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer **Mind Tools Toolkit** in.
1. Selecteer **Mind Tools Toolkit** in de zoek resultaten en voeg vervolgens de app toe. Wacht een paar seconden totdat de app aan uw tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding bij Mind Tools Toolkit met Azure AD op basis van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als er een gekoppelde relatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Mind Tools Toolkit tot stand is gebracht.

Als u eenmalige aanmelding bij Mind Tools Toolkit met Azure AD wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding met Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
1. **[Eenmalige aanmelding configureren voor Mind Tools Toolkit](#configure-mind-tools-toolkit-sso)** om de instellingen voor eenmalige aanmelding aan de clientzijde te configureren.
    1. **[Een testgebruiker voor Mind Tools Toolkit maken](#create-a-mind-tools-toolkit-test-user)** om een tegenhanger voor B.Simon te hebben in Mind Tools Toolkit. Deze tegenhanger is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding met Azure AD configureren

In dit gedeelte configureert u eenmalige aanmelding bij Mind Tools Toolkit met Azure AD door deze stappen te volgen:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **Mind Tools Toolkit** en selecteer **Eenmalige aanmelding**.

    ![Het gedeelte Beheren, met Eenmalige aanmelding gemarkeerd](common/select-sso.png)

1. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de optie **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![Het dialoogvenster Selecteer een methode voor eenmalige aanmelding, met SAML gemarkeerd](common/select-saml-option.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

    ![De pagina Eenmalige aanmelding instellen met SAML, met het potloodpictogram voor Standaard-SAML-configuratie gemarkeerd](common/edit-urls.png)

1. Ga naar **Standaard-SAML-configuratie** en typ in het vak **Aanmeldings-URL** een URL met het patroon `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`.

    > [!NOTE]
    > De waarde voor **Aanmeldings-URL** is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Mind Tools Toolkit](mailto:support@goodpractice.com) om de waarde op te vragen.

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** naar het gedeelte **SAML-handtekeningcertificaat**. Selecteer rechts van **XML-bestand met federatieve metagegevens** de optie **Downloaden** om de XML-tekst te downloaden en op uw computer op te slaan. De XML-inhoud is afhankelijk van de opties die u selecteert.

    ![Het gedeelte SAML-handtekeningcertificaat, met Downloaden gemarkeerd naast XML-bestand met federatieve metagegevens](common/metadataxml.png)

1. Kopieer in het gedeelte **Mind Tools Toolkit instellen** de URL's die u nodig hebt.

    * **Aanmeldings-URL**

    * **Azure AD-id**

    * **Afmeldings-URL**

    ![Het gedeelte Mind Tools Toolkit instellen, met de configuratie-URL's gemarkeerd](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal:

1. Selecteer helemaal links in de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer boven aan het scherm **Nieuwe gebruiker**.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Typ **B.Simon** in het veld **Naam**.  
   1. Voer in het veld **Gebruikersnaam** **B.Simon@** _bedrijfsdomein_ **.** _extensie_ in. Bijvoorbeeld B.Simon@contoso.com.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u B.Simon toestemming voor het gebruik van eenmalige aanmelding met Azure door hem of haar toegang te geven tot Mind Tools Toolkit.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Mind Tools Toolkit** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar het gedeelte **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![Het venster Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Selecteer onder aan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-mind-tools-toolkit-sso"></a>Eenmalige aanmelding configureren voor Mind Tools Toolkit

Als u eenmalige aanmelding wilt configureren in **Mind Tools Toolkit**, stuurt u de gedownloade tekst uit het **XML-bestand met federatieve metagegevens** en de eerder gekopieerde URL's naar het [ondersteuningsteam van Mind Tools Toolkit](mailto:support@goodpractice.com). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Een testgebruiker voor Mind Tools Toolkit maken

In dit gedeelte gaat u in Mind Tools Toolkit een gebruiker maken met de naam B.Simon.

Mind Tools Toolkit ondersteunt Just-In-Time-inrichting. Deze functie is standaard ingeschakeld. U hoeft geen handelingen uit te voeren in dit gedeelte. Als er nog geen gebruiker bestaat in Mind Tools Toolkit, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Mind Tools Toolkit.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u de tegel Mind Tools Toolkit selecteert in de portal Mijn apps, wordt u automatisch aangemeld bij de Mind Tools Toolkit waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the My Apps portal](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot de portal Mijn apps) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Mind Tools Toolkit uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Mind Tools Toolkit beveiligen met geavanceerde zichtbaarheid en controles](/cloud-app-security/proxy-intro-aad)