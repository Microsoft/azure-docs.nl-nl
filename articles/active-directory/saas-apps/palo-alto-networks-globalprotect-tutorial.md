---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Palo Alto Networks - GlobalProtect | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks - GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Palo Alto Networks - GlobalProtect

In deze zelfstudie leert u hoe u Palo Alto Networks - GlobalProtect integreert met Azure Active Directory (Azure AD). Wanneer u Palo Alto Networks - GlobalProtect integreert met Azure AD, kunt u het volgende doen:

* Beheer in Azure AD wie toegang tot Palo Alto Networks - GlobalProtect heeft.
* Sckel in dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Palo Alto Networks - GlobalProtect.
* Ww accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Abonnement voor Palo Alto Networks - GlobalProtect waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks - GlobalProtect ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Palo Alto Networks - GlobalProtect ondersteunt **Just In Time**-gebruikersinrichting

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Palo Alto Networks - GlobalProtect uit de galerie toevoegen

Als u de integratie van Palo Alto Networks - GlobalProtect met Azure AD wilt configureren, moet u Palo Alto Networks - GlobalProtect uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Palo Alto Networks - GlobalProtect** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Palo Alto Networks - GlobalProtect** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Eenmalige aanmelding van Azure AD voor Palo Alto Networks - GlobalProtect configureren en testen

Eenmalige aanmelding van Azure AD voor Palo Alto Networks - GlobalProtect configureren en testen met een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Palo Alto Networks - GlobalProtect.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Palo Alto Networks - GlobalProtect te configureren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Palo Alto Networks - GlobalProtect configureren](#configure-palo-alto-networks---globalprotect-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Palo Alto Networks - GlobalProtect maken](#create-palo-alto-networks---globalprotect-test-user)** : als u een equivalent van B.Simon in Palo Alto Networks - GlobalProtect wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de toepassingsintegratiepagina van **Palo Alto Networks - GlobalProtect**, zoek de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<Customer Firewall URL>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **Palo Alto Networks - GlobalProtect instellen** kopieert u de URL('s) die u nodig hebt.

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Palo Alto Networks - GlobalProtect.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Palo Alto Networks - GlobalProtect**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Palo Alto Networks - GlobalProtect SSO configureren

1. Open Palo Alto Networks - GlobalProtect als beheerder in een ander browservenster.

2. Klik op **Apparaat**.

    ![Eenmalige aanmelding 1 voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer **SAML-id-provider** in de linkernavigatiebalk en klik op Importeren om het metagegevensbestand te importeren.

    ![Eenmalige aanmelding 2 voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Voer de volgende acties uit in het venster Importeren

    ![Eenmalige aanmelding 3 voor Palo Alto configureren](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Geef in het tekstvak **Profielnaam** een naam op, bijvoorbeeld Azure AD GlobalProtect.

    b. Klik in **Metagegevens van id-provider** op **Bladeren** en selecteer het bestand metadata.xml dat u vanuit Azure Portal hebt gedownload

    c. Klik op **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Testgebruiker voor Palo Alto Networks - GlobalProtect maken

In dit gedeelte wordt een gebruiker met de naam B.Simon gemaakt in Palo Alto Networks - GlobalProtect. Palo Alto Networks - GlobalProtect ondersteunt Just In Time-gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Palo Alto Networks - GlobalProtect bestaat, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Palo Alto Networks - GlobalProtect, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Palo Alto Networks - GlobalProtect. Initieer daar de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Palo Alto Networks - GlobalProtect klikt in Mijn apps, wordt u automatisch aangemeld bij het exemplaar van Palo Alto Networks - GlobalProtect waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Palo Alto Networks - GlobalProtect hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).