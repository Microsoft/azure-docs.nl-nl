---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Claromentis | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455898"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Claromentis

In deze zelfstudie leert u hoe u Claromentis integreert met Azure Active Directory (Azure AD). Wanneer u Claromentis integreert met Azure AD, kunt u het volgende:

* Bepalen in Azure AD wie er toegang heeft tot Claromentis.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Claromentis.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Claromentis-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Claromentis ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Claromentis ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-claromentis-from-the-gallery"></a>Claromentis toevoegen uit de galerie

Om de integratie van Claromentis in Azure AD te configureren, moet u Claromentis uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Claromentis** in het zoekvak in het onderdeel **Toevoegen uit de galerie**.
1. Selecteer **Claromentis** in de resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Claromentis

Configureer en test eenmalige aanmelding van Azure AD met Claromentis met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Claromentis.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Claromentis te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Claromentis configureren](#configure-claromentis-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker maken in Claromentiss](#create-claromentis-test-user)** : om een tegenhanger voor B.Simon te maken in Claromentis die is gekoppeld aan de voorstelling van de gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Claromentis** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Voer in het tekstvak **Id** de id-waarde in zoals vereist door uw organisatie.

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke id, antwoord-URL en URL voor eenmalige aanmelding, dit wordt later in de zelfstudie nog uitgelegd.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Claromentis instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Claromentis.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Claromentis**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-claromentis-sso"></a>Eenmalige aanmelding voor Claromentis configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van Claromentis.

1. Klik op het **toepassingspictogram** en selecteer **Beheerder**.

    ![Schermopname die de Claromentis-website toont met Beheerder geselecteerd.](./media/claromentis-tutorial/config1.png)

1. Selecteer het tabblad **Aangepaste aanmeldingshandler**.

    ![Schermopname die de pagina Beheer toont met Aangepaste aanmeldingshandler geselecteerd.](./media/claromentis-tutorial/config2.png)

1. Selecteer **SAML-configuratie**.

    ![Schermopname die de configuratiepagina voor SAML toont.](./media/claromentis-tutorial/config3.png)

1. Schuif in het tabblad **SAML-configuratie** omlaag naar het onderdeel **Configuratie** en voer de volgende stappen uit:

    ![Schermopname die de sectie Configuratie van de pagina toont, waar u de informatie kunt invoeren die is beschreven in deze stap.](./media/claromentis-tutorial/config4.png)

    a. Voer in het tekstvak **Naam technische contactpersoon** de naam in van de technische contactpersoon.

    b. Voer in het tekstvak **E-mail technische contactpersoon** het e-mailadres in van de technische contactpersoon.

    c. Geef in het tekstvak **Verificatie beheerderswachtwoord** het wachtwoord in.

1. Schuif omlaag naar **Verificatiebronnen** en voor de volgende stappen uit:

    ![Schermopname die de sectie Verificatiebronnen toont, waarin u de informatie kunt invoeren die in deze stap is beschreven.](./media/claromentis-tutorial/config5.png)

    a. Voer in het tekstvak **IDP** de waarde van de **Azure AD-id** in die u uit het Azure-portal hebt gekopieerd.

    b. Voer in het tekstvak **Entiteits-id** de waarde voor de entiteits-id in.

    c. Upload het **XML-bestand met federatieve metagegevens** dat u eerder uit de Azure-portal hebt gedownload.

    d. Klik op **Opslaan**.

1. U ziet nu dat alle URL's zijn ingevuld onder **Identiteitsprovider** in het onderdeel **SAML-configuratie**.

    ![Schermopname die de pagina Identiteitsprovider toont, waarop URL's zijn ingevuld.](./media/claromentis-tutorial/config6.png)

    a. Kopieer de waarde van de **Id (Entiteits-id)** en plak deze in het tekstvak voor de **Id** bij het onderdeel **Standaard SAML-configuratie** in het Azure-portal.

    b. Kopieer de waarde van de **Antwoord-URL** en plak deze in het tekstvak voor de **Antwoord-URL** bij het onderdeel **Standaard SAML-configuratie** in het Azure-portal.

    c. Kopieer de waarde van de **Aanmeldings-URL** en plak deze in het tekstvak voor de **Aanmeldings-URL** bij het onderdeel **Standaard SAML-configuratie** in het Azure-portal.

### <a name="create-claromentis-test-user"></a>Testgebruiker voor Claromentis maken

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in Claromentis. Claromentis biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Claromentis bestaat, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Claromentis in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de Claromentis-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Claromentis met Azure AD uitproberen](https://aad.portal.azure.com/)