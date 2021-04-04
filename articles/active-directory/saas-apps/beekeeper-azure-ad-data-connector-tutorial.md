---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) met Azure Active Directory met Beekeeper Azure AD SSO | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Beekeeper Azure AD SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.openlocfilehash: 9f8ff45869c636aead6f76d4e461b96facefe2fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457347"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beekeeper-azure-ad-sso"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Beekeeper Azure AD SSO

In deze zelfstudie leert u hoe u Beekeeper Azure AD SSO kunt integreren met Azure Active Directory (Azure AD). Wanneer u Beekeeper Azure AD SSO integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure Active Directory beheren wie toegang heeft tot Beekeeper Azure AD SSO.
* Instellen dat uw gebruikers automatisch worden aangemeld bij Beekeeper Azure AD SSO met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Beekeeper Azure AD SSO waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Beekeeper Azure AD SSO ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)
* Beekeeper Azure AD SSO ondersteunt het **Just-In-Time** inrichten van gebruikers
* Zodra u Beekeeper Azure AD SSO hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beveiligd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-beekeeper-azure-ad-sso-from-the-gallery"></a>Beekeeper Azure AD SSO toevoegen vanuit de galerie

Voor de configuratie van de integratie van Beekeeper Azure AD SSO in Azure Active Directory, moet u Beekeeper Azure AD SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Beekeeper Azure AD SSO** in het zoekvak.
1. Selecteer **Beekeeper Azure AD SSO** in de zoekresultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beekeeper-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Beekeeper Azure AD SSO

Configureer en test eenmalige aanmelding van Azure AD met Beekeeper Azure AD SSO met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Beekeeper Azure AD SSO.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met Beekeeper Azure AD SSO te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Beekeeper Azure AD SSO configureren](#configure-beekeeper-azure-ad-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
    * **[Testgebruiker maken in Beekeeper Azure AD SSO](#create-beekeeper-azure-ad-sso-test-user)** : om in Beekeeper Azure AD SSO een tegenhanger van B.Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Beekeeper Azure AD SSO** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u een **Service Provider-metagegevensbestand** hebt en in de door **IDP** geïnitieerde modus wilt configureren:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<your_company>.beekeeper.io/login`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het klantondersteuningsteam van Beekeeper Azure AD SSO](mailto:support@beekeeper.io) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Beekeeper Azure AD SSO-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/default-attributes.png)

1. Bovendien verwacht de Beekeeper Azure AD SSO-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | firstname | user.givenname |
    | lastname | user.surname |
    | e-mail | user.mail |
    | gebruikersnaam | user.principalname |
    | position | user.jobtitle |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Beekeeper Azure AD SSO instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot Beekeeper Azure AD SSO.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Beekeeper Azure AD SSO** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-beekeeper-azure-ad-sso"></a>Beekeeper Azure AD SSO configureren

Als u eenmalige aanmelding aan de zijde van **Beekeeper Azure AD SSO** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van Beekeeper Azure AD SSO](mailto:support@beekeeper.io). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-beekeeper-azure-ad-sso-test-user"></a>Testgebruiker maken voor Beekeeper Azure AD SSO

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Beekeeper Azure AD SSO. Beekeeper Azure AD SSO ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Beekeeper Azure AD SSO, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Beekeeper Azure AD SSO klikt, wordt u automatisch aangemeld bij het exemplaar van Beekeeper Azure AD SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Eenmalige aanmelding (SSO) bij Beekeeper met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)