---
title: OpenAPI-meta gegevens in Azure Functions
description: Overzicht van OpenAPI-ondersteuning in Azure Functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000199"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2,0 meta gegevens ondersteunen in Azure Functions (preview-versie)
OpenAPI 2,0 (voorheen Swagger) meta gegevens ondersteuning in Azure Functions is een preview-functie die u kunt gebruiken om een OpenAPI 2,0-definitie in een functie-app te schrijven. U kunt dat bestand vervolgens hosten met behulp van de functie-app.

> [!IMPORTANT]
> De OpenAPI-preview-functie is alleen vandaag beschikbaar in de runtime 1.x. Meer informatie over het maken van een 1.x-functie-app [vindt u hier](./functions-versions.md#creating-1x-apps).

Met [OpenAPI-meta gegevens](https://swagger.io/) kan een functie die als host fungeert voor een rest API worden gebruikt door een groot aantal andere software. Deze software omvat micro soft-aanbiedingen zoals PowerApps en de [API apps functie van Azure app service](../app-service/overview.md), hulpprogram ma's voor ontwikkel aars van derden, zoals [postman](https://www.getpostman.com/docs/importing_swagger)en [nog veel meer pakketten](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>We raden u aan om te beginnen met de [zelf studie](./functions-openapi-definition.md) aan de slag en vervolgens terug te gaan naar dit document voor meer informatie over specifieke functies.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Ondersteuning voor OpenAPI-definitie inschakelen
U kunt alle OpenAPI-instellingen configureren op de pagina **API-definitie** in de **platform functies** van uw functie-app.

> [!NOTE]
> De functie-API-definitie functie wordt momenteel niet ondersteund voor Beta runtime.

Als u de generatie van een gehoste OpenAPI-definitie en een Snelstartgids wilt inschakelen, stelt u de **API-definitie bron** in op **functie (preview)**. Met **externe URL** kan uw functie gebruikmaken van een OpenAPI-definitie die elders wordt gehost.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Een Swagger-skelet genereren op basis van de meta gegevens van uw functie
Met een sjabloon kunt u beginnen met het schrijven van uw eerste OpenAPI-definitie. Met de functie definitie sjabloon maakt u een sparse OpenAPI definitie door gebruik te maken van alle meta gegevens in het function.jsbestand voor elk van uw HTTP-trigger functies. U moet meer informatie over uw API invullen vanuit de [OpenAPI-specificatie](https://swagger.io/specification/), zoals aanvraag-en antwoord sjablonen.

Zie de [zelf studie aan de slag](./functions-openapi-definition.md)voor stapsgewijze instructies.

### <a name="available-templates"></a><a name="templates"></a>Beschik bare sjablonen

|Naam| Beschrijving |
|:-----|:-----|
|Gegenereerde definitie|Een OpenAPI-definitie met de maximale hoeveelheid gegevens die kan worden afgeleid van de bestaande meta gegevens van de functie.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Opgenomen meta gegevens in de gegenereerde definitie

De volgende tabel bevat de Azure Portal instellingen en bijbehorende gegevens in function.js, omdat deze is toegewezen aan het gegenereerde Swagger-skelet.

|Swagger.jsop|Portal-gebruikers interface|Function.jsop|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Functie-app-instellingen**  >  **App service instellingen**  >  **Overzicht**  >  **URL**|*Niet aanwezig*
|[Paden](https://swagger.io/specification/#paths-object-29)|**Integreren**  >  **Geselecteerde HTTP-methoden**|Bindingen: route
|[Pad naar item](https://swagger.io/specification/#path-item-object-32)|**Integreren**  >  **Route sjabloon**|Bindingen: methoden
|[Beveiliging](https://swagger.io/specification/#security-scheme-object-112)|**Sleutels**|*Niet aanwezig*|
|OperationID|**Route + toegestane werk woorden**|Route + toegestane werk woorden|

\*De bewerkings-ID is alleen vereist voor de integratie met PowerApps en flow.
> [!NOTE]
> De uitbrei ding x-MS-Summary biedt een weergave naam in Logic Apps, PowerApps en flow.
>
> Zie [uw Swagger-definitie aanpassen voor PowerApps](/connectors/custom-connectors/openapi-extensions)voor meer informatie.

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>CI/CD gebruiken om een API-definitie in te stellen

 U moet de API-definitie hosting inschakelen in de portal voordat u broncode beheer inschakelt om uw API-definitie van broncode beheer te wijzigen. Volg deze instructies:

1. Blader naar de **API-definitie (preview)** in de instellingen van de functie-app.
   1. Stel de bron van de **API-definitie** in op **functie**.
   1. Klik op **API-definitie sjabloon genereren** en vervolgens op **Opslaan** om een sjabloon definitie te maken die u later kunt wijzigen.
   1. Noteer de URL en sleutel van uw API-definitie.
1. [Continue integratie/continue implementatie (CI/cd) instellen](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Wijzig swagger.jsin broncode beheer op \site\wwwroot \.azurefunctions\swagger\swagger.jsop.

Wijzigingen in swagger.jsin uw opslag plaats worden nu gehost door uw functie-app op de URL van de API-definitie en de sleutel die u hebt genoteerd in stap 1. c.

## <a name="next-steps"></a>Volgende stappen
* [Zelf studie aan de slag](./functions-openapi-definition.md). Probeer onze stapsgewijze instructies om een OpenAPI definitie in actie te zien.
* [Azure functions github-opslag plaats](https://github.com/Azure/Azure-Functions/). Raadpleeg de opslag plaats functies om ons feedback te geven over de preview-versie van API-definitie. Maak een GitHub-probleem voor alles wat u wilt laten zien.
* [Azure functions referentie voor ontwikkel aars](functions-reference.md). Meer informatie over het coderen van functies en het definiëren van triggers en bindingen.
