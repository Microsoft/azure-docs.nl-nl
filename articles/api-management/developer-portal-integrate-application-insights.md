---
title: Integratie van Application Insights in de ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over het integreren Application Insights in uw beheerde of zelf-hostende ontwikkelaarsportal.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741721"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integratie van Application Insights in de ontwikkelaarsportal

Een populaire functie van Azure Monitor is Application Insights. Het is een extensible APM-service (Application Performance Management) voor ontwikkelaars en DevOps-professionals. Gebruik deze om uw ontwikkelaarsportal te bewaken en prestatieafwijkingen te detecteren. Application Insights bevat krachtige analysehulpprogramma's waarmee u kunt leren wat gebruikers daadwerkelijk doen tijdens het bezoeken van uw ontwikkelaarsportal.

## <a name="add-application-insights-to-your-portal"></a>Een Application Insights toevoegen aan uw portal

Volg deze stappen om een Application Insights in uw beheerde of zelf-hostende ontwikkelaarsportal.

> [!IMPORTANT]
> Stap 1 en 2 zijn niet vereist voor beheerde portals. Als u een beheerde portal hebt, gaat u verder met stap 4.

1. Stel een lokale [omgeving in voor](developer-portal-self-host.md#step-1-set-up-local-environment) de nieuwste versie van de ontwikkelaarsportal.

1. Installeer het **npm-pakket** om [Paperbits voor Azure toe te voegen:](https://github.com/paperbits/paperbits-azure)

    ```console
    npm install @paperbits/azure --save
    ```

1. Importeer en registreer in het bestand in de `startup.publish.ts` `src` map de Application Insights module:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. De configuratie van de portal ophalen:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Breid de siteconfiguratie uit de vorige stap uit met Application Insights configuratie:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
- [Portalimplementaties automatiseren](automate-portal-deployments.md)
- [De ontwikkelaarsportal zelf hosten](developer-portal-self-host.md)
