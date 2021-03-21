---
title: Azure-rollen instellen voor Azure-beheerders toegang
titleSuffix: Azure Cognitive Search
description: Op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure in de Azure Portal voor het beheren en delegeren van beheer taken voor Azure Cognitive Search management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519496"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure-rollen instellen voor beheerders toegang tot Azure Cognitive Search

Azure biedt een [globaal autorisatie model op basis van rollen](../role-based-access-control/role-assignments-portal.md) voor alle services die worden beheerd via de portal-of Resource Manager-api's. De rollen eigenaar, bijdrager en lezer bepalen het *Service beheer* niveau voor Active Directory gebruikers, groepen en beveiligings-principals die aan elke rol zijn toegewezen. 

> [!Note]
> Er is geen op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor het beveiligen van inhoud op de service. U kunt een API-sleutel van de beheerder of de query-API-sleutel voor geverifieerde aanvragen voor de service zelf gebruiken. Voor toegang op basis van identiteiten via zoek resultaten kunt u beveiligings filters maken om de resultaten te beperken op basis van identiteit, en documenten te verwijderen waarvoor de aanvrager geen toegang moet hebben. Zie [beveiligings filters](search-security-trimming-for-azure-search.md)voor meer informatie.

## <a name="management-tasks-by-role"></a>Beheer taken per rol

Voor Azure Cognitive Search zijn rollen gekoppeld aan machtigings niveaus die ondersteuning bieden voor de volgende beheer taken:

| Rol | Taak |
| --- | --- |
| Eigenaar |Maak of verwijder de service of een object op de service, inclusief de API-sleutels, indexen, Indexeer functies, Indexeer gegevens bronnen en de planningen voor de Indexeer functie.<p>Bekijk de status van de service, inclusief aantallen en opslag grootte.<p>Een rollidmaatschap toevoegen of verwijderen (alleen een eigenaar kan het lidmaatschap van een rol beheren).<p>Abonnements beheerders en service-eigen aren hebben automatisch lidmaatschap van de rol eigen aren. |
| Inzender | Hetzelfde toegangs niveau als eigenaar, min Azure Role Management. Een inzender kan bijvoorbeeld objecten maken of verwijderen, of de [API-sleutels](search-security-api-keys.md)weer geven en opnieuw genereren, maar kan geen rollidmaatschap wijzigen.<br><br>[Search service bijdrager](../role-based-access-control/built-in-roles.md#search-service-contributor) is gelijk aan de ingebouwde rol algemene bijdrager. |
| Lezer |Bekijk service-essentiële elementen, zoals service-eind punten, abonnementen, resource groep, regio, laag en capaciteit. U kunt ook metrische service gegevens weer geven, zoals gemiddeld aantal query's per seconde, op het tabblad bewaking. Leden van deze rol kunnen de gegevens van de index, Indexeer functie, gegevens bron of vaardigheidset niet weer geven. Dit geldt ook voor de gebruiks gegevens voor deze objecten, zoals hoeveel indexen er op de service bestaan. |

Rollen verlenen geen toegangs rechten voor het service-eind punt. Zoek service bewerkingen, zoals index beheer, index populatie en query's op zoek gegevens, worden beheerd via API-Keys, niet op rollen. Zie [Manage API-Keys (](search-security-api-keys.md)Engelstalig) voor meer informatie.

## <a name="permissions-table"></a>Machtigings tabel

De volgende tabel bevat een overzicht van de bewerkingen die zijn toegestaan in azure Cognitive Search en met welke sleutel de toegang tot een bepaalde bewerking wordt ontgrendeld.

Azure RBAC-machtigingen zijn van toepassing op Portal bewerkingen en Service beheer (maken, verwijderen of wijzigen van een service of de bijbehorende API-sleutels). API-sleutels worden gemaakt nadat een service bestaat en zijn van toepassing op inhouds bewerkingen op de service. Daarnaast kunt u voor aan inhoud gerelateerde bewerkingen in de portal, zoals het maken of verwijderen van objecten, een Azure RBAC-eigenaar of Inzender communiceren met de service met een impliciete beheerder API-sleutel.

| Bewerking | Beheerd door |
|-----------|-------------------------|
| Een service maken | Azure RBAC-machtigingen: eigenaar of bijdrager |
| Een service schalen | Azure RBAC-machtigingen: eigenaar of bijdrager|
| Een service verwijderen | Azure RBAC-machtigingen: eigenaar of bijdrager |
| Beheer-of query sleutels beheren | Azure RBAC-machtigingen: eigenaar of bijdrager|
| Service-informatie weer geven in de portal of een beheer-API | Azure RBAC-machtigingen: eigenaar, bijdrager of lezer  |
| Object informatie en metrische gegevens weer geven in de portal of een beheer-API | Azure RBAC-machtigingen: eigenaar of bijdrager |
| Objecten op de service maken, wijzigen, verwijderen: <br>Indexen en onderdeel onderdelen (met inbegrip van analyse definities, Score profielen, CORS-opties), Indexeer functies, gegevens bronnen, synoniemen, suggesties | Beheerder sleutel als u gebruikmaakt van een API, Azure RBAC-eigenaar of Inzender als u de portal gebruikt |
| Een query op uitvoeren op een index | Beheerder of query sleutel als u gebruikmaakt van een API, Azure RBAC-eigenaar of Inzender als u de portal gebruikt |
| Systeem informatie over objecten opvragen, zoals het retour neren van statistieken, aantallen en lijsten met objecten | Beheerder sleutel als u gebruikmaakt van een API, Azure RBAC-eigenaar of Inzender als u de portal gebruikt |

## <a name="next-steps"></a>Volgende stappen

+ [Beheren met PowerShell](search-manage-powershell.md) 
+ [Prestaties en optimalisatie in azure Cognitive Search](search-performance-optimization.md)
+ [Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md).
