---
title: Inzicht in uw resourcestatus
titleSuffix: Azure Digital Twins
description: Zie Azure Resource Health gebruiken om de status van uw Azure Digital Apparaatdubbels-exemplaar te controleren.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616546"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Problemen oplossen met Azure Digital Apparaatdubbels: resource Health

[Azure resource Health](../service-health/resource-health-overview.md) helpt u bij het vaststellen en verkrijgen van ondersteuning voor service problemen die van invloed zijn op uw Azure-resources. Het rapporteert over de huidige en eerdere status van uw resources.

In dit artikel leest u hoe u informatie over de **resource status** kunt ophalen voor uw Azure Digital apparaatdubbels-instanties.

## <a name="use-azure-resource-health"></a>Azure Resource Health gebruiken

Azure Resource Health kunt u helpen controleren of uw Azure Digital Apparaatdubbels-exemplaar actief is. U kunt dit ook gebruiken om te leren of een regionale storing invloed heeft op de status van uw exemplaar.

Voer de volgende stappen uit om de status van uw exemplaar te controleren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer in het menu van uw exemplaar de optie _**resource status**_ onder *ondersteuning en probleem oplossing*. Hiermee gaat u naar de pagina voor het weer geven van de status geschiedenis van de resource. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Scherm opname met de pagina Resource status. De sectie status geschiedenis bevat een dagelijks rapport van de laatste negen dagen. Elke dag toont de status beschikbaar.":::

In de bovenstaande afbeelding wordt deze instantie weer gegeven als *beschikbaar* en is de afgelopen negen dagen. Zie [*overzicht van Azure resource Health*](../service-health/resource-health-overview.md)voor meer informatie over de *beschik bare* status en de andere status typen die kunnen worden weer gegeven.

U kunt ook meer te weten komen over de verschillende controles die in de resource status gaan voor verschillende typen Azure-resources in [*resource typen en status controles in azure resource Health*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere manieren om uw Azure Digital Apparaatdubbels-exemplaar te bewaken in de volgende artikelen:
* [*Problemen oplossen: metrische gegevens weer geven met Azure Monitor*](troubleshoot-metrics.md)
* [*Problemen oplossen: stel diagnoses*](troubleshoot-diagnostics.md)in.
* [*Problemen oplossen: waarschuwingen instellen*](troubleshoot-alerts.md)
