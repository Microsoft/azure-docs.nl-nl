---
title: Zelf studie voor het configureren van Azure Active Directory B2C met WhoIAM
titleSuffix: Azure AD B2C
description: In deze zelf studie leert u hoe u Azure AD B2C-verificatie kunt integreren met WhoIAM voor gebruikers verificatie.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953539"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van WhoIAM met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het configureren van [WhoIAM](https://www.whoiam.ai/brims/) -identiteits beheersysteem (BRIMS) in uw omgeving en het integreren met Active Directory B2C (Azure AD B2C).

BRIMS is een set apps en services die in uw omgeving worden geïmplementeerd. Het biedt spraak-, SMS-en e-mail verificatie van uw gebruikers database. BRIMS werkt in combi natie met uw bestaande oplossing voor identiteits-en toegangs beheer en is platform neutraal.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Een WhoIAM- [proef account](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>Scenariobeschrijving

De WhoIAM-integratie bevat de volgende onderdelen:

- Een Azure AD B2C-Tenant. Het is de autorisatie server die de referenties van de gebruiker verifieert op basis van het aangepaste beleid dat hierin is gedefinieerd. Het wordt ook wel de ID-provider genoemd.

- Een beheer portal voor het beheer van clients en hun configuraties.

- Een API-service die verschillende functies via eind punten beschikbaar maakt.  

- Azure Cosmos DB, dat fungeert als de back-end voor zowel de BRIMS-beheer portal als de API-service.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Diagram van de architectuur van Azure AD B2C integratie met WhoIAM.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt een pagina om de registratie-of aanmeldings aanvraag te starten voor een app die Azure AD B2C als id-provider gebruikt.
| 2. | Als onderdeel van de verificatie vraagt de gebruiker om het eigendom van hun e-mail adres of telefoon nummer te verifiëren of om hun stem als biometrische verificatie factor te gebruiken.  
| 3. | Azure AD B2C wordt een aanroep van de BRIMS API-service uitgevoerd en door gegeven aan het e-mail adres, telefoon nummer en spraak registratie van de gebruiker.
| 4. | BRIMS maakt gebruik van vooraf gedefinieerde configuraties, zoals volledig aanpas bare e-mail-en SMS-sjablonen om te communiceren met de gebruiker in hun respectieve taal op een manier die consistent is met de stijl van de app.
| 5. | Nadat de identiteits controle van een gebruiker is voltooid, retourneert BRIMS een token om Azure AD B2C om het resultaat van de verificatie aan te geven. Azure AD B2C verleent de gebruiker vervolgens toegang tot de app of de verificatie poging mislukt.  

## <a name="sign-up-with-whoiam"></a>Aanmelden met WhoIAM

1. Neem contact op met [WhoIAM](https://www.whoiam.ai/contact-us/) en maak een BRIMS-account.

2. Gebruik de aanmeldings richtlijnen die voor u beschikbaar zijn en configureer de volgende Azure-Services:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): wordt gebruikt voor beveiligde opslag van wacht woorden, zoals e-mail service-wacht woorden.

    - [Azure app service](https://azure.microsoft.com/services/app-service/): wordt gebruikt om de BRIMS-API en de beheer Portal Services te hosten.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): wordt gebruikt voor het verifiëren van gebruikers met beheerders rechten voor de beheer Portal.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): wordt gebruikt om instellingen op te slaan en op te halen.

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (optioneel): wordt gebruikt om u aan te melden bij zowel de API als de beheer Portal.

3. Implementeer de BRIMS-API en de BRIMS-beheer Portal in uw Azure-omgeving.

4. Azure AD B2C aangepaste beleids voorbeelden zijn beschikbaar in uw BRIMS-aanmeld documentatie. Volg de documentatie voor het configureren van uw app en het gebruik van het BRIMS-platform voor verificatie van de gebruikers identiteit.  

Raadpleeg de [product documentatie](https://www.whoiam.ai/brims/)voor meer informatie over de BRIMS van WhoIAM.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C-Tenant. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en vervolgens:

   a. Selecteer voor **toepassing** de geregistreerde app (het voor beeld is JWT).

   b. Selecteer de **omleidings-URL** voor de **antwoord-URL**.

   c. Selecteer **gebruikers stroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account.

5. De BRIMS-service wordt aangeroepen tijdens de stroom nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)