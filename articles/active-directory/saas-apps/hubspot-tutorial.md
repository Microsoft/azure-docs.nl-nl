---
title: 'Zelfstudie: Integratie van Azure Active Directory met HubSpot | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733230"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Zelfstudie: Azure Active Directory-integratie met HubSpot

In deze zelfstudie leert u hoe u HubSpot kunt integreren met Azure Active Directory (Azure AD). Wanneer u HubSpot integreert met Microsoft Azure Active Directory, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot HubSpot.
* Ervoor zorgen dat gebruikers automatisch met hun Microsoft Azure Active Directory-account worden aangemeld bij HubSpot.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met HubSpot hebt u het volgende nodig:

* Een Azure AD-abonnement Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op HubSpot waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen en HubSpot integreren met Azure AD.

HubSpot biedt ondersteuning voor de volgende functies:

* **Door SP geïnitieerde eenmalige aanmelding**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="adding-hubspot-from-the-gallery"></a>HubSpot toevoegen vanuit de galerie

Voor het configureren van de integratie van HubSpot met Azure Active Directory moet u HubSpot uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het zoekvak van de sectie **Toevoegen vanuit de galerie** de tekst **HubSpot**.
1. Selecteer **HubSpot** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Eenmalige aanmelding van Microsoft Azure Active Directory configureren en testen voor HubSpot

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met HubSpot op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in HubSpot tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met HubSpot, moet u de volgende stappen uitvoeren:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding met HubSpot configureren](#configure-hubspot-single-sign-on)** | Configureert de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Stelt Britta Simon in staat om gebruik te maken van eenmalige aanmelding via Azure AD. |
| **[Een HubSpot-testgebruiker maken](#create-a-hubspot-test-user)** | Maakt een tegenhanger van Britta Simon in HubSpot die is gekoppeld aan de Azure AD-representatie van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

1. Ga in Azure Portal op de integratiepagina van de **HubSpot**-app naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit om de *door IDP geïnitieerde modus* te configureren:

    1. Voer in het vak **Id** een URL in met het volgende patroon: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Voer in het vak **Antwoord-URL** een URL in met het volgende patroon: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Om de URL’s in te delen, kunt u ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in Azure Portal.

1. Als u de toepassing wilt configureren in de *door SP geïnitieerde* modus:

    1. Selecteer **Extra URL's instellen**.

    1. Voer in het vak **Aanmeldings-URL** het volgende in: **https:\//app.hubspot.com/login**.

    ![De optie Extra URL’s instellen](common/metadata-upload-additional-signon.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** naast **Certificaat (Base64)** . Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De optie om het certificaat (Base64) te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **HubSpot instellen** de juiste URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Eenmalige aanmelding bij HubSpot configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw HubSpot-beheerdersaccount.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **Standaardinstellingen van account**.

    ![De optie Standaardinstellingen van account in HubSpot](./media/hubspot-tutorial/config2.png)

1. Schuif omlaag naar de sectie **Beveiliging** en selecteer vervolgens **Instellen**.

    ![De optie Instellen in HubSpot](./media/hubspot-tutorial/config3.png)

1. Voer de volgende stappen uit in het gedeelte **Eenmalige aanmelding instellen**:

    1. Selecteer **Kopiëren** in het vak **URl van doelgroep (entiteits-id van serviceprovider)** om de waarde te kopiëren. Ga in Azure Portal naar het deelvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    1. Selecteer in het vak **URl voor aanmelding, ACS, ontvanger of doorsturen** **Kopiëren** om de waarde te kopiëren. Ga in Azure Portal naar het deelvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Antwoord-URL**.

    1. Plak in het vak **ID van identiteitsprovider of URL van verlener** in HubSpot de waarde voor **Azure AD-id** die u in Azure Portal hebt gekopieerd.

    1. Plak in HubSpot in het vak **URL van id-provider voor eenmalige aanmelding** de **aanmeldings-URL** die u hebt gekopieerd in Azure Portal.

    1. Open in Windows Kladblok het certificaatbestand (Base64) dat u hebt gedownload. Selecteer en kopieer de inhoud van het bestand. Plak deze vervolgens in HubSpot in het vak **X.509-certificaat**.

    1. Selecteer **Verifiëren**.

        ![De sectie Eenmalige aanmelding instellen in HubSpot](./media/hubspot-tutorial/config4.png)

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot HubSpot.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **HubSpot** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-a-hubspot-test-user"></a>Een HubSpot-testgebruiker maken

De gebruiker moet zijn ingericht in HubSpot om Azure AD in te schakelen zodat een gebruiker zich kan aanmelden bij HubSpot. Het inrichten is in HubSpot een handmatige taak.

Ga als volgt te werk om een gebruikersaccount in te richten in HubSpot:

1. Meld u aan bij uw HubSpot-bedrijfssite als beheerder.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **Gebruikers en teams**.

    ![De optie Gebruikers en teams in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecteer **Create user**.

    ![De optie Gebruiker maken in HubSpot](./media/hubspot-tutorial/user2.png)

1. Voer in het vak **E-mailadres(sen) toevoegen** het e-mailadres van de gebruiker in met de indeling brittasimon\@contoso.com en selecteer vervolgens **Volgende**.

    ![Het vak E-mailadres(sen) toevoegen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user3.png)

1. Selecteer elk tabblad in de sectie **Gebruikers maken**. Stel op elk tabblad de relevante opties en machtigingen voor de gebruiker in. Selecteer vervolgens **Volgende**.

    ![Tabbladen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user4.png)

1. Selecteer **Verzenden** om de uitnodiging naar de gebruiker te verzenden.

    ![De optie Verzenden in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > De gebruiker wordt geactiveerd nadat deze de uitnodiging heeft geaccepteerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van HubSpot, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van HubSpot en initieer daar de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze toepassing testen** in Azure Portal. U wordt automatisch aangemeld bij de instantie van HubSpot waarvoor u eenmalige aanmelding hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel HubSpot klikt en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij de instantie van HubSpot waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u HubSpot hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor uw organisatie in real time wordt beveiligd tegen exfiltratie en infiltratie van gevoelige gegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).