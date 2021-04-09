---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Cisco Webex | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Cisco Webex.
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
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Cisco Webex

In deze zelfstudie leert u hoe u Cisco Webex kunt integreren met Azure AD (Active Directory). Wanneer u Cisco Webex integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Cisco Webex.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Cisco Webex.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Cisco Webex-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cisco Webex biedt ondersteuning voor met **SP** geïnitieerde SSO.
* Cisco WebEx ondersteunt [**geautomatiseerde gebruikers inrichting**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voor het configureren van de integratie van Cisco Webex in Azure AD moet u Cisco Webex vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Cisco Webex**.
1. Selecteer **Cisco WebEx** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Azure AD SSO voor Cisco WebEx configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Cisco Webex met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Cisco Webex.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Cisco WebEx:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Cisco Webex configureren](#configure-cisco-webex)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Cisco Webex maken](#create-cisco-webex-test-user)** : als u een tegenhanger van B.Simon in Cisco Webex wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de **Cisco Web** Application Integration-pagina de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** uploadt u het bestand met metagegevens dat u hebt gedownload van de **serviceprovider** en configureert u de toepassing door de volgende stappen uit te voeren:

    >[!Note]
    >U ontvangt het metagegevensbestand van de serviceprovider in de sectie **Cisco Webex configureren**, die verderop in de zelfstudie wordt beschreven. 

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Als het uploaden van het metagegevensbestand van de serviceprovider is geslaagd, worden de waarden voor de **id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    Plak in het tekstvak **Aanmeldings-URL** de waarde van **Antwoord-URL**, die automatisch wordt ingevuld tijdens het uploaden van het SP-metagegevensbestand.

1. In de Cisco Webex-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Cisco Webex-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
  
    | Naam |  Bronkenmerk|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  De bron kenmerk waarde is standaard toegewezen aan userpricipalname. Dit kan worden gewijzigd in User. mail of User. onpremiseuserprincipalname of een andere waarde die wordt bepaald door de instelling in WebEx.


1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Cisco Webex.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cisco-webex"></a>Cisco Webex configureren

1. Meld u aan bij Cisco WebEx met uw beheerders referenties.

1. Selecteer **organisatie-instellingen** en klik onder de sectie **verificatie** op **wijzigen**.

    ![Schermopname met Settings waar u Modify kunt selecteren.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Selecteer **een id-provider van een derde partij integreren. (Geavanceerd)** en klik op **volgende**.

    ![Scherm afbeelding toont de integratie van een id-provider van een derde partij.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Klik op **bestand met meta gegevens downloaden** om het **bestand met meta gegevens van de service provider** te downloaden en op uw computer op te slaan, klikt u op **volgende**.

    ![Scherm opname toont het meta gegevensbestand van de service provider.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Klik op de optie **bestands browser** om het meta gegevensbestand van Azure ad te zoeken en te uploaden. Selecteer vervolgens **Require certificate signed by a certificate authority in Metadata (more secure)** en klik op **Next**.

    ![Schermopname met de pagina I d P Metadata.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Selecteer **Test SSO Connection**. Wanneer er een nieuw browsertabblad wordt geopend, moet u zich verifiëren bij Azure AD door u aan te melden.

1. Ga terug naar het browsertabblad **Cisco Cloud Collaboration Management**. Als de test is geslaagd, selecteert u **This test was successful.** Schakel de optie voor eenmalige aanmelding in en klik op **Next**.

1. Klik op **Opslaan**.

> [!NOTE]
> Raadpleeg [deze](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) pagina voor meer informatie over het configureren van de Cisco WebEx.

### <a name="create-cisco-webex-test-user"></a>Testgebruiker voor Cisco Webex maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Cisco WebEx. deze toepassing biedt ondersteuning voor automatische gebruikers inrichting, waarmee automatische inrichting en het ongedaan maken van de inrichting wordt ingeschakeld op basis van uw bedrijfs regels.  Micro soft raadt u aan om waar mogelijk automatische inrichting te gebruiken. Zie automatische inrichting inschakelen voor [Cisco WebEx](./cisco-webex-provisioning-tutorial.md).

Als u hand matig een gebruiker moet maken, voert u de volgende stappen uit:

1. Meld u aan bij Cisco WebEx met uw beheerders referenties.

2. Klik op **Users** en vervolgens op **Manage Users**.
   
    ![Schermopname met de pagina Users waar u gebruikers kunt beheren.](./media/cisco-spark-tutorial/user-1.png) 

3. Selecteer in het venster **gebruikers beheren** de optie **hand matig gebruikers toevoegen of wijzigen**.

    ![Scherm afbeelding toont de pagina gebruikers waar u gebruikers kunt beheren en hand matig gebruikers toevoegen of wijzigen selecteert.](./media/cisco-spark-tutorial/user-2.png)

4. Selecteer **Names and Email address**. Vul vervolgens de tekstvakken als volgt in:

    ![Schermopname met het dialoogvenster Manage Users waar u handmatig gebruikers kunt toevoegen of wijzigen.](./media/cisco-spark-tutorial/user-3.png) 

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, bijvoorbeeld **B**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld b.simon@contoso.com.

5. Klik op het plusteken om B.Simon toe te voegen. Klik op **Volgende**.

6. Klik in het venster **Services voor gebruikers toevoegen** op **gebruikers toevoegen** en vervolgens op **volt ooien**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Hiermee wordt omgeleid naar de aanmeldings-URL van Cisco WebEx, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Cisco WebEx en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de Cisco WebEx-tegel in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van Cisco WebEx. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Cisco WebEx hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)