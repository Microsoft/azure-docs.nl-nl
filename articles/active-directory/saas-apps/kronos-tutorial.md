---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Kronos | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Kronos configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: cdb72eb857f60400e8f7c23bbd1af39df802df76
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968905"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kronos"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) bij Azure Active Directory met Kronos

In deze zelfstudie leert u hoe u Kronos kunt integreren met Azure Active Directory (Azure AD). Wanneer u Kronos integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Kronos.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Kronos.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Kronos waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kronos biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-kronos-from-the-gallery"></a>Kronos toevoegen vanuit de galerie

Om de integratie van Kronos te configureren in Azure AD, moet u Kronos vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In het gedeelte **Toevoegen uit de galerie**  voert u **Kronos** in het zoekvak in.
1. Selecteer **Kronos** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-kronos"></a>Eenmalige aanmelding van Azure AD voor Kronos configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Kronos met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kronos.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Kronos te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Kronos configureren](#configure-kronos-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Kronos maken](#create-kronos-test-user)** : als u een tegenhanger van B.Simon in Kronos wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal op de integratiepagina van de toepassing **Kronos** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<company name>.kronos.net/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van Kronos](https://www.kronos.in/contact/en-in/form) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De Kronos-toepassing verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

1. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikerskenmerken en claims toont met het pictogram Bewerken geselecteerd.](./media/kronos-tutorial/tutorial_usermail.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](./media/kronos-tutorial/tutorial_usermailedit.png)

    b. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    c. Selecteer **user.userprincipalname** in de lijst **Parameter 1**.

    d. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **Kronos instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In dit gedeelte geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Kronos.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Kronos** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-kronos-sso"></a>Eenmalige aanmelding voor Kronos configureren

Als u eenmalige aanmelding aan de zijde van **Kronos** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Kronos](https://www.kronos.in/contact/en-in/form). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-kronos-test-user"></a>Een testgebruiker maken voor Kronos

In deze sectie gaat u in Kronos een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van Kronos](https://www.kronos.in/contact/en-in/form) om de gebruikers toe te voegen in het Kronos-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op Deze toepassing testen. U wordt automatisch aangemeld bij het exemplaar van Kronos waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Kronos klikt in Mijn apps, wordt u automatisch aangemeld bij het exemplaar van Kronos waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Kronos hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)