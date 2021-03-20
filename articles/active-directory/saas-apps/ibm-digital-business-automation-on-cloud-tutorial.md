---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met IBM Digital Business Automation on Cloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en IBM Digital Business Automation on Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: 91a50ed85b5802657a27c5a545de0013937e9da0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met IBM Digital Business Automation on Cloud

In deze zelfstudie leert u hoe u IBM Digital Business Automation on Cloud kunt integreren met Azure Active Directory (Azure AD). Als u IBM Digital Business Automation on Cloud integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot IBM Digital Business Automation on Cloud.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij IBM Digital Business Automation on Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement met eenmalige aanmelding (SSO) voor IBM Digital Business Automation on Cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* IBM Digital Business Automation on Cloud ondersteunt SSO dat door **SP en IDP** is geïnitieerd
* Zodra u IBM Digital Business Automation on Cloud hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>IBM Digital Business Automation on Cloud toevoegen vanuit de galerie

U kunt de integratie van IBM Digital Business Automation on Cloud configureren in Azure AD door IBM Digital Business Automation on Cloud vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **IBM Digital Business Automation on Cloud** in het zoekvak.
1. Selecteer **IBM Digital Business Automation on Cloud** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Eenmalige aanmelding van Azure AD voor IBM Digital Business Automation on Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met IBM Digital Business Automation on Cloud met behulp van een testgebruiker, **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in IBM Digital Business Automation on Cloud.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met IBM Digital Business Automation on Cloud te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor IBM Digital Business Automation on Cloud configureren](#configure-ibm-digital-business-automation-on-cloud-sso)** voor het configureren van de instellingen voor eenmalige aanmelding aan de toepassingszijde.
    1. **[Een testgebruiker voor IBM Digital Business Automation on Cloud maken](#create-ibm-digital-business-automation-on-cloud-test-user)** om een tegenhanger van B.Simon te hebben in IBM Digital Business Automation on Cloud die is gekoppeld aan de Azure AD-vertegenwoordiging van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **IBM Digital Business Automation on Cloud** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:
    
    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Zodra het bestand met metagegevens is geüpload, worden de waarde voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie Cloud voor IBM Digital Business Automation:

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

    > [!Note]
    > Klanten kunnen het metagegevensbestand voor hun Cloud-abonnement verkrijgen via de [IBM Digital Business Automation in Cloud Client-ondersteuningsteam](mailto:supportbpmoncloud@us.ibm.com).

1. Als u niet het **Metagegevensbestand van de serviceprovider** hebt, voert u in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het klantondersteuningsteam van IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **IBM Digital Business Automation on Cloud instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u het gebruik van eenmalige aanmelding van Azure voor B. Simon in door toegang te verlenen tot IBM Digital Business Automation on Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **IBM Digital Business Automation on Cloud**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Eenmalige aanmelding voor IBM Digital Business Automation on Cloud configureren

Als u eenmalige aanmelding aan de zijde van **IBM Digital Business Automation on Cloud** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte, uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Testgebruiker voor IBM Digital Business Automation on Cloud maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in IBM Digital Business Automation on Cloud. Neem contact op met het [ondersteuningsteam van IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com) om de gebruikers toe te voegen in het IBM Digital Business Automation on Cloud-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel IBM Digital Business Automation on Cloud in het toegangsvenster klikt, wordt u automatisch aangemeld bij de versie van IBM Digital Business Automation on Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [IBM Digital Business Automation on Cloud proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [IBM Digital Business Automation on Cloud beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)