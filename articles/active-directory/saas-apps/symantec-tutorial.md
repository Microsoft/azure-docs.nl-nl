---
title: 'Zelfstudie: Azure Active Directory-integratie met Symantec Web Security Service (WSS) | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518361"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Zelfstudie: Azure Active Directory-integratie met Symantec Web Security Service (WSS)

In deze zelfstudie leert u hoe u uw account van Symantec Web Security Service (WSS) integreert met uw Azure Active Directory-account (Azure AD), zodat WSS een eindgebruiker die is ingericht in Azure AD kan verifiëren met behulp van SAML-verificatie en beleidsregels op gebruikers- of groepsniveau kan afdwingen.

De integratie van Symantec Web Security Service (WSS) met Azure AD heeft de volgende voordelen:

- Alle eindgebruikers en groepen die worden gebruikt door uw WSS-account beheren via de Azure AD-portal.

- Eindgebruikers toestaan om zich met hun Azure AD-referenties te verifiëren in WSS.

- Beleidsregels die zijn gedefinieerd in uw WSS-account afdwingen op gebruikers- en groepsniveau.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Symantec Web Security Service (WSS):

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Symantec Web Security Service (WSS) waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Symantec Web Security Service (WSS) biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) toevoegen vanuit de galerie

Om de integratie van Symantec Web Security Service (WSS) met Azure AD te configureren, moet u Symantec Web Security Service (WSS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Symantec Web Security Service (WSS) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Symantec Web Security Service (WSS)** in het zoekvak, selecteer **Symantec Web Security Service (WSS)** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![Symantec Web Security Service (WSS) toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding bij Symantec Web Security Service (WSS) met Azure AD configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Symantec Web Security Service (WSS) tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Symantec Web Security Service (WSS), moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **Eenmalige aanmelding voor Symantec Web Security Service (WSS) configureren**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Symantec Web Security Service (WSS) maken](#create-symantec-web-security-service-wss-test-user)**: een tegenhanger voor Britta Simon maken in Symantec Web Security Service (WSS) die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Symantec Web Security Service (WSS):

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Symantec Web Security Service (WSS)** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. In het tekstvak **Antwoord-URL** typt u een URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Neem contact op met het [klantondersteuningsteam van Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) als de waarden voor **Id** en **Antwoord-URL** onverhoopt niet werken. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Eenmalige aanmelding configureren voor Symantec Web Security Service (WSS)

Raadpleeg de online documentatie van WSS voor het configureren van eenmalige aanmelding in Symantec Web Security Service (WSS). Het gedownloade XML-bestand met **federatieve metagegevens** moet worden geïmporteerd in de WSS-portal. Neem contact op met het [ondersteuningsteam van Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) als u hulp nodig hebt bij de configuratie in de WSS-portal.

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

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Symantec Web Security Service (WSS).

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Symantec Web Security Service (WSS)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Symantec Web Security Service (WSS)** in de lijst met toepassingen.

    ![De koppeling Symantec Web Security Service (WSS) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Testgebruiker maken voor Symantec Web Security Service (WSS)

In deze sectie maakt u een gebruiker met de naam Britta Simon in Symantec Web Security Service (WSS). De bijbehorende eindgebruikersnaam kan handmatig worden gemaakt in de WSS-portal of u kunt een paar minuten (ongeveer 15 minuten) wachten totdat de gebruikers/groepen die zijn ingericht in Azure AD zijn gesynchroniseerd met de WSS-portal. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. Het openbare IP-adres van de computer van de eindgebruiker, dat wordt gebruikt om te browsen op websites, moet ook worden ingericht in de WSS-portal (Symantec Web Security Service).

> [!NOTE]
> Klik [hier](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) om het openbare IP-adres van uw computer op te halen.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u de functionaliteit van eenmalige aanmelding testen nu het WSS-account is geconfigureerd voor het gebruik van Azure AD voor SAML-verificatie.

Nadat u in uw webbrowser hebt ingesteld dat verkeer via de proxy moet worden omgeleid naar WSS, wordt de aanmeldingspagina van Azure weergegeven wanneer u uw webbrowser opent en naar een site gaat. Voer de referenties in van de eindgebruiker die is ingericht in Azure AD (BrittaSimon in dit geval), evenals het bijbehorende wachtwoord. Als de verificatie is voltooid, kunt u naar de gewenste website browsen. Als u in WSS een beleidsregel definieert om te voorkomen dat BrittaSimon naar een bepaalde site kan browsen, ziet u vervolgens een blokkeringsbericht van WSS wanneer u probeert om als BrittaSimon naar die site te gaan.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)