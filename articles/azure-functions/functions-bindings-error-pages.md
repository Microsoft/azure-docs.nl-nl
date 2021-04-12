---
title: Azure Functions fout afhandeling en richt lijnen voor opnieuw proberen
description: Meer informatie over het afhandelen van fouten en het opnieuw proberen van gebeurtenissen in Azure Functions met koppelingen naar specifieke bindings fouten.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93284447"
---
# <a name="azure-functions-error-handling-and-retries"></a>Fout afhandeling en nieuwe pogingen Azure Functions

Het afhandelen van fouten in Azure Functions is belang rijk om te voor komen dat gegevens verloren gaan, gemiste gebeurtenissen en de status van uw toepassing te controleren.

In dit artikel worden algemene strategieën beschreven voor het afhandelen van fouten, samen met koppelingen naar binding-specifieke fouten.

## <a name="handling-errors"></a>Afhandeling van fouten

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Bindings fout codes

Wanneer u integreert met Azure-Services, kunnen fouten afkomstig zijn van de Api's van de onderliggende services. Informatie met betrekking tot binding-specifieke fouten is beschikbaar in de sectie **uitzonde ringen en retour codes** van de volgende artikelen:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT-hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
