---
title: Naslaginformatie - Azure API Management-resourcelogboek
description: Schemaverwijzing voor het Azure API Management-resourcelogboek GatewayLogs
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: df0018e323bc0c5725c9752b25b77612f035c196
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582500"
---
# <a name="reference-api-management-resource-log-schema"></a>Naslaginformatie: Schema voor API Management-resourcelogboek

Dit artikel biedt een schemaverwijzing voor het Azure API Management-resourcelogboek GatewayLogs. Logboekvermeldingen bevatten ook velden in het [algemene schema op het hoogste niveau](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Zie [Gepubliceerde API's bewaken](api-management-howto-use-azure-monitor.md#resource-logs) om het verzamelen van resourcelogboeken in API Management in te schakelen.

## <a name="gatewaylogs-schema"></a>GatewayLogs-schema

De volgende eigenschappen worden vastgelegd voor elke API-aanvraag.

| Eigenschap  | Type | Beschrijving |
| ------------- | ------------- | ------------- |
| method | tekenreeks | HTTP-methode van de inkomende aanvraag |
| url | tekenreeks | URL van de binnenkomende aanvraag |
| responseCode | geheel getal | Statuscode van het HTTP-antwoord dat is verzonden naar een client |
| responseSize | geheel getal | Aantal bytes dat naar een client is verzonden tijdens de verwerking van aanvragen | 
| cache | tekenreeks | Status van de betrokkenheid van de API Management-cache bij het verwerken van aanvragen (treffer, misser, geen) | 
| apiId | tekenreeks | ID van de API-entiteit voor de huidige aanvraag | 
| operationId | tekenreeks | ID van de bewerkingsentiteit voor de huidige aanvraag | 
| clientProtocol | tekenreeks | HTTP-protocolversie van de inkomende aanvraag |
| clientTime | geheel getal | Aantal milliseconden dat in totaal is besteed aan I/O van de client (verbinding maken, bytes verzenden en ontvangen) | 
| apiRevision | tekenreeks | API-revisie voor huidige aanvraag | 
| clientTlsVersion| tekenreeks | TLS-versie die wordt gebruikt door client die aanvraag verzendt |
| lastError | object | Voor een onsuccesvolle aanvraag, details over de laatste fout bij de aanvraagverwerking | 
| backendMethod | tekenreeks | HTTP-methode van de aanvraag die is verzonden naar een back-end |
| backendUrl | tekenreeks | URL van de aanvraag die is verzonden naar een back-end |
| backendResponseCode | geheel getal | Code van het HTTP-antwoord dat is ontvangen van een back-end |
| backedProtocol | tekenreeks | HTTP-protocolversie van de aanvraag die is verzonden naar een back-end |
| backendTime | geheel getal | Aantal milliseconden dat in totaal is besteed aan IO van de back-end (verbinding maken, bytes verzenden en ontvangen) | 


## <a name="next-steps"></a>Volgende stappen

* Zie [Gepubliceerde API's bewaken](api-management-howto-use-azure-monitor.md) voor informatie over het bewaken van API's in API Management
* Lees meer over [Algemeen en servicespecifiek schema voor Azure-resourcelogboeken](../azure-monitor/essentials/resource-logs-schema.md)

