---
title: 'Zelfstudie: Azure Active Directory SSO-integratie (eenmalige aanmelding) met Darwinbox | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: 70c77caebfd8f9bfd36c7384255cf7b66416a379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012030"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Zelfstudie: Azure Active Directory SSO-integratie (eenmalige aanmelding) met Darwinbox

In deze zelfstudie leert u hoe u Darwinbox kunt integreren met Azure Active Directory (Azure AD). Wanneer u Darwinbox integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot Darwinbox.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij Darwinbox.
* Uw accounts op een centrale locatie beheren: Azure Portal.
Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Darwinbox-abonnement met eenmalige aanmelding (SSO) ingeschakeld.
> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.


## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Darwinbox ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-darwinbox-from-the-gallery"></a>Darwinbox toevoegen uit de galerie

Voor het configureren van de integratie van Darwinbox in Azure AD, moet u Darwinbox uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **Darwinbox**.
1. Selecteer **Darwinbox** in het deelvenster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-darwinbox"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Darwinbox

Configureer en test eenmalige aanmelding van Azure AD met Darwinbox met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Darwinbox.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Darwinbox te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Darwinbox configureren](#configure-darwinbox-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Darwinbox-testgebruiker maken](#create-darwinbox-test-user)** : als u een tegenhanger van B.Simon in Darwinbox wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Darwinbox** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

   1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.darwinbox.in/`

   1. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOMID>`

      > [!NOTE]
      > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantenondersteuningsteam van Darwinbox](https://darwinbox.com/contact-us.php) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Darwinbox instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Darwinbox.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Darwinbox** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-darwinbox-sso"></a>Eenmalige aanmelding voor Darwinbox configureren

Als u eenmalige aanmelding aan de zijde van **Darwinbox** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Darwinbox](https://darwinbox.com/contact-us.php). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-darwinbox-test-user"></a>Darwinbox-testgebruiker maken

In deze sectie maakt u in Darwinbox een gebruiker met de naam B. Simon. Neem contact op met het [ondersteuningsteam van Darwinbox](https://darwinbox.com/contact-us.php) om de gebruikers toe te voegen in het Darwinbox-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Darwinbox in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Darwinbox waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-darwinbox-mobile"></a>Eenmalige aanmelding voor Darwinbox (mobiel) testen

1. Open de mobiele app Darwinbox. Klik op **URL van organisatie invoeren** en voer de URL van uw organisatie in het tekstvak in. Klik op de knop met de pijl.

    ![Schermopname die de mobiele app 'Darwinbox' toont met het selectie vakje 'URL van organisatie invoeren' geselecteerd, en een voorbeeld van een organisatie en de knop met de pijl gemarkeerd.](media/darwinbox-tutorial/DarwinboxMobile01.jpg)

1. Als u meerdere domeinen hebt, klikt u op uw domein.

    ![Schermopname van het venster 'Domein kiezen' met een voorbeeld van een domein.](media/darwinbox-tutorial/DarwinboxMobile02.jpg)

1. Voer uw Azure AD-e-mail in de Darwinbox-toepassing in en klik op **Volgende**.

    ![Schermopname van het venster 'Aanmelden' met de knop 'Volgende' gemarkeerd.](media/darwinbox-tutorial/DarwinboxMobile03.jpg)

1. Voer uw Azure AD-wachtwoord in de Darwinbox-app in en klik op **Volgende**.

    ![Schermopname van het venster 'Wachtwoord invoeren' met de knop 'Volgende' gemarkeerd.](media/darwinbox-tutorial/DarwinboxMobile04.jpg)

1. Als het aanmelden is gelukt, wordt de startpagina van de toepassing weergegeven.

    ![Mobiele app Darwinbox](media/darwinbox-tutorial/DarwinboxMobile05.jpg)

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer Darwinbox met Azure AD](https://aad.portal.azure.com/)