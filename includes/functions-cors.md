---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648272"
---
Azure Functions ondersteunt cross-Origin Resource Sharing (CORS). CORS wordt geconfigureerd [in de portal](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) en via de [Azure cli](/cli/azure/functionapp/cors). De lijst van toegestane CORS-oorsprong is van toepassing op het niveau van de functie-app. Als CORS is ingeschakeld, bevatten de antwoorden de `Access-Control-Allow-Origin` koptekst. Zie voor meer informatie [Cross-origin-resources delen](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 