---
title: API-ondersteuning in Azure Static Web Apps met Azure Functions
description: Meer informatie over welke API Azure Static Web Apps functies worden ondersteund
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737474"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-ondersteuning in Azure Static Web Apps Preview met Azure Functions

Azure Static Web Apps biedt serverloze API-eindpunten via [Azure Functions](../azure-functions/functions-overview.md). Door gebruik te maken Azure Functions, kunnen API's dynamisch worden geschaald op basis van de vraag en de volgende functies bevatten:

- **Geïntegreerde beveiliging** met directe toegang tot gebruikersverificatie [en op rollen gebaseerde autorisatiegegevens.](user-information.md)
- **Naadloze routering** die de _API-route_ veilig beschikbaar maakt voor de web-app zonder aangepaste CORS-regels.
- **HTTP-triggers** en invoer-/uitvoerbindingen.

## <a name="configuration"></a>Configuratie

API-eindpunten zijn beschikbaar voor de web-app via de _API-route._ Hoewel deze route vast staat, hebt u controle over de map en het project waar u de bijbehorende app Azure Functions vinden. U kunt deze locatie wijzigen door het [YAML-bestand van](github-actions-workflow.md#build-and-deploy) de werkstroom te bewerken dat zich in de _map .github/workflows_ van uw opslagplaats bevindt.

## <a name="constraints"></a>Beperkingen

Azure Static Web Apps biedt een API via Azure Functions. De mogelijkheden van Azure Functions zijn gericht op een specifieke set functies waarmee u een API voor een web-app kunt maken en waarmee de web-app veilig verbinding kan maken met de API. Deze functies hebben enkele beperkingen, waaronder:

- Het VOORvoegsel van de API-route moet _API zijn._
- De API moet een app Node.js 12, .NET Core 3.1 of Python 3.8 Azure Functions zijn.
- Routeregels voor API-functies ondersteunen alleen [omleidingen en](routes.md#redirects) het [beveiligen van routes met rollen](routes.md#securing-routes-with-roles).
- Triggers zijn beperkt tot [HTTP.](../azure-functions/functions-bindings-http-webhook.md)
  - Invoer- en [uitvoerbindingen](../azure-functions/functions-triggers-bindings.md#supported-bindings) worden ondersteund.
- Logboeken zijn alleen beschikbaar als u [gegevens](../azure-functions/functions-monitoring.md) Application Insights aan uw Functions-app.
- Sommige toepassingsinstellingen worden beheerd door de service. U kunt geen app-instellingen configureren die beginnen met de volgende voorvoegsels: `APPSETTING_` , , , , , , , , , `AZUREBLOBSTORAGE_` , , `AZUREFILESSTORAGE_` `AZURE_FUNCTION_` `CONTAINER_` `DIAGNOSTICS_` `DOCKER_` `FUNCTIONS_` , `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb` .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een API toevoegen](add-api.md)
