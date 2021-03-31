---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met NS1 SSO voor Azure | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en NS1 SSO voor Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met NS1 SSO voor Azure

In deze zelfstudie leert u hoe u NS1 SSO voor Azure kunt integreren met Azure AD (Azure Active Directory). Wanneer u NS1 SSO voor Azure integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot NS1 SSO voor Azure.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij NS1 SSO voor Azure.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op NS1 SSO voor Azure waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* NS1 SSO voor Azure ondersteunt door SP en IDP geïnitieerde eenmalige aanmelding.
* Nadat u NS1 SSO voor Azure hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dit biedt realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO voor Azure toevoegen vanuit de galerie

Om de integratie van NS1 SSO voor Azure in Azure AD te configureren, moet u NS1 SSO voor Azure uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **NS1 SSO voor Azure**.
1. Selecteer **NS1 SSO voor Azure** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Eenmalige aanmelding van Azure AD configureren en testen voor NS1 SSO voor Azure

Configureer en test eenmalige aanmelding van Azure AD met NS1 SSO voor Azure met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in NS1 SSO voor Azure.

Hier volgen de algemene stappen voor het configureren en testen van eenmalige aanmelding van Azure AD met NS1 SSO voor Azure:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.

    b. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer eenmalige aanmelding bij NS1 SSO voor Azure SSO](#configure-ns1-sso-for-azure-sso)** als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.

    a. **[Maak een NS1 SSO voor Azure-testgebruiker](#create-an-ns1-sso-for-azure-test-user)** als u in NS1 SSO voor Azure een tegenhanger van B.Simon wilt hebben. Deze tegenhanger is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **NS1 SSO voor Azure** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u de volgende URL: `https://api.nsone.net/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://api.nsone.net/saml/sso/<ssoid>`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de volgende URL: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde van de antwoord-URL bij met de werkelijke antwoord-URL. Neem contact op met het [clientondersteuningsteam van NS1 SSO voor Azure](mailto:techops@nsone.net) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing NS1 SSO voor Azure verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken beheren vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing. Selecteer op de pagina **Eenmalige aanmelding met SAML instellen** het potloodpictogram om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname van de sectie Gebruikerskenmerken & Claims, waarbij het potloodpictogram is gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecteer de naam van het kenmerk om de claim te bewerken.

    ![Schermopname van de sectie Gebruikerskenmerken & Claims, waarbij de kenmerknaam is gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecteer **Transformatie**.

    ![Schermopname van de sectie Claim beheren, met Transformatie gemarkeerd](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Voer in het gedeelte **Transformatie beheren** de volgende stappen uit:

    ![Schermopname van de sectie Transformatie beheren, waarbij verschillende velden zijn gemarkeerd](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecteer **ExactMailPrefix()** als **Transformatie**.

    1. Selecteer **user.userprincipalname** als **Parameter 1**.

    1. Selecteer **Toevoegen**.

    1. Selecteer **Opslaan**.

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en selecteer in de sectie **SAML-handtekeningcertificaat** de knop Kopiëren. Hiermee wordt de URL van de **App-URL voor federatieve metagegevens** gekopieerd en op uw computer opgeslagen.

    ![Schermopname van het SAML-handtekeningcertificaat, met de kopieerknop gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:

   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot NS1 SSO voor Azure.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **NS1 SSO voor Azure**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers **B.Simon**. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO voor Azure SSO configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van NS1 SSO voor Azure, moet u de URL voor de federatieve metagegevens van de app verzenden naar het ondersteuningsteam van [NS1 SSO voor Azure](mailto:techops@nsone.net). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Een testgebruiker maken voor NS1 SSO voor Azure

In deze sectie maakt u een gebruiker met de naam B.Simon in NS1 SSO voor Azure. Werk met het ondersteuningsteam van NS1 SSO voor Azure om de gebruikers toe te voegen aan het NS1 SSO voor Azure-platform. U kunt eenmalige aanmelding pas gebruiken als u gebruikers hebt gemaakt en geactiveerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel NS1 SSO voor Azure in het Toegangsvenster selecteert, wordt u automatisch aangemeld bij de NS1 SSO voor Azure waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangsvenster](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [NS1 SSO voor Azure proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)