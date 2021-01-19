---
title: 'Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks Captive Portal | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Palo Alto Networks Captive Portal configureert.
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
ms.openlocfilehash: 0e16cb3d24f9dd33b156bca72ce1c99e7aae957a
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963601"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Zelfstudie: Azure Active Directory-integratie met Palo Alto Networks Captive Portal

In deze zelfstudie leert u hoe u Palo Alto Networks Captive Portal integreert met Azure Active Directory (Azure AD).
De integratie van Palo Alto Networks – Captive Portal met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Palo Alto Networks – Captive Portal.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Palo Alto Networks – Captive Portal (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD te integreren met Palo Alto Networks Captive Portal:

* Een Azure Active Directory-abonnement. Als u geen Azure AD hebt, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).
* Een abonnement op Palo Alto Networks Captive Portal waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Palo Alto Networks – Captive Portal biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Palo Alto Networks – Captive Portal ondersteunt **Just In Time**-gebruikersinrichting

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Palo Alto Networks – Captive Portal toevoegen vanuit de galerie

Om de integratie van Palo Alto Networks – Captive Portal te configureren in Azure AD, moet u Palo Alto Networks – Captive Portal vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **Toevoegen vanuit de galerij** **Palo Alto Networks Captive Portal** in het zoekvak.
1. Selecteer **Palo Alto Networks Captive Portal** uit het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Palo Alto Networks Captive Portal op basis van een testgebruiker met de naam **B.Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Palo Alto Networks – Captive Portal tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Palo Alto Networks Captive Portal:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-sso)** : zodat de gebruiker deze functie kan gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : eenmalige aanmelding van Azure AD testen met de gebruiker B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor Palo Alto Networks Captive Portal configureren](#configure-palo-alto-networks-captive-portal-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    * **[Een testgebruiker voor Palo Alto Networks Captive Portal maken](#create-a-palo-alto-networks-captive-portal-test-user)** : als u een tegenhanger van B.Simon in Palo Alto Networks Captive Portal wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** – controleer of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de **Palo Alto Networks Captive Portal** toepassingsintegratiepagina, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het deelvenster **Basic SAML-configuratie** voert u de volgende stappen uit:

   1. Voer bij **Id** een URL in met het patroon `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Voer bij **Antwoord-URL** een URL in met het patroon `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Werk de plaatsaanduidingen in deze stap bij met de werkelijke warden voor de id en antwoord-URL's. Neem contact op met het [ondersteuningsteam van Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support) om de werkelijke waarden te verkrijgen.

5. Selecteer **Downloaden** in de sectie **SAML-handtekeningcertificaat** naast **XML-bestand met federatieve metagegevens**. Sla het gedownloade bestand op uw computer op.

    ![De koppeling voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

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

In dit gedeelte gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Palo Alto Networks Captive Portal.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Typ en selecteer **Palo Alto Networks Captive Portal** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Configureer Palo Alto Networks Captive Portal SSO

Stel vervolgens eenmalige aanmelding in voor Palo Alto Networks Captive Portal:

1. Meld u in een ander browservenster aan bij de Palo Alto Networks-website als beheerder.

2. Selecteer het tabblad **Device**.

    ![Het tabblad Device van de Palo Alto Networks-website](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecteer in het menu **SAML Identity Provider** en vervolgens **Import**.

    ![De knop Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Voer in het dialoogvenster **SAML Identity Provider Server Profile Import** de volgende stappen uit:

    ![Eenmalige aanmelding voor Palo Alto Networks configureren](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Voer bij **Profile Name** een profielnaam in, bijvoorbeeld **AzureAD-CaptivePortal**.
    
    2. Selecteer **Browse** bij **Identity Provider Metadata**. Selecteer het metadata.xml-bestand dat u hebt gedownload van de Azure-portal.
    
    3. Selecteer **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Een testgebruiker maken voor Palo Alto Networks Captive Portal

Maak vervolgens een gebruiker met de naam *Britta Simon* in Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal ondersteunt Just In Time-gebruikersinrichting, wat standaard is ingeschakeld. U hoeft geen taken uit te voeren in deze sectie. Als er nog geen gebruiker in Palo Alto Networks Captive Portal bestaat, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [klantondersteuningsteam van Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik op Deze app testen in Azure Portal en u wordt automatisch aangemeld bij de Palo Alto Networks Captive Portal waarvoor u de SSO hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Palo Alto Networks Captive Portal klikt in Mijn apps, wordt u automatisch aangemeld bij het exemplaar van Palo Alto Networks Captive Portal waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Palo Alto Networks Captive Portal hebt geconfigureerd kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).