---
title: 'Zelfstudie: Azure Active Directory-integratie met Infinite Campus | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Infinite Campus.
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
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460226"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Zelfstudie: Azure Active Directory-integratie met Infinite Campus

In deze zelfstudie leert u hoe u Infinite Campus kunt integreren met Azure Active Directory (Azure AD).
De integratie van Infinite Campus met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie er toegang tot Infinite Campus heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Infinite Campus (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Infinite Campus hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Infinite Campus waarvoor eenmalige aanmelding is ingeschakeld
* U moet ten minste een Azure Active Directory beheerder zijn en beschikken over de rol van Campus Product Security Role 'Student Information System (SIS)' om de configuratie te voltooien.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Infinite Campus ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-infinite-campus-from-the-gallery"></a>Infinite Campus toevoegen vanuit de galerie

Voor het configureren van de integratie van Infinite Campus met Azure AD moet u Infinite Campus vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Infinite Campus vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Infinite Campus** in het zoekvak, selecteer **Infinite Campus** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Infinite Campus in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Infinite Campus configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Infinite Campus tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij Infinite Campus moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Infinite Campus configureren](#configure-infinite-campus-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Infinite Campus-testgebruiker maken](#create-infinite-campus-test-user)** : als u een equivalent van Britta Simon in Infinite Campus wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Infinite Campus moet u de volgende stappen uitvoeren:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **Infinite Campus** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie Standaard SAML-configuratie (het domein verschilt afhankelijk van het hostingmodel, maar de waarde voor **FQDN** moet overeenkomen met de installatie van Infinite Campus):

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Typ in het tekstvak **Id** een URL met het volgende patroon: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Informatie over eenmalige aanmelding bij het Infinite Campus-domein en Infinite Campus-URL's](common/sp-identifier-reply.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Eenmalige aanmelding voor Infinite Campus configureren

1. Meld u in een ander browservenster bij Infinite Campus aan als een beveiligingsbeheerder.

2. Klik aan de linkerkant in het menu op **System Administration**.

    ![De Beheerder](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Ga naar **User Security** > **SAML Management** > **SSO Service Provider Configuration**.

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Voer op de pagina **SSO Service Provider Configuration** de volgende stappen uit:

    ![Eenmalige aanmelding](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecteer **Enable SAML Single Sign On**.

    b. Wijzig het veld **Optional Attribute Name** zodat hier **name** wordt weergegeven.

    c. Selecteer in de sectie **Select an option to retrieve Identity Provider (IDP) server data** het keuzerondje **Metadata URL**, plak de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit Azure Portal in het vak en klik vervolgens op **Sync**.

    d. Als u op **Sync** hebt geklikt, worden de waarden automatisch ingevuld op de pagina **SSO Service Provider Configuration**. Deze waarden kunnen worden gecontroleerd zodat ze overeenkomen met de waarden die in stap 4 hierboven worden weergegeven.

    e. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

> [!NOTE]
> Als u wilt dat al uw Azure-gebruikers via eenmalige aanmelding toegang hebben tot Infinite Campus en u gebruik wilt maken van het interne machtigingensysteem van Infinite Campus om de toegang te beheren, kunt u de eigenschap **Gebruikerstoewijzing vereist** van de toepassing instellen op Nee en de volgende stappen overslaan.

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Infinite Campus.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Infinite Campus**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Infinite Campus** in de lijst met toepassingen.

    ![De koppeling naar Infinite Campus in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-infinite-campus-test-user"></a>Infinite Campus-testgebruiker maken

Infinite Campus heeft een architectuur die is gebaseerd op demografische gegevens. Neem contact op met het [ondersteuningsteam van Infinite Campus](mailto:sales@infinitecampus.com) om de gebruikers aan het Infinite Campus-platform toe te voegen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Infinite Campus klikt, wordt u als het goed is automatisch aangemeld bij de instantie van Infinite Campus waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)