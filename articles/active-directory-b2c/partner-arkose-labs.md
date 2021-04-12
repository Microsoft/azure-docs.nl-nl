---
title: Zelf studie voor het configureren van Azure Active Directory B2C Met arkose Labs
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van Azure Active Directory B2C Met arkose Labs om Risk ante en frauduleuze gebruikers te identificeren
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c10a39b050fa66192f762ba642b4c8ac2e080250
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258138"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Zelf studie: arkose Labs configureren met Azure Active Directory B2C

In deze voorbeeld zelfstudie vindt u informatie over het integreren van Azure Active Directory (AD) B2C-verificatie met [arkose Labs](https://www.arkoselabs.com/). Met arkose Labs kunnen organisaties tegen bot-aanvallen, inbreuk op account overname en frauduleuze accounts worden geopend.  

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Een [arkose Labs](https://www.arkoselabs.com/book-a-demo/) -account.

## <a name="scenario-description"></a>Scenariobeschrijving

Arkose Labs-integratie omvat de volgende onderdelen:

- **Arkose Labs** : een fraude-en misbruik service om te beschermen tegen bots en andere geautomatiseerd misbruik.

- **Azure AD B2C gebruikers stroom voor registratie** : de registratie-ervaring die gebruikmaakt van de arkose Labs-service. Maakt gebruik van de aangepaste HTML en Java script en API-Connect oren om te integreren met de arkose Labs-service.

- **Azure functions** -API-eind punt gehost door u dat werkt met de API-connectors-functie. Deze API is verantwoordelijk voor het uitvoeren van de validatie aan de server zijde van het arkose Labs-sessie token.

In het volgende diagram wordt beschreven hoe arkose Labs kan worden geïntegreerd met Azure AD B2C.

![Afbeelding toont het architectuur diagram van arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Stap  | Beschrijving |
|---|---|
|1     | Een gebruiker meldt zich aan en maakt een account. Wanneer de gebruiker verzenden selecteert, wordt er een arkose Labs Enforcement-test weer gegeven.         |
|2     |  Nadat de gebruiker de uitdaging heeft voltooid, stuurt Azure AD B2C de status naar arkose Labs om een token te genereren. |
|3     |  Arkose Labs genereert een token en stuurt het terug naar Azure AD B2C.   |
|4     |  Azure AD B2C roept een tussenliggende Web-API aan om het aanmeldings formulier door te geven.      |
|5     |  De tussenliggende Web-API verzendt het registratie formulier naar arkose Lab voor token verificatie.    |
|6     | Arkose Lab-processen en de verificatie resultaten worden teruggestuurd naar de tussenliggende Web-API.|
|7     | De tussenliggende Web-API verzendt het slagen of mislukken van de uitdaging om Azure AD B2C. |
|8     | Als de uitdaging is voltooid, wordt een aanmeldings formulier verzonden naar Azure AD B2C en Azure AD B2C de verificatie voltooid.|

## <a name="onboard-with-arkose-labs"></a>Onboarding Met arkose Labs

1. Neem contact op met [arkose](https://www.arkoselabs.com/book-a-demo/) en maak een account.

2. Zodra het account is gemaakt, gaat u naar https://dashboard.arkoselabs.com/login  

3. Navigeer in het dash board naar site-instellingen om uw open bare sleutel en persoonlijke sleutel te vinden. Deze informatie is later nodig om Azure AD B2C te configureren. De waarden van open bare en persoonlijke sleutels worden aangeduid als `ARKOSE_PUBLIC_KEY` en `ARKOSE_PRIVATE_KEY` in de [voorbeeld code](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Deel 1: een aangepast ArkoseSessionToken-kenmerk maken

Voer de volgende stappen uit om een aangepast kenmerk te maken:  

1. Ga naar **Azure Portal**  >  **Azure AD B2C**

2. **Gebruikers kenmerken** selecteren

3. Selecteer **Toevoegen**

4. Voer **ArkoseSessionToken** in als kenmerk naam

5. Selecteer **Maken**

Meer informatie over [aangepaste kenmerken](./user-flow-custom-attributes.md?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Deel 2: een gebruikers stroom maken

De gebruikers stroom kan **zich aanmelden** en **Aanmelden** **of u kunt zich aanmelden.** De arkose Labs-gebruikers stroom wordt alleen weer gegeven tijdens het aanmelden.

1. Zie de [instructies](./tutorial-create-user-flows.md) voor het maken van een gebruikers stroom. Als u een bestaande gebruikers stroom gebruikt, moet dit het versie type van het **Aanbevolen (volgende generatie preview)** zijn.

2. Ga in de instellingen van de gebruikers stroom naar **gebruikers kenmerken** en selecteer de claim **ArkoseSessionToken** .

![Afbeelding laat zien hoe u aangepaste kenmerken selecteert](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Deel 3: aangepaste HTML, java script en pagina-indelingen configureren

Ga naar het [HTML-script](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)dat u hebt ontvangen. Het bestand bevat een HTML-sjabloon met Java script- `<script>` Tags die drie dingen doen:

1. Laad het arkose Labs-script, waarin de arkose Labs-widget wordt weer gegeven en de arkose Labs-validatie aan de client zijde.

2. Verberg het `extension_ArkoseSessionToken` invoer element en het label, dat overeenkomt met het `ArkoseSessionToken` aangepaste kenmerk, vanuit de gebruikers interface die wordt weer gegeven aan de gebruiker.

3. Wanneer een gebruiker de arkose Labs-uitdaging voltooit, wordt het antwoord van de gebruiker door arkose Labs gecontroleerd en wordt een token gegenereerd. Met de call back `arkoseCallback` in de aangepaste Java script wordt de waarde van ingesteld `extension_ArkoseSessionToken` op de gegenereerde token waarde. Deze waarde wordt verzonden naar het API-eind punt, zoals wordt beschreven in de volgende sectie.

    Raadpleeg [dit artikel](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) voor meer informatie over arkose Labs-validatie aan de client zijde.

Volg de stappen die worden beschreven om de aangepaste HTML en Java script te gebruiken voor uw gebruikers stroom.

1. Wijzig [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) -bestand zodat dit `<ARKOSE_PUBLIC_KEY>` overeenkomt met de waarde die u hebt gegenereerd voor de validatie aan de client zijde en gebruikt om het arkose Labs-script voor uw account te laden.

2. Host de HTML-pagina op een webeindpunt waarvoor cross-Origin Resource Sharing (CORS) is ingeschakeld. [Maak een Azure Blob-opslag account](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en [Configureer CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Als u uw eigen aangepaste HTML hebt, kopieert en plakt u de `<script>` elementen op uw HTML-pagina.

3. Volg deze stappen om de pagina-indelingen te configureren

   a. Ga vanuit het Azure Portal naar **Azure AD B2C**

   b. Navigeer naar **gebruikers stromen** en selecteer uw gebruikers stroom

   c. **Pagina-indelingen** selecteren

   d. **Een lokale account selecteren pagina-indeling voor aanmelden**

   e. **Aangepaste pagina-inhoud** in-/uitschakelen met **Ja**

   f. Plak de URI waar uw aangepaste HTML in **gebruik is aangepaste pagina-inhoud gebruiken**

   g. Als u sociale-id-providers gebruikt, herhaalt u **stap e** en **f** voor de lay-out van de pagina voor het **registreren van sociale accounts** .

   ![afbeelding met pagina-indelingen](media/partner-arkose-labs/page-layouts.png)

4. Ga vanuit uw gebruikers stroom naar **Eigenschappen** en selecteer pagina-indeling voor het afdwingen van **Java script inschakelen** (preview). Raadpleeg dit [artikel](./javascript-and-page-layout.md?pivots=b2c-user-flow) voor meer informatie.

### <a name="part-4---create-and-deploy-your-api"></a>Deel 4: uw API maken en implementeren

Installeer de [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code.

>[!Note]
>In de stappen die in dit gedeelte worden beschreven, wordt ervan uitgegaan dat u Visual Studio code gebruikt om de Azure-functie te implementeren. U kunt ook Azure Portal, Terminal of opdracht prompt of een andere code-editor gebruiken om te implementeren.

#### <a name="run-the-api-locally"></a>De API lokaal uitvoeren

1. Navigeer naar de Azure-extensie in Visual Studio code op de navigatie balk aan de linkerkant. Selecteer de **lokale projectmap** die uw lokale Azure-functie weergeeft.

2. Druk op **F5** of gebruik **het menu fout opsporing**  >  **starten** om het fout opsporingsprogramma te starten en aan de Azure functions-host te koppelen. Met deze opdracht wordt automatisch de single debug-configuratie gebruikt die door Azure-functie is gemaakt.

3. De functie-uitbrei ding van Azure genereert automatisch een aantal bestanden voor lokale ontwikkeling, installeert afhankelijkheden en installeer de functie kern hulpprogramma's als deze nog niet aanwezig is. Deze hulpprogram ma's helpen bij het oplossen van problemen.

4. Uitvoer van het hulp programma voor de functie kern wordt weer gegeven in het deel venster Visual Studio code **Terminal** . Als de host is gestart, **Alt + klikken op** de lokale URL die wordt weer gegeven in de uitvoer om de browser te openen en de functie uit te voeren. Klik in Azure Functions Verkenner met de rechter muisknop op de functie om de URL van de lokaal gehoste functie weer te geven.

Herhaal de stappen 1 tot en met 4 als u het lokale exemplaar tijdens het testen opnieuw wilt implementeren.

#### <a name="add-environment-variables"></a>Omgevings variabelen toevoegen

Dit voor beeld beschermt het web API-eind punt met behulp van [http-basis verificatie](https://tools.ietf.org/html/rfc7617).

Gebruikers naam en wacht woord worden opgeslagen als omgevings variabelen en niet als onderdeel van de opslag plaats. Zie [local.settings.jsin](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) het bestand voor meer informatie.

1. Een local.settings.jsmaken voor het bestand in de hoofdmap

2. Kopieer en plak de onderstaande code op het bestand:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
De waarden voor **BASIC_AUTH_USERNAME** en **BASIC_AUTH_PASSWORD** zijn de referenties die worden gebruikt voor het verifiëren van de API-aanroep voor uw Azure-functie. Kies de gewenste waarden.

De `<ARKOSE_PRIVATE_KEY>` is het geheim aan de server zijde dat u hebt gegenereerd in de arkose Labs-service. Het wordt gebruikt om de [arkose Labs-validatie-API aan de server zijde](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) aan te roepen om de waarde van de `ArkoseSessionToken` gegenereerde door de front-end te valideren.

De `<B2C_EXTENSIONS_APP_ID>` is de toepassings-id van de app die door Azure AD B2C wordt gebruikt voor het opslaan van aangepaste kenmerken in de Directory. U kunt deze toepassings-ID vinden door te navigeren naar App-registraties, te zoeken naar B2C-Extensions-app en de toepassings-ID (client) te kopiëren vanuit het deel venster **overzicht** . Verwijder de `-` tekens.

![Afbeelding toont zoeken op app-id](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>De toepassing implementeren op het web

1. Volg de stappen in [deze](/azure/javascript/tutorial-vscode-serverless-node-04) hand leiding om uw Azure-functie in de cloud te implementeren. Kopieer de web-URL van het eind punt van uw Azure-functie.

2. Zodra de implementatie is geïmplementeerd, selecteert u de optie **Upload instellingen** . De omgevings variabelen worden geüpload naar de [Toepassings instellingen](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) van de app service. Deze toepassings instellingen kunnen ook worden geconfigureerd of [beheerd via de Azure Portal.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Raadpleeg [dit artikel](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) voor meer informatie over Visual Studio code development voor Azure functions.

#### <a name="configure-and-enable-the-api-connector"></a>De API-connector configureren en inschakelen

[Maak een API-connector](./add-api-connector.md) en schakel deze in voor uw gebruikers stroom. De configuratie van de API-connector moet er als volgt uitzien:

![Afbeelding laat zien hoe u API-connector kunt configureren](media/partner-arkose-labs/configure-api-connector.png)

- **Eind punt-URL** : is de functie-URL die u eerder hebt gekopieerd tijdens de implementatie van Azure function.

- **Gebruikers naam en wacht woord** : zijn de gebruikers naam en het wacht woord die u eerder hebt gedefinieerd als omgevings variabelen.

Als u de API-connector wilt inschakelen, selecteert u in de **API-connector** instellingen voor uw gebruikers stroom de API-connector die moet worden aangeroepen **voordat de gebruikers** stap wordt gemaakt. Hiermee wordt de API opgeroepen wanneer een gebruiker **maken** in de registratie stroom selecteert. De API voert een validatie aan de server zijde uit van de `ArkoseSessionToken` waarde die is ingesteld door de call back van de arkose-widget `arkoseCallback` .

![Afbeelding toont API-connector inschakelen](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels **gebruikers stromen**.

2. Selecteer de eerder gemaakte gebruikers stroom.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. Toepassing: Selecteer de geregistreerde app (voor beeld is JWT)

   b. Antwoord-URL: de omleidings-URL selecteren

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. De registratie stroom door lopen en een account maken

5. Afmelden

6. De aanmeldings stroom door lopen  

7. Wanneer u **door gaan** selecteert, wordt er een arkose Labs-puzzel weer gegeven.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Voorbeeld codes](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) voor Azure AD B2Cs stroom voor het registreren van gebruikers

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)