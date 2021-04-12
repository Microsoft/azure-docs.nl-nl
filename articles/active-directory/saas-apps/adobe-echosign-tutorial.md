---
title: 'Zelfstudie: Azure Active Directory-integratie met Adobe Sign | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649951"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Zelfstudie: Azure Active Directory-integratie met Adobe Creative Sign

In deze zelf studie leert u hoe u Adobe Sign integreert met Azure Active Directory (Azure AD). Wanneer u Adobe Sign integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Adobe-ondertekenen.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij Adobe met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:
 
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Adobe Sign-on (SSO)-abonnement ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Sign ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="add-adobe-sign-from-the-gallery"></a>Adobe-ondertekening toevoegen vanuit de galerie

Om de integratie van Adobe Sign met Azure AD te configureren, moet u Adobe Sign vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Adobe Sign** in het zoekvak.
1. Selecteer **Adobe-teken** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Azure AD SSO voor Adobe-ondertekenen configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Adobe Sign met Azure AD configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Adobe Sign tot stand is gebracht.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met Adobe-teken:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Adobe Sign SSO configureren](#configure-adobe-sign-sso)** : als u de instellingen voor één Sign-On wilt configureren aan de kant van de toepassing.
    1. **[Een testgebruiker maken voor Adobe Sign](#create-adobe-sign-test-user)** als u een equivalent van Britta Simon in Adobe Sign wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Adobe Sign:

1. Selecteer in de Azure Portal, op de pagina **Adobe** -toepassings integratie, de optie **eenmalige aanmelding**.

1. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het potloodpictogram om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.echosign.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Adobe Sign instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Adobe-ondertekening.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Adobe Sign** in de lijst toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-adobe-sign-sso"></a>Adobe Sign SSO configureren

1. Neem vóór de configuratie contact op met het [Clientondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) om uw domein in Adobe Sign te laten opnemen als toegestaan domein. U voegt het domein als volgt toe:

    a. Het [Clientondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) stuurt u een willekeurig gegenereerd token. Voor uw domein ziet het token er ongeveer als volgt uit: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publiceer het verificatietoken in een DNS-tekstrecord en stel het [Clientondersteuningsteam van Adobe Sign ](https://helpx.adobe.com/in/contact/support.html) op de hoogte.

    > [!NOTE]
    > Dit kan enkele dagen duren. Een vertraging van de DNS-doorgifte betekent dat een waarde die is gepubliceerd in DNS mogelijk gedurende een uur of langer niet zichtbaar. Uw IT-beheerder moet weten hoe u dit token publiceert in een DNS-tekstrecord.

    c. Wanneer u het [Clienondersteuningsteam van Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via het ondersteuningsticket ervan op de hoogte stelt dat het token is gepubliceerd, wordt het domein gevalideerd en toegevoegd aan uw account.

    d. Over het algemeen verloopt het publiceren van het token op een DNS-record als volgt:

    * Aanmelden bij uw domeinaccount
    * Zoek de pagina voor het bijwerken van de DNS-record. Deze pagina wordt ook wel DNS Management, Name Server Management of Advanced Settings genoemd.
    * Zoek de TXT-records voor uw domein.
    * Voeg een TXT-record toe met de volledige tokenwaarde die is verstrekt door Adobe.
    * Sla uw wijzigingen op.

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Adobe Sign.

1. Selecteer in het menu SAML de optie **Account Settings** > **SAML Settings**.

    ![Schermafbeelding van de pagina SAML-instellingen van Adobe Sign](./media/adobe-echosign-tutorial/settings.png "Account")

1. Voer in de sectie **SAML Settings** de volgende stappen uit:

    ![Schermopname van de SAML-instellingen, inclusief 'SAML verplicht'.](./media/adobe-echosign-tutorial/saml1.png "SAML-instellingen")

   ![Schermopname van SAML-instellingen](./media/adobe-echosign-tutorial/saml.png "SAML-instellingen")

   a. Selecteer onder **SAML Mode** de optie **SAML Mandatory**.

   b. Selecteer **Allow Echosign Account Administrators to log in using their Echosign Credentials**.

   c. Selecteer onder **User Creation** de optie **Automatically add users authenticated through SAML**.

   d. Plak in het tekstvak **IDP Entity ID** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

   e. Plak de **aanmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Idp Login URL**.

   f. Plak de **afmeldings-URL**, die u in de Azure-portal hebt gekopieerd, in het tekstvak **Idp Logout URL**.

   g. Open het gedownloade bestand **Certificate(Base64)** in Kladblok. Kopieer de inhoud ervan in het klembord en plak deze in het tekstvak **IdP Certificate**.

   h. Selecteer **Save changes**.

### <a name="create-adobe-sign-test-user"></a>Testgebruiker maken voor Adobe Sign

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Adobe Sign, moeten ze worden ingericht in Adobe Sign. Dit is een handmatige taak.

>[!NOTE]
>U kunt voor het inrichten van Azure AD-gebruikersaccounts andere hulpprogramma's of API's voor het maken van Adobe Sign-gebruikersaccount gebruiken die door Adobe Sign worden verstrekt. 

1. Meld u als beheerder aan bij de bedrijfssite van **Adobe Sign**.

2. Selecteer in het menu bovenaan de optie **Account**. Selecteer in het linkerdeelvenster **Users & Groups** > **Create a new user**.

    ![Schermafbeelding van de bedrijfssite van Adobe Sign met Account, Gebruikers en groepen en Een nieuwe gebruiker maken gemarkeerd](./media/adobe-echosign-tutorial/account.png "Account")

3. Voer in de sectie **Create New User** de volgende stappen uit:

    ![Schermafbeelding van de sectie Nieuwe gebruiker maken](./media/adobe-echosign-tutorial/user.png "Gebruiker maken")

    a. Typ in de betreffende tekstvakken het **e-mailadres**, de **voornaam** en **achternaam** van een geldig Azure AD-account dat u wilt inrichten.

    b. Selecteer **Create User**.

>[!NOTE]
>De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt. 

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Adobe, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor aanmelden bij Adobe en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Adobe Sign-tegel in de mijn apps klikt, moet u automatisch worden aangemeld bij het Adobe-teken waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Adobe-ondertekening hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).