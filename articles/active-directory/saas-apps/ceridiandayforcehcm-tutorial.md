---
title: 'Zelfstudie: Azure Active Directory-integratie met Ceridian Dayforce HCM | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651844"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Zelfstudie: Azure Active Directory-integratie met Ceridian Dayforce HCM

In deze zelf studie leert u hoe u Ceridian Dayforce HCM integreert met Azure Active Directory (Azure AD). Wanneer u Ceridian Dayforce HCM integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Ceridian Dayforce HCM.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Ceridian Dayforce HCM met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Ceridian Dayforce HCM-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ceridian Dayforce HCM ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Ceridian Dayforce HCM toevoegen vanuit de galerie

Voor het configureren van de integratie van Ceridian Dayforce HCM in Azure AD, moet u Ceridian Dayforce HCM vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Ceridian Dayforce HCM** in het zoekvak.
1. Selecteer **Ceridian DAYFORCE HCM** uit het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Azure AD SSO configureren en testen voor Ceridian Dayforce HCM

Configureer en test Azure AD SSO met Ceridian Dayforce HCM met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Ceridian Dayforce HCM.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Ceridian Dayforce HCM:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Ceridian DAYFORCE HCM SSO configureren](#configure-ceridian-dayforce-hcm-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak Ceridian DAYFORCE HCM test User](#create-ceridian-dayforce-hcm-test-user)** -als u een tegen hanger van B. Simon in CERIDIAN Dayforce HCM wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren 

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Ceridian DAYFORCE HCM** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over domein en URL's voor eenmalige aanmelding met Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **Aanmeldings-URL** de URL die uw gebruikers moeten gebruiken om zich aan te melden bij uw toepassing Ceridian Dayforce HCM.

    | Omgeving | URL |
    | :-- | :-- |
    | Voor productie | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Voor testen | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. In het tekstvak **id** typt u de URL met het volgende patroon:

    | Omgeving | URL |
    | :-- | :-- |
    | Voor productie | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Voor testen | `https://fs-test.dayforcehcm.com/sp` |

    c. Typ in het tekstvak **Antwoord-URL** de URL die Azure AD moet gebruiken voor het antwoord.

    | Omgeving | URL |
    | :-- | :-- |
    | Voor productie | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Voor testen | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor Ceridian Dayforce HCM](https://www.ceridian.com/support) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In de toepassing Ceridian Dayforce HCM worden de SAML-asserties in een specifieke indeling verwacht. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam | Bronkenmerk|
    | ---------| --------- |
    | naam  | User.extensionattribute2 |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](common/new-save-attribute.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Selecteer in de lijst **Bronkenmerk** het gebruikerskenmerk dat u wilt gebruiken voor uw implementatie. Als u bijvoorbeeld de werknemer-id wilt gebruiken als unieke gebruikers-id en u de waarde van het kenmerk in de ExtensionAttribute2 hebt opgeslagen, selecteert u vervolgens user.extensionattribute2.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

8. In de sectie **Ceridian Dayforce HCM instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Ceridian Dayforce HCM.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Ceridian Dayforce HCM** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Ceridian Dayforce HCM SSO configureren

Als u eenmalige aanmelding aan de **Ceridian Dayforce HCM**-zijde wilt configureren, moet u het gedownloade **XML-bestand met metagegevens** en de correct uit de Azure-portal gekopieerde URL's naar het [ondersteuningsteam van Ceridian Dayforce HCM](https://www.ceridian.com/support) sturen. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Testgebruiker voor Ceridian Dayforce HCM maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Ceridian Dayforce HCM. Neem contact op met het [ondersteuningsteam van Ceridian Dayforce HCM](https://www.ceridian.com/support) om de gebruikers toe te voegen in het Ceridian Dayforce HCM-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de Ceridian Dayforce HCM-aanmeld-URL waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar Ceridian Dayforce HCM sign-on URL en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Ceridian Dayforce HCM in de mijn apps klikt, wordt dit omgeleid naar Ceridian Dayforce HCM-aanmeldings-URL. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Ceridian Dayforce HCM hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).