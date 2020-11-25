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
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 748566cf5b25157fcb8a2938a3f4bd307c930874
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993211"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Zelfstudie: Azure Active Directory-integratie met SAP HANA

In deze zelfstudie leert u hoe u SAP HANA integreert met Azure Active Directory (Azure AD).
De integratie van SAP HANA met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot SAP HANA heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAP HANA (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

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

## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP HANA met Azure AD moet u SAP HANA uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u SAP HANA vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAP HANA** in het zoekvak, selecteer **SAP HANA** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![SAP HANA in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte kunt u eenmalige aanmelding via Azure AD configureren en testen met SAP HANA op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAP HANA tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij SAP HANA moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SAP HANA configureren](#configure-sap-hana-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[De testgebruiker voor SAP HANA maken](#create-sap-hana-test-user)**: als u een equivalent van Britta Simon in SAP HANA wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met SAP HANA moet u de volgende stappen uitvoeren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de pagina voor integratie van de **SAP HANA**-toepassing **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij het SAP HANA-domein en SAP HANA-URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u het volgende: `HA100`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [SAP HANA-klantondersteuningsteam](https://cloudplatform.sap.com/contact.html) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

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

### <a name="configure-sap-hana-single-sign-on"></a>Eenmalige aanmelding configureren voor SAP HANA

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

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte schakelt u Britta Simon in voor gebruik van eenmalige aanmelding met Azure door toegang te verlenen aan SAP HANA.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **SAP HANA**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **SAP HANA** in de lijst met toepassingen.

    ![De SAP HANA-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

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

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP HANA in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van SAP HANA waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)