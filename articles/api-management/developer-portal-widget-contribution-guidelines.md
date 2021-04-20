---
title: Widgets bijdragen voor ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over aanbevolen richtlijnen die u kunt volgen wanneer u een widget bijdraagt aan de opslagplaats API Management ontwikkelaarsportal.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741706"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Widgets bijdragen aan de API Management ontwikkelaarsportal

Als u een widget wilt bijdragen aan de [GitHub-opslagplaats](https://github.com/Azure/api-management-developer-portal)API Management ontwikkelaarsportal, volgt u dit proces in drie stappen:

1. Fork de opslagplaats.

1. Implementeert u de widget.

1. Open een pull-aanvraag om uw widget op te nemen in de officiële opslagplaats.

Uw widget neemt de licentie van de opslagplaats over. Deze is beschikbaar voor [opt-in-installatie](developer-portal-use-community-widgets.md) in de zelf-hostende versie van de portal. Het ontwikkelaarsportalteam kan besluiten om deze ook op te nemen in de beheerde versie van de portal.

Raadpleeg de [zelfstudie over de implementatie van](developer-portal-implement-widgets.md) widgets voor een voorbeeld van het ontwikkelen van uw eigen widget.

## <a name="contribution-guidelines"></a>Richtlijnen voor bijdragen

Deze richtlijnen zijn bedoeld om de veiligheid en privacy van onze klanten en de bezoekers van hun portals te waarborgen. Volg deze richtlijnen om ervoor te zorgen dat uw bijdrage wordt geaccepteerd:

1. Plaats uw widget in de `community/widgets/<your-widget-name>` map .

1. De naam van uw widget moet kleine letters en alfanumerieke tekens zijn, met streepjes die de woorden scheiden. Bijvoorbeeld `my-new-widget`.

1. De map moet een schermopname van uw widget bevatten in een gepubliceerde portal.

1. De map moet een bestand `readme.md` bevatten dat de sjabloon uit het bestand `/scaffolds/widget/readme.md` volgt.

1. De map kan een bestand bevatten met npm-opdrachten voor het installeren of beheren van de `npm_dependencies` afhankelijkheden van de widget.

    Geef expliciet de versie van elke afhankelijkheid op. Bijvoorbeeld:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Uw widget moet minimale afhankelijkheden vereisen. Elke afhankelijkheid wordt zorgvuldig geïnspecteerd door de revisoren. Met name de kernlogica van uw widget moet opensource zijn in de map van uw widget. Verpak deze niet in een npm-pakket.

1. Wijzigingen in bestanden buiten de map van uw widget zijn niet toegestaan als onderdeel van een widgetbijdrage. Dit omvat, maar is niet beperkt tot, het `/package.json` bestand.

1. Het injecteren van traceringsscripts of het verzenden van door de klant geschreven gegevens naar aangepaste services is niet toegestaan.

    > [!NOTE]
    > U kunt alleen door de klant geschreven gegevens verzamelen via de `Logger` interface.

## <a name="next-steps"></a>Volgende stappen

- Zie de GitHub-opslagplaats API Management ontwikkelaarsportal voor [meer informatie over bijdragen.](https://github.com/Azure/api-management-developer-portal/)

- Zie [Widgets implementeren](developer-portal-implement-widgets.md) voor stapsgewijs informatie over het ontwikkelen van uw eigen widget.

- Zie [Communitywidgets gebruiken](developer-portal-use-community-widgets.md) voor meer informatie over het gebruik van widgets die zijn bijgedragen door de community.