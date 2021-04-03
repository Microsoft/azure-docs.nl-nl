---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Maxient Conduct Manager Software | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Maxient Conduct Manager Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Maxient Conduct Manager Software

In deze zelfstudie leert u hoe u Maxient Conduct Manager Software kunt integreren met Azure Active Directory (Azure AD). Wanneer u Maxient Conduct Manager Software integreert met Azure AD, kunt u het volgende doen:

* Azure AD gebruiken om uw gebruikers te verifiëren voor Maxient Conduct Manager Software
* Gebruikers zich automatisch laten aanmelden met hun Azure AD-account bij Maxient Conduct Manager Software.


Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Maxient Conduct Manager Software-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u Azure AD configureren voor gebruik met Maxient Conduct Manager Software.


* Maxient Conduct Manager Software ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Maxient Conduct Manager Software toevoegen vanuit de galerie

Voor het configureren van de integratie van Maxient Conduct Manager Software met Azure Active Directory moet u Maxient Conduct Manager Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Maxient Conduct Manager Software** in het zoekvak.
1. Selecteer **Maxient Conduct Manager Software** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Eenmalige aanmelding van Azure AD voor Maxient Conduct Manager Software configureren en testen

Test en configureer eenmalige aanmelding van Azure AD bij Maxient Conduct Manager Software. Voor eenmalige aanmelding moet u een verbinding tot stand brengen tussen Azure AD en Maxient Conduct Manager Software.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Maxient Conduct Manager Software te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : hiermee stelt u uw gebruikers in staat te verifiëren voor gebruik met Maxient Conduct Manager Software
    1. **[Alle gebruikers toewijzen om Maxient te gebruiken](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** : hiermee staat u iedereen in uw organisatie toe om te verifiëren.
1. **[Azure AD-instelling testen met Maxient](#test-with-maxient)** : hiermee controleert u of de configuratie werkt en of de juiste kenmerken worden vrijgegeven

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Maxient Conduct Manager Software** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met uw Maxient-vertegenwoordiger voor implementatie en ondersteuning om de waarde te krijgen.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.  U dient deze URL aan uw Maxient-vertegenwoordiger voor implementatie en ondersteuning te verstrekken.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Alle gebruikers toewijzen om kunnen te verifiëren voor Maxient Conduct Manager Software

In deze sectie verleent u alle accounts toegang voor verificatie met behulp van het Azure-systeem voor Maxient Conduct Manager Software.  Deze stap is **vereist** voor een goede werking van Maxient.  Maxient maakt gebruik van uw Azure AD-systeem om gebruikers te *verifiëren*. De *autorisatie* van gebruikers wordt uitgevoerd in het Maxient-systeem voor de specifieke functie die ze willen uitvoeren. Maxient gebruikt geen kenmerken uit uw map om deze beslissingen te nemen.

1. Selecteer in de Azure-portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Maxient Conduct Manager Software** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** alle gebruikers (of de juiste groepen) en **wijs ze toe** om verifiëren met Maxient mogelijk te maken.

## <a name="test-with-maxient"></a>Testen met Maxient 

Als er nog geen ondersteuningsticket is geopend bij een Maxient- vertegenwoordiger voor implementatie en ondersteuning, stuurt u een e-mail naar [support@maxient.com](mailto:support@maxient.com) met het onderwerp "Campus Based Authentication/Azure Setup - \<\<School Name\>\>". Geef in de hoofdtekst van het e-mailbericht de **App-URL voor federatieve metagegevens** op. Een medewerker van Maxient reageert met een testlink om te controleren of de juiste kenmerken worden vrijgegeven.  
    
## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Maxient Conduct Manager Software proberen met Azure AD](https://aad.portal.azure.com/)