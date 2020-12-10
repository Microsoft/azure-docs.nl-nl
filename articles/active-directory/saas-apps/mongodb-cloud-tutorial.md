---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MongoDB Cloud | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding tussen Azure Active Directory en MongoDB Cloud configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: af1c0702929e7cd700c8d19ab24e40f9c6f43a21
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602169"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met MongoDB Cloud

In deze zelfstudie leert u hoe u MongoDB Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u MongoDB Cloud integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot MongoDB Cloud, MongoDB Atlas, de MongoDB-community, MongoDB University en MongoDB-ondersteuning.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij MongoDB Cloud.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een MongoDB Cloud-organisatie waarvoor eenmalige aanmelding is ingeschakeld. U kunt u aanmelden voor een [gratis cluster](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MongoDB Cloud biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding.
* MongoDB Cloud biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers.
* Zodra u MongoDB Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app) voor meer informatie.

## <a name="add-mongodb-cloud-from-the-gallery"></a>MongoDB Cloud toevoegen vanuit de galerie

Voor het configureren van de integratie van MongoDB Cloud in Azure Active Directory, moet u MongoDB Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **MongoDB Cloud** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **MongoDB Cloud** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Eenmalige aanmelding van Azure AD configureren en testen voor MongoDB Cloud

Configureer en test eenmalige aanmelding van Azure AD met MongoDB Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MongoDB Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met MongoDB Cloud te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij MongoDB Cloud configureren](#configure-mongodb-cloud-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Een MongoDB Cloud-testgebruiker maken](#create-a-mongodb-cloud-test-user) als u een equivalent van B.Simon in MongoDB Cloud wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **MongoDB Cloud** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteuningsteam van MongoDB Cloud](https://support.mongodb.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. MongoDB Cloud verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van standaardkenmerken](common/default-attributes.png)

1. Naast de voorgaande kenmerken verwacht MongoDB Cloud nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | e-mail | user.userprincipalname |
    | voornaam | user.givenname |
    | achternaam | user.surname |

1. Ga op de pagina **Eenmalige aanmelding instellen met SAML** in het gedeelte **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens**. Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de koppeling Downloaden gemarkeerd](common/metadataxml.png)

1. Kopieer in de sectie **MongoDB Cloud instellen** de juiste URL's, op basis van uw vereiste.

    ![Schermopname van de sectie MongoDB Cloud instellen, met URL’s gemarkeerd](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot MongoDB Cloud.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **MongoDB Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-mongodb-cloud-sso"></a>Eenmalige aanmelding voor MongoDB Cloud configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van MongoDB Cloud, moet u de juiste URL's hebben gekopieerd uit Azure Portal. U moet ook de federatietoepassing configureren voor uw MongoDB Cloud-organisatie. Volg de instructies in de [documentatie van MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Neem contact op met het [ondersteuningsteam van MongoDB Cloud](https://support.mongodb.com/) als u een probleem ondervindt.

### <a name="create-a-mongodb-cloud-test-user"></a>Een MongoDB Cloud-testgebruiker maken

MongoDB Cloud biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. U hoeft geen verdere actie te ondernemen. Als er nog geen gebruiker in MongoDB Cloud bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel MongoDB Cloud in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van MongoDB Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Aanmelden voor MongoDB Atlas in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mdb_atlas_oct2020?tab=Overview)

- [MongoDB Cloud gebruiken met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [MongoDB Cloud beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)