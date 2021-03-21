---
title: 'Zelfstudie: Azure Active Directory-integratie met Kontiki | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Kontiki.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95019837"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Zelfstudie: Azure Active Directory-integratie met Kontiki

In deze zelfstudie leert u hoe u Kontiki integreert met Azure AD (Active Directory).

Kontiki-integratie met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang krijgt tot Kontiki.
* Gebruikers kunnen automatisch worden aangemeld bij Kontiki met hun Azure AD-accounts (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (software als een service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Kontiki hebt u het volgende nodig:

* Een Azure AD-abonnement Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Kontiki-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een testomgeving, en integreert u Kontiki met Azure AD.

Kontiki biedt ondersteuning voor de volgende functies:

* **Met SP geïnitieerde eenmalige aanmelding**
* **Just In Time inrichten van gebruikers**

## <a name="add-kontiki-in-the-azure-portal"></a>Kontiki toevoegen in Azure Portal

Om Kontiki te integreren met Azure AD moet u Kontiki toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de knop **Azure Active Directory** in het linkermenu.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak in: **Kontiki**. Selecteer **Kontiki** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

    ![Kontiki in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Kontiki op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kontiki.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Kontiki, moet u de volgende procedures uitvoeren:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding bij Kontiki configureren](#configure-kontiki-single-sign-on)** | Configureert de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Stelt Britta Simon in om gebruik te maken van eenmalige aanmelding via Azure AD. |
| **[Een Kontiki-testgebruiker maken](#create-a-kontiki-test-user)** | Maakt een tegenhanger van Britta Simon in Kontiki die is gekoppeld aan de Azure AD-weergave van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In dit gedeelte configureert u eenmalige aanmelding van Azure AD met Kontiki in Azure Portal.

1. Selecteer in [Azure Portal](https://portal.azure.com/) in het integratiedeelvenster van de toepassing **Kontiki** de optie **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Een methode voor eenmalige aanmelding selecteren** de modus **SAML** of **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (het potloodpictogram) om het deelvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** in het tekstvak **Aanmeldings-URL** een URL in met het volgende patroon: `https://<companyname>.mc.eval.kontiki.com`

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Kontiki](common/sp-signonurl.png)

    > [!NOTE]
    > Neem contact op met het [klantondersteuningsteam van Kontiki](https://kollective.com/support/) om de juiste waarde voor gebruik op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** naast **XML-bestand met federatieve metagegevens**. Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De optie voor het downloaden van het XML-certificaat met federatieve metagegevens](common/metadataxml.png)

1. Kopieer in de sectie **Kontiki instellen** de juiste URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Eenmalige aanmelding bij Kontiki configureren

Als u eenmalige aanmelding aan de zijde van Kontiki wilt configureren, verzendt u het gedownloade XML-bestand met federatieve gegevens en de relevante URL’s die u hebt gekopieerd uit Azure Portal, naar het [ondersteuningsteam van Kontiki](https://kollective.com/support/). Het ondersteuningsteam van Kontiki gebruikt de informatie die u verzendt, om ervoor te zorgen dat de SAML-verbinding voor eenmalige aanmelding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![De opties Gebruikers en Alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam** **Britta Simon** in.
  
    1. Voer in het vak **Gebruikersnaam** **brittasimon\@\<your-company-domain> in.\<extension>** . Bijvoorbeeld **brittasimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

    ![Het deelvenster Gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Britta Simon toegang tot Kontiki zodat ze eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen** > **Alle toepassingen** > **Kontiki**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Kontiki** in de lijst met toepassingen.

    ![Kontiki in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** de optie **Britta Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde in de SAML-assertie verwacht, selecteert u in het deelvenster **Rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-kontiki-test-user"></a>Een Kontiki-testgebruiker maken

Er is geen actie-item voor u om gebruikersinrichting te configureren in Kontiki. Wanneer een toegewezen gebruiker zich wil aanmelden bij Kontiki met behulp van de portal Mijn apps, wordt in Kontiki gecontroleerd of de gebruiker bestaat. Als er geen gebruikersaccount wordt gevonden, wordt het gebruikersaccount automatisch gemaakt in Kontiki.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Als u eenmalige aanmelding hebt ingesteld en **Kontiki** selecteert in de portal Mijn apps, wordt u automatisch aangemeld bij Kontiki. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie de volgende artikelen:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)