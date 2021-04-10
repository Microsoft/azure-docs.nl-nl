---
title: Service beheer in de portal
titleSuffix: Azure Cognitive Search
description: Een Azure Cognitive Search-service beheren, een gehoste service voor zoeken in de Cloud op Microsoft Azure met behulp van de Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579798"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Service beheer voor Azure Cognitive Search in het Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure-CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search is een volledig beheerde, op de cloud gebaseerde zoek service die wordt gebruikt voor het bouwen van een uitgebreide zoek ervaring in aangepaste apps. In dit artikel worden de beheer taken behandeld die u kunt uitvoeren in de [Azure Portal](https://portal.azure.com) voor een zoek service die u al hebt gemaakt. Met de portal kunt u alle [Beheer taken](#management-tasks) uitvoeren die betrekking hebben op de service, evenals het beheer van inhoud en verkennen. De portal biedt zo een breed spectrum toegang tot alle aspecten van de zoek service bewerkingen.

Elke zoek service wordt beheerd als een zelfstandige resource. De volgende afbeelding toont de portal pagina's voor één gratis zoek service met de naam Demo-Search-SVC. Hoewel u mogelijk gewend bent om Azure PowerShell of Azure CLI te gebruiken voor Service Management, is het zinvol om vertrouwd te raken met de hulpprogram ma's en mogelijkheden die de portal pagina's bieden. Sommige taken zijn nog eenvoudiger en sneller om in de portal uit te voeren dan via code. 

## <a name="overview-home-page"></a>Pagina overzicht (Home)

De pagina overzicht is de pagina Home van elke service. Hieronder worden de gebieden op het scherm die zijn Inge sloten in rode vakken aangegeven, worden taken, hulpprogram ma's en tegels die u mogelijk vaak gebruikt, met name als u geen ervaring hebt met de service.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Portal pagina's voor een zoek service" border="true":::

| Gebied | Beschrijving |
|------|-------------|
| 1  | De sectie **Essentials** bevat service-eigenschappen met inbegrip van het eind punt dat wordt gebruikt bij het instellen van verbindingen. U ziet ook de lagen laag, replica en aantal partities in een oogopslag. |
| 2 | Boven aan de pagina ziet u een reeks opdrachten voor het aanroepen van interactieve hulpprogram ma's of het beheren van de service. Zowel [import gegevens](search-get-started-portal.md) als [Zoek Verkenner](search-explorer.md) worden vaak gebruikt voor het maken van prototypen en verkennen. |
| 3 | Hieronder vindt  u een reeks subpagina's met tabbladen voor snelle toegang tot gebruiks statistieken, metrische gegevens over de service status en toegang tot alle bestaande indexen, Indexeer functies, gegevens bronnen en vaardig heden in uw service. Als u een index of een ander object selecteert, worden er extra pagina's beschikbaar om de object compositie, instellingen en status (indien van toepassing) weer te geven. |
| 4 | Aan de linkerkant hebt u toegang tot koppelingen waarmee extra pagina's worden geopend voor toegang tot de API-sleutels die worden gebruikt voor verbindingen, het configureren van de service, het configureren van beveiliging, het bewaken van bewerkingen, het automatiseren van taken en het verkrijgen van ondersteuning. |

### <a name="read-only-service-properties"></a>Alleen-lezen service-eigenschappen

Verschillende aspecten van een zoek service worden bepaald wanneer de service wordt ingericht en kan niet worden gewijzigd:

* Service naam (u kunt de naam van een zoek service niet wijzigen)
* Service locatie (u kunt een niet-intacte zoek service eenvoudig verplaatsen naar een andere regio. Hoewel er een sjabloon is, is het verplaatsen van de inhoud een hand matig proces)
* Servicelaag (u kunt niet overschakelen van S1 naar S2, bijvoorbeeld zonder een nieuwe service te maken)

## <a name="management-tasks"></a>Beheertaken

Service beheer is lichter ontworpen en wordt vaak gedefinieerd door de taken die u ten opzichte van de service zelf kunt uitvoeren:

* [Pas de capaciteit](search-capacity-planning.md) aan door replica's en partities toe te voegen of te verwijderen
* [API-sleutels beheren](search-security-api-keys.md) die worden gebruikt voor beheer-en query bewerkingen
* [Toegang tot beheer bewerkingen beheren](search-security-rbac.md) via op rollen gebaseerde beveiliging
* [IP-firewall regels configureren](service-configure-firewall.md) om de toegang te beperken op basis van IP-adres
* [Een persoonlijk eind punt configureren](service-create-private-endpoint.md) met behulp van een persoonlijke Azure-koppeling en een particulier virtueel netwerk
* [Service status bewaken](search-monitor-usage.md): opslag, query volumes en latentie

U kunt ook alle objecten die op de service zijn gemaakt, opsommen: indexen, Indexeer functies, gegevens bronnen, vaardig heden, enzovoort. Op de overzichts pagina van de portal ziet u alle inhoud die voor uw service bestaat. Het meren deel van de bewerkingen in een zoek service is gerelateerd aan inhoud.

Dezelfde beheer taken die in de portal worden uitgevoerd, kunnen ook programmatisch worden verwerkt via de [beheer rest api's](/rest/api/searchmanagement/), [AZ. Search Power shell-module](search-manage-powershell.md), [AZ Search Azure cli module](search-manage-azure-cli.md)en de Azure Sdk's voor .net, Python, Java en Java script. Beheer taken worden volledig weer gegeven in de portal en alle programmatische interfaces. Er is geen specifieke beheer taak die alleen beschikbaar is in één modale versie.

Cognitive Search maakt gebruik van andere Azure-Services voor uitgebreidere bewaking en beheer. De enige gegevens die in de zoek service zijn opgeslagen, zijn object inhoud (indexen, indexerings-en gegevens bron definities en andere objecten). De metrische gegevens die worden gerapporteerd aan portal pagina's, worden uit interne logboeken getrokken tijdens een cyclus van 30 dagen. Voor door de gebruiker bewaakte logboek registratie en aanvullende gebeurtenissen hebt u [Azure monitor](../azure-monitor/index.yml)nodig. Zie [logboek gegevens verzamelen en analyseren](search-monitor-logs.md)voor meer informatie over het instellen van diagnostische logboek registratie voor een zoek service.

## <a name="administrator-permissions"></a>Beheerders machtigingen

Wanneer u de pagina overzicht van de zoek service opent, bepalen de machtigingen die aan uw account zijn toegewezen welke pagina's voor u beschikbaar zijn. Op de pagina overzicht aan het begin van het artikel ziet u de portal pagina's die door een beheerder of Inzender worden weer gegeven.

In azure resource worden beheer rechten verleend via roltoewijzingen. In de context van Azure Cognitive Search bepalen [roltoewijzingen](search-security-rbac.md) of de functies van de portal, [Power shell](search-manage-powershell.md), [Azure cli](search-manage-azure-cli.md)of de [beheer rest api's](/rest/api/searchmanagement/search-howto-management-rest-api)kunnen worden toegewezen aan de hand van de toewijzing van REPLICA'S en partities of het beheren van de API-sleutels.

> [!TIP]
> Het inrichten of buiten gebruik stellen van de service zelf kan worden uitgevoerd door een beheerder van een Azure-abonnement of een mede beheerder. Met behulp van Azure-mechanismen kunt u een abonnement of resource vergren delen om te voor komen dat uw zoek service per ongeluk of onbevoegde wordt verwijderd door gebruikers met beheerders rechten. Zie voor meer informatie [bronnen vergren delen om onverwachte verwijdering te voor komen](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Volgende stappen

* [Bewakings mogelijkheden](search-monitor-usage.md) bekijken die beschikbaar zijn in de portal
* Automatiseren met [Power shell](search-manage-powershell.md) of [Azure cli](search-manage-azure-cli.md)
* [Beveiligings functies](search-security-overview.md) voor het beveiligen van inhoud en bewerkingen controleren
* [Diagnostische logboek registratie](search-monitor-logs.md) inschakelen voor het bewaken van query's en het indexeren van werk belastingen