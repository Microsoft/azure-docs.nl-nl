---
title: Abonnementen en facturering
description: Meer informatie over abonnementen en facturering voor Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898480"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plannen en facturering voor Azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

## <a name="job-collection-plans"></a>Taak verzamelings plannen

In azure scheduler bevat een taak verzameling een specifiek aantal taken. De taak verzameling is de factureer bare entiteit en wordt geleverd met de Premium-abonnementen Standard, P10 Premium en P20, die hier worden beschreven: 

| Taak verzamelings plan | Maximum aantal taken per verzameling | Max. terugkeer patroon | Maximum aantal taak verzamelingen per abonnement | Limieten | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 taken per verzameling | Eén per minuut. Kan taken niet vaker dan één per minuut uitvoeren. | Elk Azure-abonnement kan Maxi maal 100 standaard taak verzamelingen bevatten. | Toegang tot de volledige functieset van scheduler | 
| **P10 Premium** | 50 taken per verzameling | Eén per minuut. Kan taken niet vaker dan één per minuut uitvoeren. | Elk Azure-abonnement kan Maxi maal 10.000 P10 Premium-taak verzamelingen bevatten. <a href="mailto:wapteams@microsoft.com">Neem contact met ons op</a>voor meer verzamelingen. | Toegang tot de volledige functieset van scheduler |
| **P20 Premium** | 1000 taken per verzameling | Eén per minuut. Kan taken niet vaker dan één per minuut uitvoeren. | Elk Azure-abonnement kan Maxi maal 5.000 P20 Premium-taak verzamelingen bevatten. <a href="mailto:wapteams@microsoft.com">Neem contact met ons op</a>voor meer verzamelingen. | Toegang tot de volledige functieset van scheduler |
|||||| 

## <a name="pricing"></a>Prijzen

Zie [prijzen van scheduler](https://azure.microsoft.com/pricing/details/scheduler/)voor meer informatie over prijzen.

## <a name="upgrade-or-downgrade-plans"></a>Upgrade of downgrade plannen

U kunt op elk gewenst moment een abonnement op een taak verzameling bijwerken of een downgrade uitvoeren voor de Premium-abonnementen Standard, P10 Premium en P20.

## <a name="active-status-and-billing"></a>Actieve status en facturering

Taak verzamelingen zijn altijd actief, tenzij uw volledige Azure-abonnement tijdelijk is uitgeschakeld vanwege problemen met de facturering. Hoewel u alle taken in een taak verzameling via één bewerking kunt uitschakelen, wordt met deze actie niet de facturerings status van de taak verzameling gewijzigd, zodat de taak verzameling *nog steeds* wordt gefactureerd. Lege taak verzamelingen worden beschouwd als actief en worden gefactureerd.

Als u er zeker van wilt zijn dat een taak verzameling niet wordt gefactureerd, moet u de taak verzameling verwijderen.

## <a name="standard-billable-units"></a>Standaard factureer bare eenheden

Een standaard factureer bare eenheid kan Maxi maal 10 standaard taak verzamelingen bevatten. Omdat een standaard taak verzameling Maxi maal 50 taken per verzameling kan hebben, kunt u met één standaard facturerings eenheid uw Azure-abonnement Maxi maal 500 taken, of tot bijna 22 *miljoen* uitvoeringen per maand. In deze lijst wordt uitgelegd hoe u wordt gefactureerd op basis van verschillende aantallen standaard taak verzamelingen:

* Als u tussen 1 en 10 standaard taak verzamelingen hebt, wordt u gefactureerd voor één standaard facturerings eenheid. 

* Als u tussen 11 en 20 standaard taak verzamelingen hebt, wordt u gefactureerd voor twee standaard facturerings eenheden. 

* Als u tussen 21 en 30 standaard taak verzamelingen hebt, wordt u gefactureerd voor drie standaard facturerings eenheden, enzovoort.

## <a name="p10-premium-billable-units"></a>Factureer bare eenheden voor P10 Premium

Een factureer bare P10-eenheid voor Premium kan Maxi maal 10.000 P10 Premium-taak verzamelingen bevatten. Omdat een P10 Premium-taak verzameling Maxi maal 50 taken per verzameling kan hebben, kunt u met één factuur eenheid van P10 Premium Maxi maal 500.000 taken uitvoeren, of tot bijna 22 *miljard* taak uitvoeringen per maand. 

P10 Premium-taak verzamelingen bieden dezelfde mogelijkheden als standaard taak verzamelingen, maar bieden een prijs grens voor apps waarvoor veel taak verzamelingen nodig zijn en die meer schaal baarheid bieden. In deze lijst wordt uitgelegd hoe u factureert op basis van verschillende aantallen P10 Premium-taak verzamelingen:

* Als u tussen 1 en 10.000 Premium-taak verzamelingen hebt, wordt u gefactureerd voor één factuur eenheid van P10 Premium. 

* Als u tussen 10.001 en 20.000 P10 Premium-taak verzamelingen hebt, wordt u gefactureerd voor 2 P10 Premium-facturerings eenheden, enzovoort.

## <a name="p20-premium-billable-units"></a>Factureer bare eenheden voor P20 Premium

Een factureer bare P20-eenheid voor Premium kan Maxi maal 5.000 P20 Premium-taak verzamelingen bevatten. Omdat een P20 Premium-taak verzameling Maxi maal 1.000 taken per taak verzameling kan hebben, kunt u met één P20 Premium-facturerings eenheid uw Azure-abonnement tot 5.000.000 taken of tot bijna 220 *miljard* taak uitvoeringen per maand.

P20 Premium-taak verzamelingen bieden dezelfde mogelijkheden als P10 Premium-taak verzamelingen, maar bieden ook ondersteuning voor een groter aantal taken per verzameling en een groter aantal taken in het algemeen dan P10 Premium, waardoor u meer schaal baarheid kunt bieden.

## <a name="plan-comparison"></a>Vergelijking plannen

* Als u meer dan 100 standaard taak verzamelingen (10 standaard facturerings eenheden) hebt, kunt u een betere deal krijgen door alle taak verzamelingen in een Premium-abonnement te hebben.

* Als u één standaard taak verzameling en één Premium-taak verzameling hebt, wordt u gefactureerd voor één standaard facturerings eenheid *en* één Premium-facturerings eenheid.

  De Scheduler service-facturen op basis van het aantal actieve taak verzamelingen die standaard of Premium zijn.

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)