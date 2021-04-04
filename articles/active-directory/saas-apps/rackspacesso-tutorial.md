---
title: 'Zelfstudie: Azure Active Directory-integratie met Rackspace SSO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Rackspace SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: eed0ea075c5e9eb39810259be2aa6781a59f2f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511644"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Zelfstudie: Azure Active Directory-integratie met Rackspace SSO

In deze zelfstudie leert u hoe u Rackspace SSO kunt integreren met Azure Active Directory (Azure AD).
De integratie van Rackspace SSO met Azure Active Directory heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Rackspace SSO.
* Uw gebruikers kunnen zich automatisch aanmelden bij Rackspace SSO (eenmalige aanmelding) met hun Azure Active Directory-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Rackspace SSO wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een Rackspace SSO-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rackspace SSO ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-rackspace-sso-from-the-gallery"></a>Rackspace SSO toevoegen vanuit de galerie

Voor de configuratie van de integratie van Rackspace SSO in Azure Active Directory, moet u Rackspace SSO vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

**Voer de volgende stappen uit om Rackspace SSO toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Rackspace SSO**, selecteer **Rackspace SSO** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Rackspace SSO in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Azure Active Directory met Rackspace SSO configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Wanneer u eenmalige aanmelding met Rackspace gebruikt, worden de Rackspace-gebruikers automatisch gemaakt wanneer ze zich voor de eerste keer aanmelden bij de Rackspace-portal. 

Voer de volgende stappen uit als u eenmalige aanmelding bij Azure Active Directory met Rackspace SSO wilt configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Rackspace SSO configureren](#configure-rackspace-sso-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
1. **[Kenmerktoewijzing instellen in het Rackspace-configuratiescherm](#set-up-attribute-mapping-in-the-rackspace-control-panel)** : als u Rackspace-rollen wilt toewijzen aan Azure AD-gebruikers.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Rackspace SSO te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de **Rackspace SSO**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** uploadt u het **bestand met metagegevens van de serviceprovider** dat u kunt downloaden via de [URL](https://login.rackspace.com/federate/sp.xml) en voert u de volgende stappen uit:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Schermopnamen met Standaard SAML-configuratie, met de koppeling voor het uploaden van een bestand met metagegevens.](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Schermopname van een dialoogvenster, waar u een bestand kunt selecteren en uploaden.](common/browse-upload-metadata.png)

    c. Zodra het metagegevensbestand is geüpload, worden de benodigde url's automatisch gevuld.

    d. Typ een URL in het tekstvak **Aanmeldings-URL**: `https://login.rackspace.com/federate/`

    ![Informatie over eenmalige aanmelding bij Rackspace SSO-domeinen en -URL's](common/sp-signonurl.png)   

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

Dit bestand wordt geüpload naar Rackspace om de vereiste federatieve configuratie-instellingen voor id's te vullen.

### <a name="configure-rackspace-sso-single-sign-on"></a>Eenmalige aanmelding van Rackspace SSO configureren

Eenmalige aanmelding configureren op **Rackspace SSO**:

1. Zie de documentatie bij [Een id-provider toevoegen aan het configuratiescherm](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Hiermee wordt u stapsgewijs begeleid door de volgende stappen:
    1. Een nieuwe id-provider maken
    1. Geef een e-maildomein op dat door gebruikers wordt gebruikt om uw bedrijf te identificeren bij het aanmelden.
    1. Upload het **XML-bestand met federatieve metagegevens** dat u eerder vanuit het Azure-configuratiescherm hebt gedownload.

Hiermee worden de basisinstellingen voor eenmalige aanmelding die nodig zijn voor Azure en Rackspace, correct geconfigureerd om verbinding te maken.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Rackspace SSO.

1. Selecteer **Bedrijfstoepassingen** in de Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Rackspace SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rackspace SSO** in de lijst met toepassingen.

    ![De Rackspace SSO-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Kenmerktoewijzing instellen in het Rackspace-configuratiescherm

Rackspace maakt gebruik van **beleid voor kenmerktoewijzing** om Rackspace-rollen en -groepen toe te wijzen aan uw gebruikers met eenmalige aanmelding. Het **beleid voor kenmerktoewijzing** zet Azure AD SAML-claims om naar de gebruikersconfiguratievelden die Rackspace nodig heeft. Meer informatie vindt u in de [basisdocumentatie over kenmerktoewijzing](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) van Rackspace. Enkele aandachtspunten:

* Als u verschillende niveaus van Rackspace-toegang wilt toewijzen met behulp van Azure AD-groepen, moet u de groepsclaim inschakelen in de **Rackspace SSO-instellingen voor eenmalige aanmelding** van Azure. Het **beleid voor kenmerktoewijzing** wordt vervolgens gebruikt om die groepen te vergelijken met de gewenste Rackspace-rollen en -groepen:

    ![De instellingen voor groepsclaims](common/sso-groups-claim.png)

* Standaard verzendt Azure AD de UID van Azure AD-groepen in de SAML-claim, op basis van de naam van de groep. Als u uw on-premises Active Directory echter synchroniseert met Azure AD, hebt u de mogelijkheid om de werkelijke namen van de groepen te verzenden:

    ![De naaminstellingen voor groepsclaims](common/sso-groups-claims-names.png)

In het volgende voorbeeld van **beleid voor kenmerktoewijzing** ziet u dat:
1. De naam van de Rackspace-gebruiker wordt ingesteld op de `user.name` SAML-claim. Elke claim kan worden gebruikt, maar dit is het meest gebruikelijk om dit in te stellen op een veld met het e-mailadres van de gebruiker.
1. Het instellen van de Rackspace-rollen `admin` en `billing:admin` voor een gebruiker door deze te koppelen aan een Azure AD-groep, via de groepsnaam of groeps-UID. Er wordt een *vervanging* van `"{0}"` in het veld `roles` gebruikt en dit wordt vervangen door de resultaten van de regelexpressies `remote`.
1. Met behulp van de `"{D}"` *standaardvervanging* kan Rackspace aanvullende SAML-velden ophalen door te zoeken naar standaard en bekende SAML-claims in de SAML-uitwisseling.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Zorg dat u een teksteditor gebruikt die de syntaxis van YAML valideert bij het bewerken van uw beleidsbestand.

Zie de [basisdocumentatie over kenmerktoewijzing](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) van Rackspace voor meer voorbeelden.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Rackspace SSO in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Rackspace SSO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

U kunt ook de knop **Valideren** gebruiken in de instellingen van de eenmalige aanmelding van **Rackspace SSO**:

   ![Knop SSO valideren](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)