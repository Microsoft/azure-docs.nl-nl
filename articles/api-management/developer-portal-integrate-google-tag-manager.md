---
title: Integratie van Google Tag Manager in de ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over het Google Tag Manager in uw beheerde of zelf-hostende ontwikkelaarsportal in Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741724"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integratie van Google Tag Manager in API Management ontwikkelaarsportal

[Google Tag Manager](https://developers.google.com/tag-manager) is een systeem voor tagbeheer dat is gemaakt door Google. U kunt deze gebruiken voor het beheren van JavaScript- en HTML-tags die worden gebruikt voor het bijhouden en analyseren van websites. U kunt bijvoorbeeld een Google Tag Manager Google Analytics, heatmaps of chatbots zoals LiveChat te integreren.

Volg de stappen in dit artikel om een Google Tag Manager in uw beheerde of zelf-hostende ontwikkelaarsportal in Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Een Google Tag Manager toevoegen aan uw portal

Volg deze stappen om een Google Tag Manager in uw beheerde of zelf-hostende ontwikkelaarsportal.

> [!IMPORTANT]
> Stap 1 en 2 zijn niet vereist voor beheerde portals. Als u een beheerde portal hebt, gaat u verder met stap 4.

1. Stel een lokale [omgeving in voor](developer-portal-self-host.md#step-1-set-up-local-environment) de nieuwste versie van de ontwikkelaarsportal.

1. Installeer het **npm-pakket** om [Paperbits toe te voegen voor Google Tag Manager](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. Importeer en registreer `startup.publish.ts` de GTM-module in het bestand in `src` de map:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Breid de siteconfiguratie uit de vorige stap uit met Google Tag Manager configuratie:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Volgende stappen

- [Portalimplementaties automatiseren](automate-portal-deployments.md)
- [De ontwikkelaarsportal zelf hosten](developer-portal-self-host.md)