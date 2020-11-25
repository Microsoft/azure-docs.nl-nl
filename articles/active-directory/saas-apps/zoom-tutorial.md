---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Zoom | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: 6b4484b9bb235b24d2b733c329b52ab6b04be183
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006418"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Zoom

In deze zelfstudie leert u hoe u Zoom kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zoom integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang heeft tot Zoom.
* U kunt instellen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Zoom.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Zoom-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoom biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding 
* Zoom biedt ondersteuning voor [**Geautomatiseerd** inrichten van gebruikers](./zoom-provisioning-tutorial.md).

## <a name="adding-zoom-from-the-gallery"></a>Zoom toevoegen vanuit de galerie

Voor het configureren van de integratie van Zoom in Azure AD moet u Zoom uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Zoom** in het zoekvak.
1. Selecteer **Zoom** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Zoom

Configureer en test eenmalige aanmelding van Azure AD met Zoom met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zoom.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Zoom te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding van Zoom configureren](#configure-zoom-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Zoom maken](#create-zoom-test-user)** : als u in Zoom een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) naar de toepassingsintegratiepagina van **Zoom**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.zoom.us`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `<companyname>.zoom.us`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Zoom-ondersteuningsteam](https://support.zoom.us/hc/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Zoom instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Zie [De rolclaim configureren die in het SAML-token is uitgegeven voor bedrijfstoepassingen](../develop/active-directory-enterprise-app-role-management.md) voor meer informatie over het configureren van rollen in Azure AD.

> [!NOTE]
> Zoom verwacht mogelijk een groepsclaim in de SAML-nettolading. Als u groepen hebt gemaakt, neemt u contact op met het [klantondersteuningsteam van Zoom](https://support.zoom.us/hc/). Geef de groepsgegevens op, zodat zij de groepsgegevens aan hun kant kunnen configureren. U moet ook de Object-id aan het [Zoom-ondersteuningsteam](https://support.zoom.us/hc/) melden zodat zij van hun kant een configuratie kunnen uitvoeren. Zie [Zoom configureren met Azure](https://support.zoom.us/hc/articles/115005887566) om de object-id op te halen.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Zoom.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zoom** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zoom-sso"></a>Eenmalige aanmelding van Zoom configureren

1. Meld u in een ander webbrowservenster aan als beheerder bij uw Zoom-bedrijfssite.

2. Klik op het tabblad **Eenmalige aanmelding**.

    ![Het tabblad Eenmalige aanmelding](./media/zoom-tutorial/zoom-sso1.png "Eenmalige aanmelding")

3. Klik op het tabblad **Beveiligingsbeheer** en ga vervolgens naar de instellingen voor **Eenmalige aanmelding**.

4. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![De sectie Eenmalige aanmelding](./media/zoom-tutorial/zoom-sso2.png "Eenmalige aanmelding")

    a. Plak in het tekstvak **Aanmeldingspagina-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    b. Voor de waarde voor de **Afmeldingspagina-URL** gaat u naar Azure Portal en klikt u aan de linkerkant op **Azure Active Directory**. Navigeer vervolgens naar **App-registraties**.

    ![De knop Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Klik op **Eindpunten**

    ![De knop Eindpunt](./media/zoom-tutorial/endpoint.png)

    d. Kopieer het **Afmeldingseindpunt van SAML-P** en plak dit in het tekstvak **Afmeldingspagina-URL**.

    ![De knop Eindpunt kopiëren](./media/zoom-tutorial/endpoint1.png)

    e. Open het base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak het in het tekstvak **Id-providercertificaat**.

    f. Plak in het tekstvak **Verlener** de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal. 

    g. Klik op **Wijzigingen opslaan**.

    > [!NOTE]
    > Ga naar de Zoom-documentatie voor meer informatie[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Zoom-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in Zoom. Zoom biedt ondersteuning voor het automatisch inrichten van gebruikers. Deze functie is standaard ingeschakeld. U kunt [hier](./zoom-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, neemt u contact op met het [klantondersteuningsteam van Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zoom in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Zoom waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Zoom proberen met Azure AD](https://aad.portal.azure.com/)