---
title: Communitywidgets gebruiken in de ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over communitywidgets voor API Management ontwikkelaarsportal en hoe u deze in uw code injecteert en gebruikt.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741604"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Communitywidgets gebruiken in de ontwikkelaarsportal

Alle ontwikkelaars plaatsen hun door de community bijgedragen widgets in de map van de `/community/widgets/` GitHub-opslagplaats API Management [ontwikkelaarsportal.](https://github.com/Azure/api-management-developer-portal) Elke is geaccepteerd door het ontwikkelaarsportalteam. U kunt de widgets gebruiken door ze in uw [zelf-hostende versie](developer-portal-self-host.md) van de portal te injecteren. De beheerde versie van de ontwikkelaarsportal biedt momenteel geen ondersteuning voor communitywidgets.

> [!NOTE]
> Het ontwikkelaarsportalteam inspecteert de bijgedragen widgets en hun afhankelijkheden grondig. Het team kan echter niet garanderen dat het veilig is om de widgets te laden. Gebruik uw eigen oordeel bij het kiezen van een widget die is bijgedragen door de community. Raadpleeg onze richtlijnen [voor widgetbijdragen](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) voor meer informatie over onze preventieve maatregelen.

## <a name="inject-and-use-external-widgets"></a>Externe widgets injecteren en gebruiken

1. Stel een lokale [omgeving in voor](developer-portal-self-host.md#step-1-set-up-local-environment) de nieuwste versie van de ontwikkelaarsportal.

1. Ga naar de map van de widget in de `/community/widgets` map . Lees de beschrijving van de widget in het `readme.md` bestand.

1. Registreer de widget in de modules van de portal:

    1. `src/apim.design.module.ts` - een module die ontwerptijdafhankelijkheden registreert.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` : een module die publiceertijdafhankelijkheden registreert.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` : een module die run time-afhankelijkheden registreert.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Controleer of de widget een bestand `npm_dependencies` bevat.

1. Als dat het zo is, kopieert u de opdrachten uit het bestand en voert u deze uit in de bovenste map van de opslagplaats.

    Als u dit doet, worden de afhankelijkheden van de widget geïnstalleerd.

1. Voer `npm start` uit.

U kunt de widget in de **categorie Community** zien in de widget-selector.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Schermopname van widget-selector":::


## <a name="next-steps"></a>Volgende stappen


Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)

- [Widgets voor bijdragen](developer-portal-widget-contribution-guidelines.md)
