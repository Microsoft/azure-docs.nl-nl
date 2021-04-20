---
title: Implementaties van ontwikkelaarsportal automatiseren
titleSuffix: Azure API Management
description: Meer informatie over het automatisch migreren van zelf-hostend inhoud van de ontwikkelaarsportal tussen twee API Management services.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741739"
---
# <a name="automate-developer-portal-deployments"></a>Implementaties van ontwikkelaarsportal automatiseren

De API Management-ontwikkelaarsportal ondersteunt programmatische toegang tot inhoud. Hiermee kunt u gegevens importeren naar of exporteren vanuit een API Management-service via het [inhoudsbeheersysteem REST API.](/rest/api/apimanagement/) De REST API werkt voor zowel beheerde als zelf-hostende portals.

## <a name="automated-migration-script"></a>Geautomatiseerd migratiescript

U kunt de API gebruiken om de migratie van inhoud tussen twee API Management-services te automatiseren, bijvoorbeeld een service in de testomgeving en een service in de productieomgeving. Het `scripts.v3/migrate.js` script in de [github-opslagplaats](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) API Management ontwikkelaarsportal vereenvoudigt dit automatiseringsproces.

> [!WARNING]
> Met het script wordt de inhoud van de ontwikkelaarsportal in uw doel-API Management verwijderd. Als u zich zorgen maakt, moet u een back-up maken.

> [!NOTE]
> Als u een zelf-hostende portal gebruikt met een expliciet gedefinieerd aangepast opslagaccount voor het hosten van mediabestanden (dat wil zeggen, u definieert de instelling in het configuratiebestand), moet u het oorspronkelijke `blobStorageUrl` `config.design.json` script `scripts/migrate.js` [gebruiken.](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js) Het oorspronkelijke script werkt niet voor beheerde of zelf-hostende portals met het mediaopslagaccount dat wordt beheerd door API Management. In dat geval gebruikt u in plaats daarvan het script uit `/scripts.v3` de map .

Met het script voert u de volgende stappen uit:

1. Leg de inhoud en media van de API Management vast.
1. Verwijder de inhoud en media van de doelservice API Management portal.
1. Upload de inhoud en media van de portal naar de API Management service.
1. Optioneel en alleen voor beheerde portals: publiceer de portal automatisch.

Nadat het script is uitgevoerd, moet de doel-API Management-service dezelfde portalinhoud bevatten als de bronservice en kunt u deze als beheerder zien.

* Als u een beheerde portal gebruikt, kunt u het script zo instellen dat de doelportal automatisch wordt gepubliceerd om de gemigreerde versie automatisch beschikbaar te maken voor de bezoekers. 
* Als u een zelf-hostende portal gebruikt, moet u de doelportal handmatig publiceren. Volg de instructies voor publiceren en hosten in de zelfstudie om [een zelf-hostend ontwikkelaarsportal in te stellen.](developer-portal-self-host.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
- [De ontwikkelaarsportal zelf hosten](developer-portal-self-host.md)