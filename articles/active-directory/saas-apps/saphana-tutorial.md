---
title: 'Zelfstudie: Integratie van Azure Active Directory met SAP HANA'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP HANA.
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
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727078"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Zelfstudie: Azure Active Directory-integratie met SAP HANA

In deze zelfstudie leert u hoe u SAP HANA kunt integreren met Azure Active Directory (Azure AD). Wanneer u SAP HANA integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD bepalen wie er toegang heeft tot SAP HANA.
* Zorg ervoor dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP HANA.
* Uw accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met SAP HANA hebt u de volgende zaken nodig:

- Een Azure AD-abonnement
- Een SAP HANA-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld
- Een HANA-exemplaar dat actief is in een openbare IaaS, on-premises, in een Azure-VM of in grote SAP-exemplaren in Azure
- De XSA-webbeheerinterface en HANA Studio, geïnstalleerd in het HANA-exemplaar

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de SAP HANA-productieomgeving te gebruiken. Test de integratie eerst in de ontwikkel- of faseringsomgeving van de toepassing. Gebruik dan pas de productieomgeving.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op SAP HANA waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP HANA biedt ondersteuning voor door **IDP** geïnitieerde SSO
* SAP HANA biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.


## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP HANA met Azure AD moet u SAP HANA uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **SAP HANA** in het zoekvak.
1. Selecteer **SAP HANA** in het resultatenvenster en voeg daarna de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SAP HANA

Eenmalige aanmelding van Azure AD configureren en testen voor SAP HANA met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP HANA.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor SAP HANA:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor SAP HANA configureren](#configure-sap-hana-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[De testgebruiker voor SAP HANA maken](#create-sap-hana-test-user)**: als u een equivalent van Britta Simon in SAP HANA wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **SAP HANA**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van SAP HANA](https://cloudplatform.sap.com/contact.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. De toepassing SAP HANA verwacht de SAML-asserties in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die de sectie 'Gebruikerskenmerken' toont met het pictogram 'Bewerken' geselecteerd.](common/edit-attribute.png)

6. Voer in het gedeelte **Gebruikerskenmerken** in het dialoogvenster **Gebruikerskenmerken en claims** de volgende stappen uit:
 
    a. Klik op **pictogram bewerken** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname met het dialoogvenster Gebruikerskenmerken en claims, waarin het pictogram Bewerken is geselecteerd.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Selecteer in de lijst **Transformatie****ExtractMailPrefix()**.

    c. In de lijst **Parameter 1** selecteert u **user.mail**.

    d. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SAP HANA.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP HANA** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-hana-sso"></a>Eenmalige aanmelding voor SAP HANA configureren

1. Als u eenmalige aanmelding wilt configureren aan de SAP HANA-zijde, meldt u zich aan bij de **HANA XSA-webconsole** via het bijbehorende HTTPS-eindpunt.

    > [!NOTE]
    > In de standaardconfiguratie leidt de URL de aanvraag om naar een aanmeldingsscherm. Hier moet u de referenties van een geverifieerde SAP HANA-databasegebruiker opgeven. De gebruiker die zich aanmeldt, moet machtigingen hebben voor het uitvoeren van SAML-beheertaken.

2. Ga in de XSA-webinterface naar de **SAML-id-provider**. Selecteer daar de knop **+** aan de onderzijde van het scherm. Het deelvenster **Info id-provider toevoegen** wordt weergegeven. Voer dan de volgende stappen uit:

    ![Id-provider toevoegen](./media/saphana-tutorial/sap1.png)

    a. In het deelvenster **Info id-provider toevoegen** plakt u de inhoud van het XML-bestand met metagegevens in het vak **Metagegevens**. Het bestand hebt u uit Azure Portal gedownload.

    ![Schermopname van het deelvenster Add Identity Provider Info, waarin de vakken Metadata en Name zijn gemarkeerd.](./media/saphana-tutorial/sap2.png)

    b. Als de inhoud van het XML-document geldig is, wordt bij het parseren de informatie geëxtraheerd die nodig is voor de velden **Onderwerp, Entiteits-id en Verlener** in het schermgedeelte **Algemene gegevens**. Er wordt ook informatie uitgepakt die nodig is voor de URL-velden in het schermgedeelte **Bestemming**, bijvoorbeeld de **basis-URL en de URL voor eenmalige aanmelding(*)**.

    ![Instellingen voor het toevoegen van een id-provider](./media/saphana-tutorial/sap3.png)

    c. In het vak **Naam** van het gedeelte **Algemene gegevens** voert u een naam in voor de nieuwe SAML-id-provider voor eenmalige aanmelding.

    > [!NOTE]
    > Het is verplicht om de naam van de SAML-id-provider op te geven. De naam moet uniek zijn. De naam wordt weergegeven in de lijst beschikbare SAML-id-providers die wordt getoond wanneer u SAML selecteert als verificatiemethode voor SAP HANA XS-toepassingen. U kunt dit bijvoorbeeld doen in het schermgedeelte **Verificatie** van het beheerprogramma voor XS-artefacten.

3. Selecteer **Opslaan** om de details van de SAML-id-provider op te slaan en om de nieuwe SAML-id-provider toe te voegen aan de lijst bekende SAML-id-providers.

    ![De knop Opslaan](./media/saphana-tutorial/sap4.png)

4. Ga in HANA Studio naar de systeemeigenschappen op het tabblad **Configuratie** en filter de instellingen op **saml**. Verander dan **assertion_timeout** van **10 sec** in **120 sec**.

    ![De instelling assertion_timeout](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Een SAP HANA-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SAP HANA, moet u ze in SAP HANA inrichten.
SAP HANA biedt ondersteuning voor **just-in-time** inrichten, dat standaard is ingeschakeld.

Als de gebruiker handmatig moet maken, voert u de volgende stappen uit:

>[!NOTE]
>U kunt wijzigen welke externe verificatie de gebruiker gebruikt. De gebruiker kan zich verifiëren via een extern systeem zoals Kerberos. Neem contact op met uw [domeinbeheerder](https://cloudplatform.sap.com/contact.html) voor gedetailleerde informatie over externe identiteiten.

1. Open [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) als beheerder en schakel dan SAML-SSO in voor de DB-gebruiker.

    ![Gebruiker maken](./media/saphana-tutorial/sap5.png)

2. Schakel het onzichtbare selectievakje links van **SAML** in en selecteer vervolgens de optie **Configureren**.

3. Selecteer **Toevoegen** om de SAML-id-provider toe te voegen.  Selecteer de juiste SAML-id-provider en selecteer vervolgens **OK**.

4. Voeg de **externe id** toe (in dit geval BrittaSimon) of kies **Willekeurig**. Selecteer vervolgens **OK**.

   > [!Note]
   > Als het selectievakje bij **Willekeurig** niet is ingeschakeld, moet de gebruikersnaam in HANA exact overeenkomen met de naam van de gebruiker in de UPN voor het domeinachtervoegsel. (BrittaSimon@contoso.com wordt bijvoorbeeld BrittaSimon in HANA.)

5. Voor testdoeleinden wijst u alle **XS**-rollen toe aan de gebruiker.

    ![Rollen toewijzen](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Verleen alleen machtigingen die nodig zijn in de betreffende situaties.

6. Sla de gebruiker op.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op 'Deze toepassing testen'. U wordt automatisch aangemeld bij de instantie van SAP HANA waarvoor u eenmalige aanmelding hebt ingesteld

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in 'Mijn apps' op de tegel 'SAP HANA' klikt, zou u automatisch moeten worden aangemeld bij de instantie van SAP HANA waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u SAP HANA hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).