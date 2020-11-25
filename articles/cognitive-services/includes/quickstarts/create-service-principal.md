---
title: Een Azure-service-principal maken
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097483"
---
## <a name="create-an-azure-service-principal"></a>Een Azure-service-principal maken

Als u uw toepassing wilt laten communiceren met uw Azure-account, hebt u een Azure-service-principal nodig om machtigingen te beheren. Volg de instructies in [Een Azure-service-principal maken](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Wanneer u een service-principal maakt, ziet u dat deze een geheime waarde, een id en een toepassings-id heeft. Sla de toepassings-id en het geheim op een tijdelijke locatie op voor latere stappen.