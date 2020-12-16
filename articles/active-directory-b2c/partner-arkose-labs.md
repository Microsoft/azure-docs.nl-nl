---
title: Arkose Labs met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C-verificatie Met arkose Labs om bescherming te bieden tegen bot-aanvallen, aanvallen op account-inkomend en frauduleuze account openingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c7eea87101a36edb0d77026489ea351b601158b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584592"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van arkose Labs met Azure Active Directory B2C

In deze zelf studie leert u hoe u Azure AD B2C-verificatie kunt integreren met arkose Labs. Met arkose Labs kunnen organisaties tegen bot-aanvallen, inbreuk op account overname en frauduleuze accounts worden geopend.  

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In het volgende diagram wordt beschreven hoe arkose Labs kan worden geïntegreerd met Azure AD B2C.

![Arkose Labs-architectuur diagram](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Stap  | Beschrijving |
|---|---|
|1     | Een gebruiker meldt zich aan met een eerder gemaakt account. Wanneer de gebruiker verzenden selecteert, wordt er een arkose Labs Enforcement-test weer gegeven. Nadat de gebruiker de uitdaging heeft voltooid, wordt de status verzonden naar arkose Labs om een token te genereren.        |
|2     |  Arkose Labs stuurt het token terug naar Azure AD B2C.       |
|3     |  Voordat het aanmeldings formulier wordt verzonden, wordt het token naar arkose Labs verzonden voor verificatie.       |
|4     |  Arkose stuurt een geslaagd of mislukt resultaat van de uitdaging terug.       |
|5     |  Als de uitdaging is voltooid, wordt een aanmeldings formulier verzonden naar Azure AD B2C en Azure AD B2C de verificatie voltooid.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Onboarding Met arkose Labs

1. Neem contact op met [arkose Labs](https://www.arkoselabs.com/book-a-demo/) en maak een account.

2. Wanneer uw account is gemaakt, gaat u naar https://dashboard.arkoselabs.com/login .

3. Navigeer in het dash board naar site-instellingen om uw open bare sleutel en persoonlijke sleutel te vinden. Deze informatie is later nodig om Azure AD B2C te configureren.

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Deel 1: Blob-opslag maken om de aangepaste HTML op te slaan

Als u een opslag account wilt maken, voert u de volgende stappen uit:  

1. Meld u aan bij de Azure Portal.

2. Zorg ervoor dat u de map gebruikt die uw Azure-abonnement bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw abonnement bevat. Dit is een andere map dan de map met uw Azure B2C-tenant.

3. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer  **opslag accounts**.

4. Selecteer **Toevoegen**.

5. Selecteer onder  **resource groep** de optie  **nieuwe maken**, voer een naam in voor de nieuwe resource groep en selecteer **OK**.

6. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn in Azure, moet tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.

7. Selecteer de locatie van het opslagaccount of accepteer de standaardlocatie.

8. Accepteer alle andere standaard waarden, selecteer   **beoordeling &** Create  >  **Create**.

9. Nadat het opslagaccount is gemaakt, selecteert u  **Naar de resource gaan**.

#### <a name="create-a-container"></a>Een container maken

1. Selecteer op de pagina overzicht van het opslag account  **blobs**.

2. Selecteer   **container**, voer een naam in voor de container, kies   **BLOB** (anonieme lees toegang alleen voor blobs) en selecteer vervolgens **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Cross-Origin-resource delen (CORS) inschakelen

In browsers maakt Azure AD B2C-code gebruik van een moderne en gestandaardiseerde benadering om aangepaste inhoud te laden vanaf een URL die u in een gebruikersstroom opgeeft. Met CORS kunnen beperkte bronnen op een webpagina worden aangevraagd vanuit andere domeinen.

1. Selecteer  **CORS** in het menu.

2. Voer in voor  **toegestane oorsprongen**  `https://your-tenant-name.b2clogin.com` . Vervang uw-Tenant naam door de naam van uw Azure AD B2C-Tenant. Bijvoorbeeld  `https://fabrikam.b2clogin.com`. Gebruik alleen kleine letters bij het invoeren van de naam van uw Tenant.

3. Voor  **toegestane methoden** selecteert u  **ophalen**, **plaatsen** en  **Opties**.

4. Voer bij **Toegestane headers** een asterisk (*) in.

5. Voer een asterisk (*) in voor **weer gegeven headers**.

6. Voer bij **Maximumleeftijd** 200 in.

   ![Aanmelden bij arkose Labs en aanmelden](media/partner-arkose-labs/signup-signin-arkose.png)

7. Selecteer **Opslaan**.

### <a name="part-2--set-up-a-back-end-server"></a>Deel 2: een back-endserver instellen

Down load Git Bash en voer de volgende stappen uit:

1. Volg de instructies voor het [maken van een web-app](../app-service/quickstart-php.md), totdat het bericht ' Gefeliciteerd!U hebt uw eerste PHP-app geïmplementeerd op App Service.

2. Open uw lokale map en wijzig de naam van het bestand **index. php** in **verify-token. php**.

3. Open het bestand verify-token. php waarvan u de naam hebt gewijzigd en:

   a. Vervang de inhoud door de inhoud van het bestand verify-token. php dat is gevonden in de [github-opslag plaats](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Vervang <private_key> op regel 3 door uw persoonlijke sleutel die is verkregen van het arkose Labs-dash board.

4. Voer in het lokale terminal venster uw wijzigingen door in Git en push de code wijzigingen naar Azure door het volgende te typen in Git Bash:

   ``git commit -am "updated output"``

   ``git push azure main``  

### <a name="part-3---final-setup"></a>Deel 3 – laatste installatie

#### <a name="store-the-custom-html"></a>Sla de aangepaste HTML op

1. Open het bestand index.html dat is opgeslagen in de [github-opslag plaats](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Vervang alle instanties van `<tenantname>` door de naam van uw b2C-Tenant (met andere woorden `<tenantname>.b2clogin.com` ). Er moeten vier instanties zijn.

3. Vervang de `<appname>` door de naam van de app die u hebt gemaakt in deel 2, stap 1.

   ![Scherm afbeelding Met arkose Labs Azure CLI](media/partner-arkose-labs/arkose-azure-cli.png)

4. Vervang de `<public_key>` op regel 64 door de open bare sleutel die u hebt verkregen via het arkose Labs-dash board.

5. Upload het index.html-bestand naar de Blob-opslag die hierboven is gemaakt.

6. Ga naar **opslag**  >  **container**  >  **uploaden**.

#### <a name="set-up-azure-ad-b2c"></a>Azure AD B2C instellen

> [!NOTE]
> Als u nog geen account hebt, [maakt u een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

1. Maak een gebruikers stroom op basis van de [informatie.](tutorial-create-user-flows.md) Stop bij het bereiken van de sectie **de gebruikers stroom testen**.

2. Schakel Java script in uw [gebruikers stroom](javascript-and-page-layout.md)in.

3. Schakel op de pagina dezelfde gebruikers stroom de URL voor de aangepaste pagina in: Ga naar de pagina-indeling **gebruikers stroom**  >    >  **Gebruik aangepaste pagina-inhoud**  =  **Ja**  >  **Invoegen aangepaste pagina-URL**.
Deze URL voor de aangepaste pagina wordt opgehaald uit de locatie van het index.html-bestand in de Blob-opslag  

   ![Scherm opname van de opslag-URL van arkose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C-Tenant en selecteer onder **beleids regels** **gebruikers stromen**.

2. Selecteer de eerder gemaakte gebruikers stroom.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing** -Selecteer de geregistreerde app (voor beeld is JWT).

   b. **Antwoord-URL** : Selecteer de omleidings-URL.

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account.

5. Meld u af.

6. Ga door naar de aanmeldings stroom.

7. Wanneer u **door gaan** selecteert, wordt er een arkose Labs-puzzel weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](custom-policy-get-started.md?tabs=applications)