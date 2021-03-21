---
title: Versies in azure API Management | Microsoft Docs
description: Meer informatie over het concept van versies in azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040376"
---
# <a name="versions-in-azure-api-management"></a>Versies in azure API Management

Met versies kunt u groepen van gerelateerde Api's voor uw ontwikkel aars presen teren. U kunt versies gebruiken om de laatste wijzigingen in uw API veilig te verwerken. Clients kunnen ervoor kiezen om uw nieuwe API-versie te gebruiken wanneer ze klaar zijn, terwijl bestaande clients een oudere versie blijven gebruiken. Versies worden onderscheiden van een versie-id (dit is een wille keurige teken reeks waarde), en met een versie beheer schema kunnen clients bepalen welke versie van een API ze willen gebruiken.

Voor de meeste doel einden kan elke API-versie worden beschouwd als een eigen onafhankelijke API. Twee verschillende API-versies kunnen verschillende sets bewerkingen en verschillende beleids regels hebben.

Met versies kunt u het volgende doen:

- Publiceer meerdere versies van uw API tegelijk.
- Gebruik een pad, een query reeks of een header om onderscheid te maken tussen versies.
- Gebruik een wille keurige teken reeks waarde die u wilt gebruiken om uw versie te identificeren. Dit kan een getal, een datum of een naam zijn.
- Uw API-versies gegroepeerd weer geven op de ontwikkelaars Portal.
- Neem een bestaande (niet-versie bewerkte) API op en maak een nieuwe versie zonder bestaande clients te verbreken.

[Aan de slag met versies door de volgende procedure te volgen.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Versie beheer schema's

Verschillende API-Ontwikkel aars hebben verschillende vereisten voor versie beheer. Azure API Management schrijft geen enkele aanpak voor versie beheer, maar biedt verschillende opties.

### <a name="path-based-versioning"></a>Versie beheer op basis van een pad

Wanneer het schema voor het versie beheer van het pad wordt gebruikt, moet de versie-id worden opgenomen in het URL-pad voor API-aanvragen.

`https://apis.contoso.com/products/v1`En `https://apis.contoso.com/products/v2` kunnen bijvoorbeeld verwijzen naar dezelfde API, `products` maar naar versies `v1` en `v2` respectievelijk.

De indeling van een API-aanvraag-URL voor het gebruik van op paden gebaseerde versie beheer is: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Versie beheer op basis van koptekst

Wanneer het schema voor header versies wordt gebruikt, moet de versie-id worden opgenomen in een HTTP-aanvraag header voor API-aanvragen. U kunt de naam van de HTTP-aanvraag header opgeven.

U kunt bijvoorbeeld een aangepaste header maken met de naam `Api-Version` , en clients kunnen `v1` `v2` de waarde van de header of opgeven.

### <a name="query-string-based-versioning"></a>Versie beheer op basis van een query reeks

Wanneer het schema voor de versie van de query teken reeks wordt gebruikt, moet de versie-id worden opgenomen in een query reeks parameter voor API-aanvragen. U kunt de naam van de query teken reeks parameter opgeven.

De indeling van een API-aanvraag-URL voor het gebruik van versie beheer op basis van een query reeks is: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

`https://apis.contoso.com/products?api-version=v1`En `https://apis.contoso.com/products/api-version=v2` kunnen bijvoorbeeld verwijzen naar dezelfde API, `products` maar naar versies `v1` en `v2` respectievelijk.

## <a name="original-versions"></a>Oorspronkelijke versies

Als u een versie aan een niet-versie van API toevoegt, `Original` wordt er automatisch een versie gemaakt die op de standaard-URL reageert zonder dat een versie-id is opgegeven. De `Original` versie zorgt ervoor dat eventuele bestaande bellers niet worden onderbroken door het proces van het toevoegen van een versie. Als u een nieuwe API maakt met versies die aan het begin zijn ingeschakeld, `Original` wordt er geen versie gemaakt.

## <a name="how-versions-are-represented"></a>Hoe versies worden weer gegeven

Azure API Management onderhoudt een resource met de naam een *versieset* die een set versies voor één logische API vertegenwoordigt. Een versieset bevat de weergave naam van de API met versie nummer en het [versie beheer schema dat wordt gebruikt](#versioning-schemes) om aanvragen naar opgegeven versies te sturen.

Elke versie van een API wordt onderhouden als eigen API-resource, die vervolgens wordt gekoppeld aan een versieset. Een versieset kan Api's met verschillende bewerkingen of beleids regels bevatten. U kunt belang rijke wijzigingen aanbrengen tussen versies in een set.

De Azure Portal maakt versie sets voor u. U kunt de naam en beschrijving voor een versie die is ingesteld in de Azure Portal wijzigen.

U kunt versie sets rechtstreeks weer geven en beheren met behulp van [Azure cli](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [Resource Manager-sjablonen](/azure/templates/microsoft.apimanagement/service/apiversionsets)of de [Azure Resource Manager-API](/rest/api/apimanagement/2020-06-01-preview/apiversionset).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Een API die niet naar een versie is gemigreerd, migreren naar een API met versie

Wanneer u de Azure Portal gebruikt om versie beheer in te scha kelen op een bestaande API, worden de volgende wijzigingen aangebracht in uw API Management resources:

 * Er wordt een nieuwe versieset gemaakt.
 * De bestaande versie wordt behouden en [geconfigureerd als de `Original` API-versie](#original-versions). De API is gekoppeld aan de versieset, maar er hoeft geen versie-id te worden opgegeven.
 * De nieuwe versie wordt gemaakt als een nieuwe API en is gekoppeld aan de versieset. Deze nieuwe API moet worden geopend met behulp van het versie beheer schema en de id.

## <a name="versions-and-revisions"></a>Versies en revisies

Versies en revisies zijn verschillende functies. Elke versie kan meerdere revisies hebben, net als een API zonder versie nummer. U kunt revisies gebruiken zonder gebruik van versies, of op de andere manier. Doorgaans worden versies gebruikt om API-versies te scheiden met belang rijke wijzigingen, terwijl revisies kunnen worden gebruikt voor kleine en niet-brekende wijzigingen in een API.

Als u merkt dat de revisie wijzigingen heeft doorgevoerd of als u de revisie formeel wilt omzetten in een bèta-en test versie, kunt u een versie maken op basis van een revisie. Klik in het Azure Portal op het tabblad revisies op de optie versie maken van revisie in het context menu van de revisie.

## <a name="developer-portal"></a>ontwikkelaarsportal

In de [ontwikkelaars Portal](./api-management-howto-developer-portal.md) wordt elke versie van een API afzonderlijk weer gegeven.

![API Management ontwikkelaars Portal een lijst met versie-Api's weer geven](media/api-management-versions/portal-list.png)

In de details van een API wordt ook een lijst met alle versies van die API weer gegeven. Er `Original` wordt een versie weer gegeven zonder een versie-id.

![API Management ontwikkelaars Portal de details van een API en een lijst met versies voor die API weer geven](media/api-management-versions/portal-details.png)

> [!TIP]
> API-versies moeten worden toegevoegd aan een product voordat ze op de ontwikkelaars Portal worden weer gegeven.
