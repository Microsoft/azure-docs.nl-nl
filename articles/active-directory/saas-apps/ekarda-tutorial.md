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
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 7bb74732074482c12d3bc760e259bb014ccf006f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met ekarda

In deze zelfstudie leert u hoe u ekarda integreert met Azure AD (Active Directory). Wanneer u ekarda integreert met Azure AD, kunt u het volgende:

* Beheren in Azure AD wie toegang heeft tot ekarda.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ekarda.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat is eenmalige aanmelding?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een ekarda-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ekarda biedt ondersteuning voor met SP en IDP geïnitieerde eenmalige aanmelding.
* ekarda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers.
* Nadat u ekarda hebt geconfigureerd, kunt u sessiebeheer afdwingen. Deze voorzorgsmaatregel biedt in realtime bescherming tegen de exfiltratie en infiltratie van gevoelige gegevens in uw organisatie. Sessiebeheer is een uitbreiding van App-beheer voor voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Ekarda toevoegen vanuit de galerie

Als u de integratie van ekarda in Azure AD wilt configureren, voegt u ekarda vanuit de galerie toe aan de lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.

1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om de nieuwe app toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **ekarda**.
1. Selecteer **ekarda** in het resultatenpaneel en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Eenmalige aanmelding van Azure AD configureren en testen voor ekarda

Configureer en test eenmalige aanmelding van Azure AD met ekarda met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ekarda.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met ekarda te configureren en testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.

    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij Ekarda configureren](#configure-ekarda-sso): als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * [Een ekarda-testgebruiker maken](#create-an-ekarda-test-user): als u een equivalent van B.Simon in ekarda wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen in de Azure-portal om eenmalige aanmelding van Azure AD in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
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

    * Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

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

   ![Schermopname van de sectie Beheren, met Gebruikers en groepen gemarkeerd.](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de sectie Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd.](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-ekarda-sso"></a>Eenmalige aanmelding bij ekarda configureren

1. Meld u in een ander browservenster als beheerder aan bij de ekarda-bedrijfssite.
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

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u in de Mijn apps-portal op de tegel ekarda klikt, wordt u automatisch aangemeld bij de ekarda-site waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the My Apps portal](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot de portal Mijn apps) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
* [ekarda uitproberen met Azure AD](https://aad.portal.azure.com/)
* Gebruik de [enterprise eCard-oplossing van ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) om het gewenste aantal medewerkers in te richten om eCards met uw bedrijfslogo naar hun klanten en collega's te verzenden. Meer informatie over het inrichten van [ekarda als een oplossing voor eenmalige aanmelding](https://support.ekarda.com/#SSO-Implementation).
* [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
* [ekarda beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)