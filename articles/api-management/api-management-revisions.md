---
title: Revisies in Azure API Management | Microsoft Docs
description: Meer informatie over het concept van revisies in Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812134"
---
# <a name="revisions-in-azure-api-management"></a>Revisies in Azure API Management

Met revisies kunt u uw API's op een gecontroleerde en veilige manier wijzigen. Wanneer u wijzigingen wilt aanbrengen, maakt u een nieuwe revisie. Vervolgens kunt u de API bewerken en testen zonder uw API-consumenten te verstoren. Wanneer u klaar bent, maakt u de revisie actueel. Tegelijkertijd kunt u eventueel een vermelding in het wijzigingslogboek plaatsen om uw API-consumenten up-to-date te houden met wat er is gewijzigd. Het wijzigingslogboek wordt gepubliceerd naar uw ontwikkelaarsportal.

> [!NOTE]
> De ontwikkelaarsportal is niet beschikbaar in de verbruikslaag.

Met revisies kunt u het volgende doen:

- U kunt veilig wijzigingen aanbrengen in uw API-definities en -beleid, zonder uw productie-API te verstoren.
- Probeer wijzigingen uit voordat u ze publiceert.
- Documenteren welke wijzigingen u maakt, zodat uw ontwikkelaars kunnen begrijpen wat er nieuw is.
- Terugdraaien als u problemen vindt.

[Ga aan de slag met revisies door ons overzicht te volgen.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Toegang tot specifieke revisies

Elke revisie van uw API kan worden gebruikt met behulp van een speciaal gevormde URL. Toevoegen aan het einde van de API-URL, maar vóór de querytekenreeks, om toegang te krijgen tot een `;rev={revisionNumber}` specifieke revisie van die API. U kunt deze URL bijvoorbeeld gebruiken om toegang te krijgen tot revisie 3 van de `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Standaard heeft elke revisie dezelfde beveiligingsinstellingen als de huidige revisie. U kunt het beleid voor een specifieke revisie bewust wijzigen als u voor elke revisie een andere beveiliging wilt toepassen. U kunt bijvoorbeeld een [IP-filterbeleid](./api-management-access-restriction-policies.md#RestrictCallerIPs) toevoegen om te voorkomen dat externe aanroepers toegang krijgen tot een revisie die nog in ontwikkeling is.

Een revisie kan offline worden genomen, waardoor deze niet toegankelijk is voor aanroepers, zelfs als ze proberen de revisie te openen via de URL. U kunt een revisie als offline markeren met behulp van de Azure Portal. Als u PowerShell gebruikt, kunt u de `Set-AzApiManagementApiRevision` cmdlet gebruiken en het `Path` argument instellen op `$null` .

> [!NOTE]
> We raden u aan revisies offline te halen wanneer u ze niet gebruikt voor het testen.

## <a name="current-revision"></a>Huidige revisie

Eén revisie kan worden ingesteld als de *huidige* revisie. Deze revisie wordt gebruikt voor alle API-aanvragen die geen expliciet revisienummer in de URL opgeven. U kunt terugdraaien naar een eerdere revisie door die revisie in te stellen als actueel.

U kunt een revisie instellen als actueel met behulp van de Azure Portal. Als u PowerShell gebruikt, kunt u de `New-AzApiManagementApiRelease` cmdlet gebruiken.

## <a name="revision-descriptions"></a>Revisiebeschrijvingen

Wanneer u een revisie maakt, kunt u een beschrijving instellen voor uw eigen traceringsdoeleinden. Beschrijvingen worden niet afgespeeld voor uw API-gebruikers.

Wanneer u een revisie als actueel ingeeft, kunt u eventueel ook een openbare wijzigingslogboeknota opgeven. Het wijzigingslogboek is opgenomen in de ontwikkelaarsportal die uw API-gebruikers kunnen bekijken. U kunt de notitie van het wijzigingslogboek wijzigen met behulp van de `Update-AzApiManagementApiRelease` PowerShell-cmdlet .

## <a name="versions-and-revisions"></a>Versies en revisies

Versies en revisies zijn afzonderlijke functies. Elke versie kan meerdere revisies hebben, net als een API zonder versie. U kunt revisies gebruiken zonder versies of andersom. Normaal gesproken worden versies gebruikt om API-versies te scheiden met belangrijke wijzigingen, terwijl revisies kunnen worden gebruikt voor kleine en niet-belangrijke wijzigingen in een API.

Als u vindt dat uw revisie belangrijke wijzigingen heeft, of als u uw revisie formeel wilt wijzigen in een bèta-/testversie, kunt u een versie van een revisie maken. Klik met Azure Portal de knop 'Versie maken op basis van revisie' in het contextmenu van de revisie op het tabblad Revisies.
