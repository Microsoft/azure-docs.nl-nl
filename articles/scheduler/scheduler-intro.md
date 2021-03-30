---
title: Wat is Azure Scheduler?
description: Planning maken en geautomatiseerde taken uitvoeren die services binnen of buiten Azure aanroepen
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 66ec285554299214122a4093837d3506bf642b13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368125"
---
# <a name="what-is-azure-scheduler"></a>Wat is Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) helpt u bij het maken van [taken](../scheduler/scheduler-concepts-terms.md) die in de cloud worden uitgevoerd door declaratief acties te beschrijven. Vervolgens worden deze acties automatisch gepland en uitgevoerd door de service. U kunt bijvoorbeeld services binnen en buiten Azure aanroepen, zoals het aanroepen van HTTP of HTTPS-eindpunten, en berichten plaatsen in Azure Storage-wachtrijen en Azure Service Bus-wachtrijen of -onderwerpen. U kunt taken direct of op een later tijdstip uitvoeren. Scheduler ondersteunt met gemak [complexe schema's en geavanceerde terugkeerpatronen](../scheduler/scheduler-advanced-complexity.md). Scheduler geeft aan wanneer taken moeten worden uitgevoerd, houdt een geschiedenis bij van de taakresultaten die u kunt bekijken en plant op voorspelbare en betrouwbare wijze de uitvoering van workloads.

Andere Azure-planfuncties maken op de achtergrond gebruik van Scheduler, zoals [Azure WebJobs](../app-service/webjobs-create.md), een onderdeel van de functie [Web Apps](https://azure.microsoft.com/services/app-service/web/) in Azure App Service. U kunt de communicatie voor deze acties beheren door gebruik te maken van de [scheduler rest API](/rest/api/scheduler/), waarmee u de communicatie voor deze acties kunt beheren.

Hier volgen enkele scenario's waarbij Scheduler u kan helpen:

* Terugkerende app-acties uitvoeren: zoals het regelmatig gegevens verzamelen van Twitter in een feed.

* Dagelijks onderhoud uitvoeren: het dagelijks verwijderen van logboeken, het uitvoeren van back-ups en overige onderhoudstaken.

  Als beheerder wilt u bijvoorbeeld de komende negen maanden elke dag om 01.00 uur een back-up maken van uw database.

Hoewel u Scheduler gebruiken kunt om geplande workloads te maken, onderhouden uit te voeren, host Scheduler de workload niet en voert het geen code uit. De service *roept* de service of programmacode aan die elders wordt gehost, bijvoorbeeld in Azure, on-premises of bij een andere provider. Dit aanroepen doet Scheduler via HTTP, HTTPS, een opslagwachtrij, een Service Bus-wachtrij of een Service Bus-onderwerp.

Als u taken en [taak verzamelingen](../scheduler/scheduler-concepts-terms.md)wilt maken, plannen, beheren, bijwerken of verwijderen, kunt u code, de [scheduler-rest API](/rest/api/scheduler/)of de [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md)gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Plannen en facturering voor Azure scheduler](scheduler-plans-billing.md)
* [Complexe schema's en geavanceerde terugkeer patronen bouwen met Azure scheduler](scheduler-advanced-complexity.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)