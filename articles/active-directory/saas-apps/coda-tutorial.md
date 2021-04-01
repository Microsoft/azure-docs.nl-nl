---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Coda | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: c78744538991e337b226e49a053807ef8174d507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455728"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Coda

In deze zelfstudie leert u hoe u Coda integreert met Azure Active Directory (Azure AD). Wanneer u Coda integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie er toegang heeft tot Coda.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Coda.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Coda (Enterprise) waarvoor eenmalige aanmelding is ingeschakeld en GDrive-integratie is uitgeschakeld. Neem contact op met het [ondersteuningsteam van Coda](mailto:support@coda.io) om GDrive-integratie uit te schakelen voor uw organisatie als dit momenteel is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Coda ondersteunt eenmalige aanmelding via **IDP**

* Coda biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

* Wanneer u Coda hebt geconfigureerd, kunt u sessiebeheer afdwingen. Dit biedt realtime bescherming tegen de exfiltratie en infiltratie van gevoelige bedrijfsgegevens. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Coda toevoegen vanuit de galerie

Als u de integratie van Coda met Azure AD wilt configureren, voegt u Coda vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Coda** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Coda** in het venster met resultaten en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Coda

Configureer en test eenmalige aanmelding van Azure AD met Coda met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Coda.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Coda te configureren en te testen:

1. **[Configuratie van eenmalige aanmelding bij Coda starten](#begin-configuration-of-coda-sso)** : als u wilt beginnen met de configuratie van eenmalige aanmelding in CODA.
1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
   * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
   * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Coda configureren](#configure-coda-sso)** : als u de instellingen voor eenmalige aanmelding bij Coda wilt configureren.
   * **[Coda-testgebruiker maken](#create-coda-test-user)** : als u een tegenhanger van B.Simon in Coda wilt maken die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="begin-configuration-of-coda-sso"></a>Configuratie van eenmalige aanmelding bij Coda starten

Volg deze stappen in Coda om aan de slag te gaan.

1. Open in Coda het venster **Organisatie-instellingen**.

   ![Organisatie-instellingen openen](media/coda-tutorial/org-settings.png)

1. Zorg dat GDrive-integratie is uitgeschakeld voor uw organisatie. Als deze momenteel is ingeschakeld, neemt u contact op met het [ondersteuningsteam van Coda](mailto:support@coda.io) om u te helpen bij het uitschakelen van GDrive.

   ![GDrive is uitgeschakeld](media/coda-tutorial/gdrive-off.png)

1. Selecteer onder **Verificatie met eenmalige aanmelding (SAML)** de optie **SAML configureren**.

   ![SAML-instellingen](media/coda-tutorial/saml-settings-link.png)

1. Noteer de waarden voor **Entiteits-id** en **URL SAML-antwoord**. U hebt deze in de volgende stappen nodig.

   ![Entiteits-id en URL van SAML-antwoord voor gebruik in Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Coda** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

   a. Voer in het tekstvak **Id** de Entiteits-id in die u eerder hebt genoteerd. Deze volgt dit patroon: `https://coda.io/samlId/<CUSTOMID>`

   b. Voer in het tekstvak **Antwoord-URL** de URL SAML-antwoord in die u eerder hebt genoteerd. Deze volgt dit patroon: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Uw waarden verschillen van de hierboven genoemde waarden. U kunt uw waarden vinden in de console SAML configureren van Coda. Werk deze waarden bij met de werkelijke id en antwoord-URL.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Coda instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Coda.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Coda** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

   ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-coda-sso"></a>Eenmalige aanmelding bij Coda configureren

Als u de installatie wilt voltooien, voert u in het venster **SAML configureren** van Coda waarden in van Azure Active Directory.

1. Open in Coda het venster **Organisatie-instellingen**.
1. Selecteer onder **Verificatie met eenmalige aanmelding (SAML)** de optie **SAML configureren**.
1. Stel **SAML-provider** in bij **Azure Active Directory**.
1. Plak in **Aanmeldings-URL identiteitsprovider** de **aanmeldings-URL** vanuit de Azure-console.
1. Plak in **Verlener identiteitsprovider** de **Azure AD-id** vanuit de Azure-console.
1. In **Openbaar certificaat identiteitsprovider** selecteert u de optie **Certificaat uploaden** en selecteert u het certificaatbestand dat u eerder hebt gedownload.
1. Selecteer **Opslaan**.

U bent nu klaar met het instellen van de SAML-verbinding voor eenmalige aanmelding.

### <a name="create-coda-test-user"></a>Een testgebruiker voor Coda maken

In deze sectie wordt een gebruiker met de naam Britta Simon in Coda gemaakt. Coda biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Coda bestaat, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de Coda-tegel klikt, wordt u automatisch aangemeld bij het Coda-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Coda proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Coda beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)