---
title: 'Zelfstudie: Azure Active Directory-integratie met PolicyStat | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893013"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Zelfstudie: Azure Active Directory-integratie met PolicyStat

In deze zelfstudie leert u hoe u PolicyStat kunt integreren met Azure Active Directory (Azure AD).
De integratie van PolicyStat met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD regelen wie toegang tot PolicyStat heeft.
* U kunt instellen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij PolicyStat (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met PolicyStat hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op PolicyStat waarvoor eenmalige aanmelding is ingeschakeld

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PolicyStat biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

* PolicyStat biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-policystat-from-the-gallery"></a>PolicyStat toevoegen vanuit de galerie

Voor het configureren van de integratie van PolicyStat in Azure AD moet u PolicyStat uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om PolicyStat toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **PolicyStat** in het zoekvak, selecteer **PolicyStat** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![PolicyStat in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij PolicyStat configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in PolicyStat tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij PolicyStat te configureren en testen, moet u de volgende stappen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor PolicyStat configureren](#configure-policystat-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
4. **[Een testgebruiker maken in PolicyStat](#create-policystat-test-user)** : om in PolicyStat een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
5. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding met Azure Active Directory bij PolicyStat moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **PolicyStat**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij PolicyStat](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.policystat.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van PolicyStat](http://www.policystat.com/support/) om de waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

5. In de PolicyStat-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname met het dialoogvenster Gebruikerskenmerken waarin het pictogram Bewerken is geselecteerd.](common/edit-attribute.png)

6. Bovendien verwacht de PolicyStat-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.
    
    ![Schermopname met de sectie 'Gebruikersclaims' met de acties 'Nieuwe claim toevoegen' en 'Opslaan' gemarkeerd.](common/new-save-attribute.png)

    ![Schermopname met het dialoogvenster Gebruikersclaims beheren, waarin de tekstvakken Naam, Transformatie en Parameter zijn gemarkeerd en de knop Opslaan is geselecteerd.](./media/policystat-tutorial/attribute01.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron als **Transformatie**.

    e. Typ de kenmerkwaarde voor die rij in de lijst **Transformatie**.
    
    f. Typ de kenmerkwaarde voor die rij in de lijst **Parameter 1**.

    g. Klik op **Opslaan**.

7. Kopieer in de sectie **PolicyStat instellen** de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-policystat-single-sign-on"></a>Eenmalige aanmelding bij PolicyStat configureren

1. Meld u in een ander browservenster aan als beheerder bij uw PolicyStat-bedrijfswebsite.

2. Klik op het tabblad **Beheerder** en klik vervolgens op **Eenmalige aanmelding configureren** in het linker navigatiedeelvenster.
   
    ![Menu Beheerder](./media/policystat-tutorial/ic808633.png "Menu Beheerder"):

3. Klik op **Uw IDP-metagegevens** en voer vervolgens de volgende stappen uit in de sectie **Uw IDP-metagegevens**:
   
    ![Schermopname waarin de actie Your IDP Metadata is geselecteerd.](./media/policystat-tutorial/ic808636.png "Eenmalige aanmelding configureren")
   
    a. Open uw gedownloade metagegevensbestand, kopieer de inhoud en plak deze vervolgens in het tekstvak **Uw Identity Provider-metagegevens**.

    b. Klik op **Wijzigingen opslaan**.

4. Klik op **Kenmerken configureren** en voer vervolgens de volgende stappen uit in de sectie **Kenmerken configureren**:
   
    a. In het tekstvak **Kenmerk gebruikersnaam** typt u **uid**.

    b. Typ in het tekstvak **Kenmerk voornaam** de claimnaam van het voornaamkenmerk uit Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. Typ in het tekstvak **Kenmerk achternaam** de claimnaam van het achternaamkenmerk uit Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. Typ in het tekstvak **Kenmerk e-mailadres** de claimnaam van het e-mailadreskenmerk uit Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Klik op **Wijzigingen opslaan**.

5. Selecteer in de sectie **Instellen** de optie **Integratie van eenmalige aanmelding inschakelen**.
   
    ![Eenmalige aanmelding configureren](./media/policystat-tutorial/ic808634.png "Eenmalige aanmelding configureren")


### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u uw eigen account toestemming geven voor gebruik van eenmalige aanmelding met Azure door het toegang te geven tot PolicyStat.

1. Selecteer **Ondernemingstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **PolicyStat**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **PolicyStat** in de lijst met toepassingen.

    ![De PolicyStat-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** uw account in de lijst met gebruikers en klik op de knop **Selecteren** onderaan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-policystat-test-user"></a>PolicyStat-testgebruiker maken

In dit gedeelte maakt u in PolicyStat een gebruiker met de naam Britta Simon. PolicyStat biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in PolicyStat bestaat, wordt er een nieuwe gemaakt na de verificatie.

>[!NOTE]
>U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van PolicyStat gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PolicyStat in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van PolicyStat waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)