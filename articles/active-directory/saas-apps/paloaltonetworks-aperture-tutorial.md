---
title: 'Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Aperture | Microsoft Docs'
description: Hier vindt u informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks - Aperture.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 42a6bc9bfb06f1c80b719bdda686ae111a8884ab
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221985"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks - Aperture

In deze zelf studie leert u hoe u Palo Alto Networks-opening kunt integreren met Azure Active Directory (Azure AD). Wanneer u Palo Alto Networks-diafragma integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Palo Alto-netwerken-opening.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Palo Alto Networks-opening met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Palo Alto Networks-opening single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks: opening ondersteunt **SP** -en **IDP** geïnitieerde SSO.

## <a name="add-palo-alto-networks---aperture-from-the-gallery"></a>Palo Alto-netwerken toevoegen-opening uit de galerie

Als u de integratie van Palo Alto Networks - Aperture met Azure AD wilt configureren, moet u Palo Alto Networks - Aperture toevoegen vanuit de galerie aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Palo Alto Networks – Aperture** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Palo Alto Networks – Aperture** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Palo Alto Networks – Aperture op basis van een testgebruiker met de naam **B.Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Palo Alto Networks - Aperture tot stand is gebracht.

Voer de volgende stappen uit om Azure AD eenmalige aanmelding te configureren en te testen met Palo Alto Networks – Aperture:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Palo Alto Networks – Aperture SSO configureren](#configure-palo-alto-networks---aperture-sso)** – als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Palo Alto Networks - Aperture maken](#create-palo-alto-networks---aperture-test-user)** : als u in Palo Alto Networks - Aperture een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal naar de toepassingsintegratiepagina van **Palo Alto Networks – Aperture**, zoek de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Palo Alto Networks - Aperture instellen** kopieert u de URL('s) die u nodig hebt.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Palo Alto Networks – Aperture.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Palo Alto Networks - Aperture** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-palo-alto-networks---aperture-sso"></a>Configureer Palo Alto Networks – Aperture SSO

1. Meld u in een ander browservenster als beheerder aan bij Palo Alto Networks - Aperture.

2. Klik op de bovenste menubalk op **Instellingen**.

    ![Het tabblad Instellingen](./media/paloaltonetworks-aperture-tutorial/settings.png)

3. Ga naar de sectie **TOEPASSING** en klik op het formulier **Verificatie** aan de linkerkant van het menu.

    ![Het tabblad Verificatie](./media/paloaltonetworks-aperture-tutorial/authentication.png)
    
4. Voer de volgende stappen uit op de pagina **Verificatie**:
    
    ![Het tabblad Verificatie](./media/paloaltonetworks-aperture-tutorial/tab.png)

    a. Schakel in het veld **Eenmalige aanmelding** de optie **Eenmalige aanmelding inschakelen (ondersteunde SSP-providers zijn Okta, One login)** in.

    b. Plak de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal, in het tekstvak **Identiteitsprovider-id**.

    c. Klik op **Bestand kiezen** om het gedownloade certificaat van Azure AD te uploaden in het veld **Certificaat van identiteitsprovider**.

    d. Plak in het tekstvak **URL voor eenmalige aanmelding van identiteitsprovider** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    e. Controleer de informatie van de identiteitsprovider in de sectie **Aperture-informatie** en download het certificaat uit het veld **Aperture-sleutel**.

    f. Klik op **Opslaan**.


### <a name="create-palo-alto-networks---aperture-test-user"></a>Een Palo Alto Networks - Aperture-testgebruiker maken

In deze sectie gaat u een gebruiker met de naam Britta Simon maken in Palo Alto Networks - Aperture. Werk samen met het [klantondersteuningsteam van Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) om de gebruikers toe te voegen in het Palo Alto Networks - Aperture-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Palo Alto Networks – Aperture, waar u de aanmeldingsstroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Palo Alto Networks – Aperture en initieer daar de aanmeldingsstroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **Deze app testen** in Azure Portal en u wordt automatisch aangemeld bij de Palo Alto Networks – Aperture waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u in Mijn apps op de tegel Palo Alto Networks - Aperture klikt, en deze is geconfigureerd in de SP-modus, wordt u omgeleid naar de aanmeldingspagina van de toepassing voor het initiëren van de aanmeldingsstroom. Als deze is geconfigureerd in de IDP-modus, wordt u automatisch aangemeld bij het Palo Alto Networks - Aperture-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Palo Alto Networks – Aperture hebt geconfigureerd kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).