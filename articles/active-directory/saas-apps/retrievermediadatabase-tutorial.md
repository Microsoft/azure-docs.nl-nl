---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met RetrieverMediaDatabase | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en RetrieverMediaDatabase.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 32ca39685772272ae93ccbe0c2afcbd1f66894b1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354757"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retrievermediadatabase"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met RetrieverMediaDatabase

In deze zelfstudie leert u hoe u RetrieverMediaDatabase kunt integreren met Azure AD (Active Directory). Wanneer u RetrieverMediaDatabase integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot RetrieverMediaDatabase.
* Zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij RetrieverMediaDatabase.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement met eenmalige aanmelding ingeschakeld voor RetrieverMediaDatabase.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RetrieverMediaDatabase ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-retrievermediadatabase-from-the-gallery"></a>RetrieverMediaDatabase toevoegen vanuit de galerie

Om de integratie van RetrieverMediaDatabase in Azure AD te configureren, moet u RetrieverMediaDatabase vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** de tekst **RetrieverMediaDatabase** in het zoekvak.
1. Selecteer **RetrieverMediaDatabase** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-retrievermediadatabase"></a>Eenmalige aanmelding van Azure AD voor RetrieverMediaDatabase configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met RetrieverMediaDatabase met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RetrieverMediaDatabase.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met RetrieverMediaDatabase te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij RetrieverMediaDatabase configureren](#configure-retrievermediadatabase-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor RetrieverMediaDatabase maken](#create-retrievermediadatabase-test-user)** : als u een tegenhanger van B.Simon in RetrieverMediaDatabase wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina voor de **RetrieverMediaDatabase**-toepassing naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.


1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)
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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot RetrieverMediaDatabase.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **RetrieverMediaDatabase** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-retrievermediadatabase-sso"></a>Eenmalige aanmelding voor RetrieverMediaDatabase configureren

Als u eenmalige aanmelding aan de zijde van **RetrieverMediaDatabase** wilt configureren, moet u de **App-URL voor federatieve metagegevens** verzenden naar het [ondersteuningsteam van RetrieverMediaDatabase](mailto:support@retriever.nl). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-retrievermediadatabase-test-user"></a>Testgebruiker voor RetrieverMediaDatabase maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in RetrieverMediaDatabase. Werk samen met het [ondersteuningsteam van RetrieverMediaDatabase](mailto:support@retriever.nl) om de gebruikers toe te voegen aan het RetrieverMediaDatabase-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

1. Klik op Deze toepassing testen in de Azure-portal. U wordt automatisch aangemeld bij de instantie van RetrieverMediaDatabase waarvoor u eenmalige aanmelding hebt ingesteld

1. U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel RetrieverMediaDatabase klikt, zou u automatisch moeten worden aangemeld bij de RetrieverMediaDatabase waarvoor u de eenmalige aanmelding hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u RetrieverMediaDatabase hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


