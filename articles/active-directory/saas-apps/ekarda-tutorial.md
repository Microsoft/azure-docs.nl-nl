---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met ekarda | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: b057d07e10676291f42a9a070e32cb17df672651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735148"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ekarda

In deze zelfstudie leert u hoe u ekarda integreert met Azure AD (Active Directory). Wanneer u ekarda integreert met Azure AD, kunt u het volgende:

* Beheren in Azure AD wie toegang heeft tot ekarda.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ekarda.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een ekarda-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ekarda biedt ondersteuning voor met SP en IDP geïnitieerde eenmalige aanmelding.
* ekarda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers.

## <a name="add-ekarda-from-the-gallery"></a>Ekarda toevoegen vanuit de galerie

Als u de integratie van ekarda in Azure AD wilt configureren, voegt u ekarda vanuit de galerie toe aan de lijst met beheerde SaaS-apps:

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om de nieuwe app toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **ekarda**.
1. Selecteer **ekarda** in het resultatenpaneel en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Eenmalige aanmelding van Azure AD configureren en testen voor ekarda

Configureer en test eenmalige aanmelding van Azure AD met ekarda met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ekarda.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met ekarda te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.

    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij Ekarda configureren](#configure-ekarda-sso): als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * [Een ekarda-testgebruiker maken](#create-an-ekarda-test-user): als u een equivalent van B.Simon in ekarda wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen in de Azure-portal om eenmalige aanmelding van Azure AD in te schakelen:

1. Meld u aan bij Azure Portal.
1. Ga op de integratiepagina van de toepassing **ekarda** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram bij **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd.](common/edit-urls.png)

1. Volg in de sectie **Standaard-SAML-configuratie** deze stappen als u het **Service Provider-metagegevensbestand** ziet:
    1. Selecteer **Metagegevensbestand uploaden**.
    1. Selecteer het mappictogram, selecteer het metagegevensbestand en selecteer vervolgens **Uploaden**.
    1. Wanneer het bestand met metagegevens is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch weergegeven in het tekstvak van de sectie ekarda.

    > [!Note]
    > Als de waarden voor **id** en **Antwoord-URL** niet automatisch worden weergegeven, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Als u het **Service Provider-metagegevensbestand** niet ziet in de sectie **Standaard-SAML-configuratie** en u de toepassing wilt configureren in de met IDP geïnitieerde modus, voert u waarden in de volgende velden in:

    1. Typ in het tekstvak **Id** een URL met het volgende patroon: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Typ in het tekstvak **Antwoord-URL** een URL met het volgende patroon: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Als u de toepassing wilt configureren in de met SP geïnitieerde modus, selecteert u **Extra URL's instellen**:

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > De waarden in de twee voorgaande stappen zijn niet echt. Werk ze bij met de waarden van de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van ekarda](mailto:contact@ekarda.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML**, in de sectie **SAML-handtekeningcertificaat**, op **Downloaden** om het **Certificaat (Base64)** te downloaden op de computer.

    ![Schermopname van de sectie SAML-handtekeningcertificaat van de pagina Eenmalige aanmelding instellen met SAML, met de downloadkoppeling gemarkeerd voor het Base64-certificaat.](common/certificatebase64.png)

1. Kopieer in de sectie **ekarda instellen** de juiste URL's op basis van uw behoeften.

    ![Schermopname van de sectie ekarda instellen van de pagina Eenmalige aanmelding instellen met SAML, met de kopieën van de URL-koppelingen gemarkeerd.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gebruikt u de Azure-portal om een testgebruiker te maken met de naam B.Simon.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Voer bijvoorbeeld `B.Simon@contoso.com` in.
   1. Schakel het selectievakje **Wachtwoord weergeven** in, en noteer de waarde die verschijnt in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ekarda.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **ekarda** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-ekarda-sso"></a>Eenmalige aanmelding bij ekarda configureren

1. Als u de configuratie in ekarda wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **ekarda instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de ekarda-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij ekarda. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u ekarda handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw ekarda-bedrijfssite.

1. Selecteer **Beheerder** > **Mijn account**.

    ![Schermopname van de gebruikersinterface van de ekarda-site, met Mijn account gemarkeerd in het menu Beheerder.](./media/ekarda-tutorial/ekarda.png)

1. Ga onderaan de pagina naar de sectie **SAML-INSTELLINGEN**. In deze sectie configureert u de SAML-integratie.
1. Volg deze stappen in de sectie **SAML-INSTELLINGEN**:

    ![Schermopname van de ekarda-pagina SAML-INSTELLINGEN, met SAML-configuratievelden gemarkeerd.](./media/ekarda-tutorial/ekarda1.png)

    1. Selecteer de koppeling **Service Provider-metagegevens** en sla het bestand op de computer op.
    1. Schakel het selectievakje **SAML inschakelen** in.
    1. Plak in het tekstvak **IDP-entiteits-id** de waarde van **Azure AD-id** die u eerder hebt gekopieerd in de Azure-portal.
    1. Plak in het tekstvak **IDP-aanmeldings-id** de waarde van **Aanmeldings-URL** die u eerder hebt gekopieerd in de Azure-portal.
    1. Plak in het tekstvak **IDP-afmeldings-id** de waarde van **Afmeldings-URL** die u eerder hebt gekopieerd in de Azure-portal.
    1. Open in Kladblok het **Certificaat (Base64)** dat u hebt gedownload in de Azure-portal. Plak deze inhoud in het tekstvak **IDP-x509-certificaat**.
    1. Schakel het selectievakje **SLO inschakelen** in de sectie **OPTIES** in.
    1. Selecteer **Update**.

### <a name="create-an-ekarda-test-user"></a>Een ekarda-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in ekarda. ekarda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. U hoeft in deze sectie niets te doen. Als er nog geen gebruiker met de naam B. Simon in ekarda bestaat, wordt na verificatie een nieuwe gebruiker gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van ekarda, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van ekarda en initieer hier de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt automatisch aangemeld bij het exemplaar van ekarda waarvoor u eenmalige aanmelding hebt ingesteld

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel ekarda klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het ekarda-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u ekarda hebt geconfigureerd, kunt u sessiebeheer afdwingen. Deze voorzorgsmaatregel biedt in realtime bescherming tegen de exfiltratie en infiltratie van gevoelige gegevens in uw organisatie. Sessiebeheer is een uitbreiding van App-beheer voor voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).