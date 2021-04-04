---
title: 'Zelfstudie: Azure Active Directory-integratie met Abstract | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Abstract.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: e1c3236c4c1957b4d0daee8d30c71f03fb8674dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587805"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Zelfstudie: Abstract integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Abstract integreert met Azure Active Directory (Azure AD). Wanneer u Abstract integreert met Azure AD, kunt u het volgende:

* Bepaal in Azure AD wie toegang heeft tot Abstract.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Abstract.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Abstract-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Abstract biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-abstract-from-the-gallery"></a>Abstract toevoegen vanuit de galerie

Voor het configureren van de integratie van Abstract met Azure Active Directory moet u Abstract vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Abstract** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Abstract** in het resultatenpaneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Abstract met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Abstract.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Abstract te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Abstract configureren](#configure-abstract-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Abstract-testgebruiker maken](#create-abstract-test-user)** : als u een tegenhanger van Britta Simon in Abstract wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Abstract** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.abstract.com/signin`

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Eenmalige aanmelding voor abstract configureren

Zorg ervoor dat u uw `App Federation Metadata Url` en de `Azure AD Identifier` ophaalt uit de Azure-portal, omdat u deze nodig hebt om eenmalige aanmelding te configureren voor Abstract.

U vindt deze informatie op de pagina **Eenmalige aanmelding met SAML instellen**:

* De `App Federation Metadata Url` bevindt zich in de sectie **SAML-handtekeningcertificaat**.
* De `Azure AD Identifier` bevindt zich in de sectie **Abstract instellen**.


U bent nu klaar om eenmalige aanmelding te configureren voor Abstract:

>[!Note]
>U moet zich verifiëren met de beheerdersaccount van een organisatie om toegang te krijgen tot de SSO-instellingen voor Abstract.

1. Open de [Abstract-web-app](https://app.abstract.com/).
2. Ga naar de pagina **Machtigingen** in de linkerzijbalk.
3. Voer in de sectie **Eenmalige aanmelding configureren** de **metagegevens-URL** en **Entiteits-ID** in.
4. Voer handmatige uitzonderingen in die u mogelijk hebt. E-mailadressen die worden vermeld in de sectie handmatige uitzonderingen, worden niet gebruikt voor eenmalige aanmelding en aanmelden is alleen mogelijk met een e-mailadres en wachtwoord. 
5. Klik op **Wijzigingen opslaan**.

>[!Note] 
>U moet primaire e-mailadressen gebruiken in de lijst met handmatige uitzonderingen. Het activeren van eenmalige aanmelding mislukt als de e-mail die u vermeldt een tweede e-mail van een gebruiker is. Als dat gebeurt, ziet u een foutbericht met het primaire e-mailadres voor het mislukte account. Voeg het primaire e-mailadres toe aan de lijst met handmatige uitzonderingen nadat u hebt gecontroleerd of u de gebruiker kent.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Abstract.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Abstract**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-abstract-test-user"></a>Een Abstract-testgebruiker maken

Eenmalige aanmelding voor Abstract testen:

1. Open de [Abstract-web-app](https://app.abstract.com/).
2. Ga naar de pagina **Machtigingen** in de linkerzijbalk.
3. Klik op **Testen met mijn account**. Als de test mislukt, kunt u [contact opnemen met het ondersteuningsteam](https://help.abstract.com/hc/).

>[!Note]
>U moet zich verifiëren met de beheerdersaccount van een organisatie om toegang te krijgen tot de instellingen voor eenmalige aanmelding voor Abstract.
Dit beheerdersaccount moet worden toegewezen aan Abstract vanaf de Azure-portal.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel van Abstract in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Abstract waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)