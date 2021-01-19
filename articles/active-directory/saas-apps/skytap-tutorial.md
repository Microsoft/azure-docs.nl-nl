---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Eenmalige aanmelding voor Skytap | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Eenmalige aanmelding voor Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108140"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Eenmalige aanmelding voor Skytap

In deze zelfstudie leert u hoe u Eenmalige aanmelding voor Skytap kunt integreren met Azure Active Directory (Azure AD). Wanneer u Single Sign-on for Skytap integreert met Azure AD, kunt u het volgende doen:

* In Azure AD controleren wie toegang heeft tot Eenmalige aanmelding voor Skytap.
* U kunt instellen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Single Sign-on voor Skytap.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Eenmalige aanmelding voor Skytap waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Eenmalige aanmelding voor Skytap biedt ondersteuning voor door SP en IDP geïnitieerde eenmalige aanmelding.
* Nadat u Eenmalige aanmelding voor Skytap hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dit biedt realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Eenmalige aanmelding voor Skytap toevoegen vanuit de galerie

Voor het configureren van de integratie van Eenmalige aanmelding voor Skytap in Azure AD, moet u Eenmalige aanmelding voor Skytap vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Eenmalige aanmelding voor Skytap** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Eenmalige aanmelding voor Skytap** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Eenmalige aanmelding voor Skytap

Configureer en test eenmalige aanmelding van Azure AD met Eenmalige aanmelding voor Skytap met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Eenmalige aanmelding voor Skytap.

Hier volgen de algemene stappen voor het configureren en testen van eenmalige aanmelding van Azure AD met Eenmalige aanmelding voor Skytap:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.

    b. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Skytap configureren](#configure-single-sign-on-for-skytap-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.

    a. **[Maak een testgebruiker voor Eenmalige aanmelding voor Skytap](#create-single-sign-on-for-skytap-test-user)** om een equivalent van B. Simon te hebben in Eenmalige aanmelding voor Skytap. Deze tegenhanger is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure Portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Eenmalige aanmelding voor Skytap** en zoek de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van de pagina Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `http://pingone.com/<custom EntityID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Selecteer optioneel **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. In het tekstvak **Relaystatus** typt u een URL met het volgende patroon: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL, aanmeldings-URL en relaystatus. Neem contact op met het [ondersteuningsteam van Eenmalige aanmelding voor Skytap](mailto:support@skytap.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** in het gedeelte **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens**. Selecteer **Downloaden** om het metagegevensbestand te downloaden en op uw computer op te slaan.

    ![Schermopname van de downloadkoppeling voor het certificaat](common/metadataxml.png)

1. In de sectie **Eenmalige aanmelding voor Skytap instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Schermopname van Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot Eenmalige aanmelding voor Skytap.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Eenmalige aanmelding voor Skytap** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers **B.Simon**. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Eenmalige aanmelding voor Skytap configureren

Als u eenmalige aanmelding aan de zijde van Eenmalige aanmelding voor Skytap wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Microsoft Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Eenmalige aanmelding voor Skytap](mailto:support@skytap.com). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Testgebruiker maken voor Eenmalige aanmelding voor Skytap

In deze sectie maakt u in Eenmalige aanmelding voor Skytap een gebruiker met de naam B.Simon. Werk samen met het [klantondersteuningsteam van Eenmalige aanmelding voor Skytap](mailto:support@skytap.com) om de gebruikers toe te voegen aan het platform van Eenmalige aanmelding voor Skytap. U kunt eenmalige aanmelding pas gebruiken als u gebruikers hebt gemaakt en geactiveerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Eenmalige aanmelding voor Skytap klikt, wordt u automatisch aangemeld bij het exemplaar van Eenmalige aanmelding voor Skytap waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Slack uitproberen met Azure AD](https://aad.portal.azure.com/)
