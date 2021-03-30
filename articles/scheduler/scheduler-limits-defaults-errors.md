---
title: Limieten, quota en drempel waarden in azure scheduler
description: Meer informatie over limieten, quota's, standaard waarden en bandbreedte drempels voor Azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898528"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limieten, quota en beperkings drempels in azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

## <a name="limits-quotas-and-thresholds"></a>Limieten, quota en drempel waarden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-MS-Request-id-header

Elke aanvraag die wordt gedaan voor de Scheduler-service retourneert een antwoord header met de naam **x-MS-Request-id**. Deze header bevat een ondoorzichtige waarde waarmee de aanvraag uniek wordt geïdentificeerd. Dus als een aanvraag consistent is mislukt en u hebt bevestigd dat de aanvraag juist is ingedeeld, kunt u de fout melden aan micro soft door de waarde voor **x-MS-Request-id** op te geven en deze gegevens op te nemen: 

* De waarde voor **x-MS-Request-id**
* De geschatte tijd waarop de aanvraag is gedaan 
* De id's voor het Azure-abonnement, de taak verzameling en de taak 
* Het type bewerking dat de aanvraag heeft gedaan

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Plannen en facturering voor Azure scheduler](scheduler-plans-billing.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)