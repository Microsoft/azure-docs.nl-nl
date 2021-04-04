---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met askSpoke | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en askSpoke.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 8ef2dadfc0099822d68a5b1cfb2cc35759e1a866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457653"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met askSpoke

In deze zelfstudie leert u hoe u askSpoke integreert met Azure Active Directory (Azure AD). Wanneer u askSpoke integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot askSpoke.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij askSpoke.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* askSpoke SSO-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* askSpoke biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* askSpoke ondersteunt het **Just-In-Time** inrichten van gebruikers
* Zodra u askSpoke hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-askspoke-from-the-gallery"></a>askSpoke uit de galerie toevoegen

Als u de integratie van askSpoke in Azure AD wilt configureren, moet u askSpoke vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **askSpoke** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **askSpoke** in het paneel met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-askspoke"></a>Eenmalige aanmelding van Azure AD configureren en testen voor askSpoke

Configureer en test eenmalige aanmelding van Azure AD met askSpoke met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in askSpoke.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met askSpoke te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[askSpoke-eenmalige aanmelding configureren](#configure-askspoke-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een askSpoke-testgebruiker maken](#create-askspoke-test-user)** : als u een equivalent van B.Simon in askSpoke wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **askSpoke** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.askspoke.com/saml/callback`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de werkelijke antwoord-URL. Neem contact op met het [askSpoke-ondersteuningsteam](mailto:support@askspoke.com) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://askspoke.com/login`

1. In de askSpoke-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing askSpoke nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------| --------- |
    | voornaam | user.givenname |
    | achternaam | user.surname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **askSpoke instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot askSpoke.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **askSpoke** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-askspoke-sso"></a>Eenmalige aanmelding voor askSpoke configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van askSpoke.

1. Klik in het linkernavigatievenster op **Settings**.

    ![Het tabblad Instellingen in askSpoke](./media/askspoke-tutorial/configure1.png)

1. Schuif omlaag naar **SSO** en klik op **Connect**.

    ![Verbinding maken met askSpoke](./media/askspoke-tutorial/configure2.png)

1. Voer in de sectie **Enable SAML & SCIM** de volgende stappen uit:

    ![Het kopje SAML en SCIM inschakelen in askSpoke](./media/askspoke-tutorial/configure3.png)

    1. Plak in het tekstvak **Sign on URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    1. Plak in het tekstvak **Issuer** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    1. Open het bestand **Certificate(Base64)** dat u vanuit Azure Portal hebt gedownload in Kladblok, kopieer de inhoud van het certificaatbestand en plak deze in het tekstvak **Public certificate**.

    1. Kopieer de waarde van **ACS URL** en plak deze in het tekstvak **Reply URL** in de sectie **Basic SAML Configuration** in Azure Portal.

    1. Klik op **Test SAML connection**.

### <a name="create-askspoke-test-user"></a>askSpoke-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in askSpoke. askSpoke biedt ondersteuning voor Just-In-Time inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in askSpoke bestaat, wordt er een nieuwe gemaakt wanneer u askSpoke probeert te openen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel askSpoke in het toegangsvenster klikt, wordt u automatisch aangemeld bij het askSpoke-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [askSpoke proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)