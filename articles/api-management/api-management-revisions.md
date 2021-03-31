---
title: Revisies in azure API Management | Microsoft Docs
description: Meer informatie over het concept van revisies in azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403235"
---
# <a name="revisions-in-azure-api-management"></a>Revisies in azure API Management

Met revisies kunt u op een gecontroleerde en veilige manier wijzigingen aanbrengen in uw Api's. Als u wijzigingen wilt aanbrengen, maakt u een nieuwe revisie. U kunt de API vervolgens bewerken en testen zonder uw API-consumenten te verstoren. Wanneer u klaar bent, kunt u uw revisie actueel maken. Op hetzelfde moment kunt u eventueel een item in het wijzigings logboek plaatsen om uw API-gebruikers up-to-date te houden met wat er is gewijzigd. Het wijzigings logboek wordt gepubliceerd naar uw ontwikkelaars Portal.

> [!NOTE]
> De ontwikkelaars Portal is niet beschikbaar in de laag verbruik.

Met revisies kunt u het volgende doen:

- Wijzig veilig wijzigingen in uw API-definities en-beleid zonder uw productie-API te verstoren.
- Probeer wijzigingen uit voordat u ze publiceert.
- Documenteer de wijzigingen die u aanbrengt, zodat uw ontwikkel aars kunnen begrijpen wat er nieuw is.
- Terugdraaien als u problemen ondervindt.

[Ga aan de slag met revisies door ons overzicht te volgen.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Toegang tot specifieke revisies

Elke revisie van uw API kan worden geopend met behulp van een speciaal gevormde URL. Toegevoegd aan `;rev={revisionNumber}` het einde van de API-URL, maar vóór de query reeks, om toegang te krijgen tot een specifieke revisie van die API. U kunt deze URL bijvoorbeeld gebruiken om toegang te krijgen tot revisie 3 van de `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Elke revisie heeft standaard dezelfde beveiligings instellingen als de huidige revisie. U kunt het beleid voor een specifieke revisie opzettelijk wijzigen als u voor elke revisie een andere beveiliging wilt Toep assen. U kunt bijvoorbeeld een [IP-filter beleid](./api-management-access-restriction-policies.md#RestrictCallerIPs) toevoegen om te voor komen dat externe Bellers toegang krijgen tot een revisie die nog steeds wordt ontwikkeld.

Een revisie kan offline worden gehaald, waardoor deze niet toegankelijk is voor aanroepers, zelfs als ze proberen toegang te krijgen tot de revisie via de URL. U kunt een revisie als offline markeren met behulp van de Azure Portal. Als u Power shell gebruikt, kunt u de `Set-AzApiManagementApiRevision` cmdlet gebruiken en het `Path` argument instellen op `$null` .

> [!NOTE]
> We raden u aan revisies offline uit te voeren wanneer u deze niet gebruikt voor het testen.

## <a name="current-revision"></a>Huidige revisie

Eén revisie kan worden ingesteld als de *huidige* revisie. Deze revisie wordt gebruikt voor alle API-aanvragen die geen expliciet revisie nummer opgeven in de URL. U kunt teruggaan naar een eerdere revisie door die revisie als actueel in te stellen.

U kunt een revisie als actueel instellen met behulp van de Azure Portal. Als u Power shell gebruikt, kunt u de `New-AzApiManagementApiRelease` cmdlet gebruiken.

## <a name="revision-descriptions"></a>Beschrijvingen van wijzigingen

Wanneer u een revisie maakt, kunt u een beschrijving voor uw eigen tracerings doeleinden instellen. Beschrijvingen worden niet afgespeeld voor uw API-gebruikers.

Wanneer u een revisie instelt als actueel, kunt u eventueel ook een opmerking voor een openbaar wijzigingen logboek opgeven. Het wijzigings logboek is opgenomen in de ontwikkelaars portal voor uw API-gebruikers die u wilt weer geven. U kunt de notitie van het wijzigings logboek wijzigen met behulp van de `Update-AzApiManagementApiRelease` Power shell-cmdlet.

## <a name="versions-and-revisions"></a>Versies en revisies

Versies en revisies zijn verschillende functies. Elke versie kan meerdere revisies hebben, net als een API zonder versie nummer. U kunt revisies gebruiken zonder gebruik van versies, of op de andere manier. Doorgaans worden versies gebruikt om API-versies te scheiden met belang rijke wijzigingen, terwijl revisies kunnen worden gebruikt voor kleine en niet-brekende wijzigingen in een API.

Als u merkt dat de revisie wijzigingen heeft doorgevoerd of als u de revisie formeel wilt omzetten in een bèta-en test versie, kunt u een versie maken op basis van een revisie. Klik in het Azure Portal op het tabblad revisies op de optie versie maken van revisie in het context menu van de revisie.
