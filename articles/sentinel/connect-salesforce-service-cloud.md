---
title: Cloud gegevens van de Sales Force-service verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Sales Force-Service Cloud Data Connector voor het ophalen van Sales Force-Logboeken in azure Sentinel. Bekijk Sales Force-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570538"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Uw Sales Force-service-cloud verbinden met Azure Sentinel

> [!IMPORTANT]
> De Cloud connector van de Sales Force-service is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u de Cloud oplossing van de Sales Force-service verbindt met Azure Sentinel. Met de data connector van de Sales Force-service kunt u eenvoudig uw Sales Force-gegevens verbinden met Azure Sentinel, zodat u deze kunt weer geven in werkmappen, het gebruik ervan om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Sales Force Service Cloud en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- U moet lees-en schrijf machtigingen hebben voor Azure Functions om een functie-app te kunnen maken. Meer [informatie over Azure functions](../azure-functions/index.yml).

- U moet beschikken over de volgende Sales Force-REST API referenties: **Sales Force API-gebruikers naam**, **Sales Force API-wacht woord**, Sales **Force-beveiligings token**, Sales Force- **sleutel** **van Sales Force.** Meer [informatie over Sales Force-rest API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Een Sales Force-Service-Cloud configureren en verbinden

De Sales Force-Service-Cloud kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie met **gegevens connectors** de optie **Sales Force Service-Cloud (preview)** en open vervolgens de **pagina connector**.

1. Volg de stappen die worden beschreven in de sectie **configuratie** van de pagina connector.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **CustomLogs** in de `SalesforceServiceCloud_CL` tabel.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Sales Force Service-Cloud kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.