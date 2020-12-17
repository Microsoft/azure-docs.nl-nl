---
title: bestand opnemen
description: bestand opnemen
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614507"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch-accounts per regio per abonnement | 1-3 |50 |
| Toegewezen kerngeheugens per Batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kerngeheugens met een lage prioriteit per Batch-account | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](/rest/api/batchservice/job/get#jobstate)** taken en taakplanningen per Batch-account (**voltooide** taken hebben geen limiet) | 100-300 | 1000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

<sup>1</sup>Neem contact op met de ondersteuning van Azure als u een grotere hoeveelheid wilt aanvragen.

> [!NOTE]
> De standaardlimieten variëren afhankelijk van het type abonnement dat u gebruikt voor het maken van een Batch-account. De weergegeven kerngeheugenquota zijn voor Batch-accounts in de modus Batch-service. [Bekijk de quota in uw Batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Om ervoor te zorgen dat onze capaciteit beter kan worden beheerd tijdens de wereldwijde gezondheidspandemie, zijn de standaard kerngeheugenquota voor nieuwe Batch-accounts in sommige regio's en voor sommige typen abonnementen verlaagd. In sommige gevallen zijn de bovenstaande waarden tot nul kerngeheugens verlaagd. Wanneer u een nieuw Batch-account maakt, moet u [uw quotum voor kerngeheugens controleren](../articles/batch/batch-quota-limit.md#view-batch-quotas) en indien nodig [een verhoging van het kerngeheugenquotum aanvragen](../articles/batch/batch-quota-limit.md#increase-a-quota). Als alternatief kunt u ook Batch-accounts opnieuw gebruiken die al voldoende quota hebben.