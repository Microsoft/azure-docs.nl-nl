---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Leapsome | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ddc8cce7b56e0d9d4b3dc33dc1ad2d8c16582841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458703"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Leapsome

In deze zelfstudie leert u hoe u Leapsome integreert met Azure Active Directory (Azure AD). Wanneer u Leapsome integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang heeft tot Leapsome.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Leapsome.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Leapsome waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Leapsome ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-leapsome-from-the-gallery"></a>Leapsome toevoegen uit de galerie

Om de integratie van Leapsome in Azure AD te configureren, moet u Leapsome uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Leapsome**.
1. Selecteer **Leapsome** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Leapsome

Configureer en test eenmalige aanmelding van Azure AD met Leapsome met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Leapsome.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Leapsome te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Leapsome configureren](#configure-leapsome-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker voor Leapsome maken](#create-leapsome-test-user)** : als u een tegenhanger van B.Simon in Leapsome wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Leapsome** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL: `https://www.leapsome.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > De bovenstaande waarden voor Antwoord-URL en Aanmeldings-URL zijn geen echte waarden. U werkt deze bij met de werkelijke waarden, zoals later in de zelfstudie wordt uitgelegd.

1. In de Leapsome-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Leapsome-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk | Naamruimte |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | titel | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | afbeelding | URL naar de afbeelding van de werknemer | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > De waarde voor het kenmerk ‘afbeelding’ is niet echt. Werk deze waarde bij met de werkelijke afbeeldings-URL. Neem contact op met het [klantenondersteuningsteam van Leapsome](mailto:support@leapsome.com) om deze waarde op te vragen.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Leapsome instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot Leapsome.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Leapsome** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-leapsome-sso"></a>Eenmalige aanmelding voor Leapsome configureren

1. Meld u in een ander browservenster als beveiligingsbeheerder aan bij Leapsome.

1. Klik rechts bovenaan op het instellingenpictogram en klik vervolgens op **Beheerdersinstellingen**.

    ![Leapsome instellen](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Klik in de linkermenubalk op **Eenmalige aanmelding** en voer op de pagina **Eenmalige aanmelding met SAML** de volgende stappen uit:

    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecteer **Eenmalige aanmelding met SAML inschakelen**.

    b. Kopieer de waarde van **Aanmeldings-URL (wijs uw gebruikers hiernaartoe om aanmelding te starten)** en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    c. Kopieer de waarde van **Antwoord-URL (ontvangt antwoord van uw identiteitsprovider)** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak **Aanmeldings-URL voor eenmalige aanmelding (geleverd door identiteitsprovider)** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Kopieer het certificaat dat u hebt gedownload uit de Azure-portal zonder `--BEGIN CERTIFICATE and END CERTIFICATE--`-opmerkingen en plak dit in het tekstvak **Certificaat (geleverd door identiteitsprovider)** .

    f. Klik op **INSTELLINGEN VOOR EENMALIGE AANMELDING BIJWERKEN**.

### <a name="create-leapsome-test-user"></a>Testgebruiker voor Leapsome maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Leapsome. Werk samen met het [ondersteuningsteam van Leapsome](mailto:support@leapsome.com) om de gebruikers die of het domein dat voor het Leapsome-platform in de whitelist moet(en) worden opgenomen, toe te voegen. Als het team het domein heeft toegevoegd, worden gebruikers automatisch ingericht voor het Leapsome-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Leapsome klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Leapsome waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Leapsome uitproberen met Azure AD](https://aad.portal.azure.com/)