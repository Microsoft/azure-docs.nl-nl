---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Raketa | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511151"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) via Azure Active Directory met Raketa

In deze zelfstudie leert u hoe u Raketa kunt integreren met Azure Active Directory (Azure AD). Wanneer u Raketa integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie toegang heeft tot Raketa.
* Uw gebruikers in staat stellen om zich automatisch met hun Azure AD-account aanmelden bij Raketa.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Raketa-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Raketa ondersteunt door **SP** geïnitieerde eenmalige aanmelding.
* Zodra u Raketa hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Raketa toevoegen vanuit de galerie

Voor het configureren van de integratie van Raketa met Microsoft Azure Active Directory moet u Raketa vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Ga naar **Bedrijfstoepassingen** [2] en selecteer vervolgens **Alle toepassingen** [3].

1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. Typ **Raketa** in het zoekvak [6] in het gedeelte **Toevoegen vanuit de galerie** [5].

1. Selecteer **Raketa** in de resultaten [7] en klik vervolgens op de knop **Toevoegen** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Eenmalige aanmelding via Azure AD configureren en testen voor Raketa

Configureer en test eenmalige aanmelding van Azure AD met Raketa met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Raketa.

Voltooi de volgende stappen om eenmalige aanmelding bij Raketa met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Raketa configureren](#configure-raketa-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Raketa-testgebruiker maken](#create-raketa-test-user)** : als u een equivalent van B.Simon in Raketa wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Raketa** de sectie **Beheren** en selecteer **Eenmalige aanmelding** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Selecteer **SAML** [10]op de pagina **Selecteer een methode voor eenmalige aanmelding** [9].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** [11] om de instellingen te bewerken.

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. Typ de URL `https://raketa.travel/` in de tekstvakken **Id (entiteits-id)** [12] en **Aanmeldings-URL** [14].

    1. In het tekstvak **Antwoord-URL** [13 ]typt u een URL met het volgende patroon: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>`.  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [Raketa-ondersteuningsteam](mailto:help@raketa.travel) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **Certificaat (Base64)** en selecteer **Downloaden** [15] om het certificaat te downloaden en op te slaan op de computer.

1. In de sectie **Raketa instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    1. Aanmeldings-URL [16]: de URL van de webpagina voor autorisatie, die wordt gebruikt om de gebruikers om te leiden naar het verificatiesysteem.

    1. Azure AD-id [17]: de id van Azure AD.

    1. Afmeldings-URL [18]: de URL van de webpagina, die wordt gebruikt om de gebruikers na afmelding om te leiden.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory** [1], selecteer **Gebruikers** [19] en selecteer vervolgens **Alle gebruikers** [20].

1. Selecteer **Nieuwe gebruiker** [21] bovenaan het scherm.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:

   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** [22] in. Bijvoorbeeld `B.Simon@contoso.com`.

   1. Voer `B.Simon` in het veld **Naam** [23] in.

   1. Schakel het selectievakje **Wachtwoord weergeven** [25] in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord** [24].

   1. Klik op **Maken** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Raketa.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** [2] en selecteer vervolgens **Alle toepassingen** [3].

1. Selecteer **Raketa** [27] in de lijst met toepassingen.  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Selecteer **Gebruiker toevoegen** [29] en selecteer vervolgens **Gebruikers en groepen** [30] in het dialoogvenster **Toewijzing toevoegen**.

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de gebruiker **B.Simon** [31] in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** [32] onderaan het scherm.

1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.

1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>SSO configureren voor Raketa

Als u eenmalige aanmelding aan de zijde van **Raketa** wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Raketa](mailto:help@raketa.travel). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-raketa-test-user"></a>Raketa-testgebruiker maken

In dit gedeelte maakt u in Raketa een gebruiker met de naam B.Simon. Neem contact op met het [ondersteuningsteam van Raketa](mailto:help@raketa.travel) om de gebruikers toe te voegen aan het Raketa-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Raketa in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Raketa waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Raketa proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Raketa beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)