---
title: 'Zelfstudie: Azure Active Directory-integratie met Recognize | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Recognize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 7195a126938deabf542e6fa3e52b72c2efdddac2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515216"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Zelfstudie: Azure Active Directory-integratie met Recognize

In deze zelfstudie leert u hoe u Recognize integreert met Azure Active Directory (Azure AD).
De integratie van Recognize met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Recognize.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Recognize (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met Recognize te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Recognize-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Recognize ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-recognize-from-the-gallery"></a>Recognize toevoegen vanuit de galerie

Voor het configureren van de integratie van Recognize met Azure AD moet u Recognize uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Recognize vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Recognize**, selecteer **Recognize** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Recognize in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD bij Recognize op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Recognize.

Als u eenmalige aanmelding van Azure AD bij Recognize wilt configureren en testen, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Recognize configureren](#configure-recognize-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Recognize maken](#create-recognize-test-user)** : als u in Recognize een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD bij Recognize wilt configureren:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de pagina voor toepassingsintegratie van **Recognize** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    >[!NOTE]
    >U krijgt het **metagegevensbestand van de serviceprovider** via de sectie **Eenmalige aanmelding voor Recognize configureren** van de zelfstudie.

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Wanneer het metagegevensbestand is geüpload, wordt de waarde voor **Id** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Recognize](common/sp-identifier.png)

     In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Als de waarde voor **Id** niet automatisch wordt ingevuld, krijgt u de id-waarde door de URL voor de metagegevens van de serviceprovider te openen in de sectie Instellingen voor eenmalige aanmelding, die later wordt beschreven in de sectie **Eenmalige aanmelding voor Recognize configureren** van de zelfstudie. De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Recognize](mailto:support@recognizeapp.com) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Recognize instellen** de betreffende URL('s) naargelang uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-recognize-single-sign-on"></a>Eenmalige aanmelding voor Recognize configureren

1. Meld u in een ander browservenster als beheerder aan bij de Recognize-tenant.

2. Klik in de rechterbovenhoek op **Menu**. Ga naar **Company Admin** (Bedrijfsbeheer).
   
    ![Schermopname met Company Admin geselecteerd in het instellingenmenu.](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Klik in het linkernavigatiedeelvenster op **Settings** (Instellingen).
   
    ![Schermopname met Settings geselecteerd op de navigatiepagina.](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Voer in de sectie **Instellingen voor eenmalige aanmelding** de volgende stappen uit.
   
    ![Schermopname van S S O Settings, waarin u de beschreven waarden kunt invoeren.](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Selecteer **AAN** voor **Eenmalige aanmelding inschakelen**.

    b. Plak in het tekstvak **IDP-entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd uit Azure Portal.
    
    c. Plak in het tekstvak **Doel-URL voor eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.
    
    d. Plak in het tekstvak **Doel-URL voor eenmalige aanmelding** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal. 
    
    e. Open het gedownloade bestand **Certificate(Base64)** in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze vervolgens in het tekstvak **Certificaat**.
    
    f. Klik op de knop **Instellingen opslaan**. 

5. Kopieer naast de sectie **Instellingen voor eenmalige aanmelding** de URL onder **URL voor de metagegevens van de serviceprovider**.
   
    ![Schermopname van Notes (Notities), waar u de metagegevens voor de serviceprovider kunt kopiëren.](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Open de **koppeling met de metagegevens-URL** in een lege browser om het document met metagegevens te downloaden. Kopieer vervolgens de EntityDescriptor-waarde (entityID) uit het bestand en plak deze in het tekstvak **Id** in **Standaard SAML-configuratie** in Azure Portal.
    
    ![Schermopname van een tekstvak met X M L in tekst zonder opmaak, waar u de entiteit-id kunt ophalen.](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot Recognize.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Recognize**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Recognize** in de lijst met toepassingen.

    ![De Recognize-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-recognize-test-user"></a>Een Recognize-testgebruiker maken

Als u gebruikers van Azure AD de mogelijkheid wilt bieden om zich aan te melden bij Recognize, moeten ze worden ingericht voor Recognize. In het geval van Recognize is inrichten een handmatige taak.

Deze app biedt geen ondersteuning voor SCIM-inrichting, maar beschikt over een alternatieve gebruikerssynchronisatie waarmee gebruikers worden ingericht. 

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de Recognize-bedrijfssite.

2. Klik in de rechterbovenhoek op **Menu**. Ga naar **Bedrijfsbeheerder**.

3. Klik in het linkernavigatiedeelvenster op **Instellingen**.

4. Voer in de sectie **Gebruikerssynchronisatie** de volgende stappen uit.
   
    ![Nieuwe gebruiker](./media/recognize-tutorial/tutorial_recognize_005.png "Nieuwe gebruiker")
   
    a. Selecteer **AAN** voor **Synchronisatie ingeschakeld**.
   
    b. Selecteer **Microsoft/Office 365** voor **Een synchronisatieprovider kiezen**.
   
    c. Klik op **Gebruikerssynchronisatie uitvoeren**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Recognize-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de Recognize-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)