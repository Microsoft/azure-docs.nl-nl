---
title: 'Zelfstudie: Azure Active Directory-integratie met ArcGIS Enterprise | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en ArcGIS Enterprise configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645489"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Zelfstudie: Azure Active Directory-integratie met ArcGIS Enterprise

In deze zelf studie leert u hoe u ArcGIS Enter prise integreert met Azure Active Directory (Azure AD). Wanneer u ArcGIS Enter prise integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ArcGIS Enter prise.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ArcGIS Enter prise met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ArcGIS Enter prise single sign-on (SSO) ingeschakeld abonnement.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ArcGIS Enter prise ondersteunt door **SP en IDP** geïnitieerde SSO.
* ArcGIS Enter prise ondersteunt **just-in-time** gebruikers inrichting.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>ArcGIS Enter prise toevoegen vanuit de galerie

Als u de integratie van ArcGIS Enterprise in Azure AD wilt configureren, moet u ArcGIS Enterprise vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **ArcGIS Enter prise** in het zoekvak.
1. Selecteer **ArcGIS Enter prise** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Azure AD SSO configureren en testen voor ArcGIS Enter prise

Azure AD SSO met ArcGIS Enter prise configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ArcGIS Enter prise.

Als u Azure AD SSO wilt configureren en testen met ArcGIS Enter prise, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ArcGIS Enter PRISE SSO configureren](#configure-arcgis-enterprise-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak ArcGIS Enter prise test User](#create-arcgis-enterprise-test-user)** -om een soort tegen te brengen van B. Simon in ArcGIS Enter prise dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **ArcGIS Enter prise** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u de toepassing in de door **IDP geïnitieerde** modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<EXTERNAL_DNS_NAME>.portal`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van ArcGIS Enterprise](mailto:support@esri.com) om deze waarden op te vragen. De waarde voor de id is beschikbaar in de sectie **Set Identity Provider**. Dit wordt verderop in deze zelfstudie uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ArcGIS Enter prise.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **ArcGIS Enter prise**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-arcgis-enterprise-sso"></a>ArcGIS Enter prise SSO configureren

1. Als u de configuratie in ArcGIS Enterprise wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **ArcGIS Enterprise instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de ArcGIS Enterprise-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij ArcGIS Enterprise. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u ArcGIS Enterprise handmatig wilt instellen, meldt u zich als beheerder aan bij de bedrijfssite van ArcGIS Enterprise.


1. Selecteer **Organization >EDIT SETTINGS**.

    ![Schermopname toont het tabblad 'ArcGIS Enterprise Organization' met instellingen voor bewerken uitgevouwen.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Selecteer het tabblad **Security**.

    ![Schermopname toont het geselecteerde tabblad Beveiliging.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Blader omlaag naar de sectie **Enterprise Logins via SAML** en selecteer **SET ENTERPRISE LOGIN**.

    ![Schermopname toont de Enterprise-aanmeldingen via SAML, waar u op 'Enterprise-aanmelding instellen' kunt klikken.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Voer in de sectie **Set Identity Provider** de volgende stappen uit:

    ![Schermopname toont de pagina 'Gebruiker toevoegen', waar u de stappen kunt uitvoeren die hier worden beschreven.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Geef in het tekstvak **Name** een naam op, zoals **Azure Active Directory Test**.

    b. Plak in het tekstvak **URL** de waarde van **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    c. Klik op **Show advanced settings**, kopieer de waarde van **Entity ID** en plak deze in het tekstvak **Id** in de sectie **ArcGIS Enterprise-domein en -URL's** in de Azure-portal.

    ![Schermopname toont waar u de eenheids-id kunt verkrijgen en waar u de identificatieprovider kunt bijwerken.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Klik op **UPDATE IDENTITY PROVIDER**.

### <a name="create-arcgis-enterprise-test-user"></a>Een testgebruiker maken voor ArcGIS Enterprise

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in ArcGIS Enterprise. ArcGIS Enterprise biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in ArcGIS Enterprise, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Neem contact op met het [ondersteuningsteam van ArcGIS Enterprise](mailto:support@esri.com) als u handmatig een gebruiker moet maken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar ArcGIS Enter prise sign on URL waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar ArcGIS Enter prise sign-on URL en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **deze toepassing testen** in azure Portal en u moet zich automatisch aanmelden bij de ArcGIS-onderneming waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de ArcGIS Enter prise-tegel in de mijn apps klikt, wordt u naar de aanmeldings pagina van de toepassing omgeleid voor het initiëren van de aanmeldings stroom als u deze in de modus voor het aanmelden van het bedrijf hebt geconfigureerd, waarna u automatisch wordt aangemeld bij de ArcGIS onderneming waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u ArcGIS Enter prise hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).