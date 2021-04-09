---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco Webex Meetings | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592486"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Cisco Webex Meetings

In deze zelfstudie leert u hoe u Cisco Webex Meetings kunt integreren met Azure Active Directory (Azure AD). Wanneer u Cisco Webex Meetings integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Cisco Webex Meetings.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Cisco Webex Meetings.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Cisco Webex Meetings waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco Web-vergaderingen ondersteunen SSO **met SP en IDP** .

* Cisco WebEx-vergaderingen bieden ondersteuning **voor Just in time** -gebruikers inrichting.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco Webex Meetings toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Webex Meetings in Azure AD moet u Cisco Webex Meetings vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Cisco Webex Meetings**.
1. Selecteer **Cisco WebEx Meetings** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Azure AD SSO voor Cisco WebEx-vergaderingen configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Cisco Webex Meetings met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cisco Webex Meetings.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Cisco WebEx-vergaderingen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
   
1. **[Eenmalige aanmelding voor Cisco Webex Meetings configureren](#configure-cisco-webex-meetings-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
   * **[Testgebruiker maken in Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** : als u een tegenhanger van Britta Simon in Cisco Webex Meetings wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
    
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina **Cisco WebEx** -toepassings integratie, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** kunt u de toepassing configureren in door **IDP** geïnitieerde modus door het **metagegevensbestand van de serviceprovider** als volgt te uploaden:
   1. Klik op **Metagegevensbestand uploaden**.
   1. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.
   1. Als het uploaden van het metagegevensbestand van de serviceprovider is geslaagd, worden de waarden voor de **id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**.
   
      > [!Note]
      > U ontvangt het metagegevensbestand van de serviceprovider in de sectie **Eenmalige aanmelding voor Cisco WebEx Meetings configureren**, die verderop in de zelfstudie wordt beschreven. 

1. Als u de toepassing wilt configureren in door **SP** geïnitieerde modus, voert u de volgende stappen uit:  
   1. Klik in de sectie **Standaard SAML-configuratie** op het pictogram voor bewerken/pen.

      ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

   1. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<customername>.my.webex.com`

1. In de toepassing Cisco Webex Meetings worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

   ![image](common/edit-attribute.png)

1. Bovendien verwacht de Cisco Webex Meetings-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie Gebruikersclaims in het dialoogvenster Gebruikerskenmerken voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel: 

   | Naam | Bronkenmerk|
   | ---------------|  --------- |
   |   firstname    | user.givenname |
   |   lastname    | user.surname |
   |   e-mail       | user.mail |
   |   uid    | user.mail |

   1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.
   1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.
   1. Laat **Naamruimte** leeg.
   1. Selecteer Bron bij **Kenmerk**.
   1. Typ of selecteer in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij in de vervolgkeuzelijst.
   1. Klik op **Opslaan**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Cisco Webex Meetings instellen** de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Cisco Webex Meetings.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Cisco Webex Meetings** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-webex-meetings-sso"></a>Eenmalige aanmelding voor Cisco Webex Meetings configureren

1. Meld u aan bij Cisco WebEx-vergaderingen met uw beheerders referenties.
1. Ga naar **Common Site Setting** (Algemene site-instellingen) en navigeer naar **SSO Configuration** (SSO-configuratie).

   ![Schermopname met Cisco Webex Administration met Common Site Settings en S S O Configuration geselecteerd.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. Voer de volgende stappen uit op de pagina **Webex Administration** (Beheer van Webex):

   ![Schermopname met de pagina Webex Administration met de informatie die in deze stap wordt beschreven.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Selecteer **SAML 2.0** als **Federation-protocol**.
   1. Klik op de koppeling **Import SAML Metadata** (SAML-metagegevens importeren) en upload het metagegevensbestand dat u vanuit Azure Portal hebt gedownload.
   1. Selecteer **SSO-profiel** als **IDP gestart**  en klik op de knop **exporteren** om het meta gegevensbestand van de service provider te downloaden en te uploaden in het gedeelte **basis configuratie van SAML** op Azure Portal.
   1. Typ in het tekstvak **AuthContextClassRef** een van de volgende waarden:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Als u de MFA wilt inschakelen met behulp van Azure AD, voert u de twee waarden als volgt in: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Selecteer **Auto Account Creation** (Automatisch account maken).
   
      > [!NOTE]
      > Voor het inschakelen van **Just-in-Time**-gebruikersinrichting moet u het selectievakje **Auto Account Creation** inschakelen. Daarnaast moeten de SAML-tokenkenmerken worden doorgegeven in het SAML-antwoord.

   1. Klik op **Opslaan**.

      > [!NOTE]
      > Deze configuratie is alleen voor de klanten die gebruikmaken van een Webex-gebruikers-id in e-mailindeling.
      > 
      > Voor meer informatie over het configureren van de Cisco WebEx-vergaderingen raadpleegt u de webpagina [over WebEx](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) .

### <a name="create-cisco-webex-meetings-test-user"></a>Testgebruiker maken in Cisco Webex Meetings

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in Cisco Webex Meetings. Cisco Webex Meetings biedt ondersteuning voor **Just-In-Time**-inrichting; dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Cisco Webex Meetings, wordt er een nieuwe gemaakt wanneer u Cisco Webex Meetings opent.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar Cisco WebEx-vergaderingen aanmelden URL waar u de aanmeldings stroom kunt initiëren.  

* Ga naar de aanmeldings-URL van Cisco WebEx-vergaderingen rechtstreeks en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Cisco Web-vergaderingen waarvoor u de SSO hebt ingesteld.

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de Cisco WebEx-vergaderingen tegel in de mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Cisco WebEx-vergaderingen waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Cisco WebEx-vergaderingen hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)