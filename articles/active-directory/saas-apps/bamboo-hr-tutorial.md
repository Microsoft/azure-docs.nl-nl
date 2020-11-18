---
title: 'Zelfstudie: Azure Active Directory-integratie met BambooHR | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 8025728ffc40aca27807068eff29f5a889a8d76e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331393"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Zelfstudie: Azure Active Directory-integratie met BambooHR

In deze zelfstudie leert u hoe u BambooHR integreert met Azure Active Directory (Azure AD). Wanneer u BambooHR integreert met Microsoft Azure Active Directory, kunt u het volgende:

* In Microsoft Azure AD beheren wie toegang heeft tot BambooHR.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij BambooHR.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een BambooHR-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* BambooHR ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-bamboohr-from-the-gallery"></a>BambooHR uit de galerie toevoegen

Als u de integratie van BambooHR in Azure AD wilt configureren, moet u BambooHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak de tekst **BambooHR**.
1. Selecteer **BambooHR** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Eenmalige aanmelding van Azure Active Directory voor BambooHR configureren en testen

Configureer en test eenmalige aanmelding van Azure AD bij BambooHR met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in BambooHR.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD bij BambooHR te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    * **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding bij BambooHR configureren](#configure-bamboohr-sso)** : als u de instellingen voor eenmalige aanmelding aan de app-zijde wilt configureren.
    * **[Testgebruiker voor BambooHR maken](#create-bamboohr-test-user)**: als u een equivalent van Britta Simon in BambooHR wilt maken dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in Azure Portal naar de integratiepagina van de app **BambooHR**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company>.bamboohr.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    | Antwoord-URL |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteuningsteam van BambooHR](https://www.bamboohr.com/contact.php) om de waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **BambooHR instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot BambooHR.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **BambooHR** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-bamboohr-sso"></a>Eenmalige aanmelding bij BambooHR configureren

1. Meld u in een nieuw venster als beheerder aan bij de bedrijfssite van BambooHR.

2. Doe het volgende op de startpagina:
   
    ![De pagina voor eenmalige aanmelding met BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Eenmalige aanmelding")   

    a. Selecteer **Apps**.
   
    b. In het deelvenster **Apps** selecteert u **Eenmalige aanmelding**.
   
    c. Selecteer **Eenmalige aanmelding op basis van SAML**.

3. Doe het volgende in het deelvenster **Eenmalige aanmelding op basis van SAML**:
   
    ![Het deelvenster eenmalige aanmelding SAML](./media/bamboo-hr-tutorial/IC796692.png "Eenmalige aanmelding met SAML")
   
    a. Plak in het vak **Aanmeldings-URL voor eenmalige aanmelding** de **Aanmeldings-URL** die u in stap 6 vanuit Azure Portal hebt gekopieerd.
      
    b. Open in Kladblok het base-64 gecodeerde certificaat dat u hebt gedownload vanuit Azure Portal, kopieer de inhoud en plak deze in het vak **X.509-certificaat**.
   
    c. Selecteer **Opslaan**.

### <a name="create-bamboohr-test-user"></a>Testgebruiker voor BambooHR maken

Als u wilt dat Azure AD-gebruikers zich bij BambooHR kunnen aanmelden, stelt u ze als volgt handmatig in BambooHR in:

1. Meld u bij uw **BambooHR**-site aan als beheerder.

2. Selecteer **Instellingen** in de werkbalk bovenaan.
   
    ![De knop Settings](./media/bamboo-hr-tutorial/IC796694.png "Instelling")

3. Selecteer **Overzicht**.

4. Selecteer **Beveiliging** > **Gebruikers** in het linkerdeelvenster.

5. Typ de gebruikersnaam, het wachtwoord en het e-mailadres van het geldige Azure AD-account dat u wilt instellen.

6. Selecteer **Opslaan**.
        
>[!NOTE]
>U kunt ook BambooHR-hulpprogramma's en -API's voor het maken van gebruikersaccounts gebruiken om Azure AD-gebruikersaccounts in te stellen.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

1. Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van BambooHR, waar u de aanmeldingsstroom kunt starten. 

2. Ga rechtstreeks naar de aanmeldings-URL van BambooHR en start de aanmeldingsstroom daar.

3. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in Toegangsvenster op de BambooHR-tegel klikt, wordt u omgeleid naar de aanmeldings-URL voor BambooHR. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.


## <a name="next-steps"></a>Volgende stappen

Wanneer u BambooHR hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor in realtime beveiliging wordt geboden tegen exfiltratie en infiltratie van gevoelige gegevens van uw organisatie. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).