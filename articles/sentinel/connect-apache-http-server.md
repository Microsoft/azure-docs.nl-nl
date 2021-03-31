---
title: Uw Apache HTTP-server verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Apache HTTP server-connector voor het ophalen van Apache-Logboeken in azure Sentinel. Bekijk Apache-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096851"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Uw Apache HTTP-server verbinden met Azure Sentinel

> [!IMPORTANT]
> De Apache HTTP-server connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Apache HTTP-server verbindt met Azure Sentinel. Met de Apache HTTP-server-connector kunt u eenvoudig uw Apache HTTP-server logboeken opnemen in azure Sentinel, zodat u de gegevens in werkmappen kunt weer geven, er een query op moet uitvoeren om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Apache HTTP-server en Azure-Sentinel maakt gebruik van lokale bestands verwerking door de Log Analytics-agent.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet schrijf machtiging hebben voor de Azure Sentinel-werk ruimte.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Apache HTTP-server logboeken configureren en integreren via Log Analytics agent

De Apache HTTP-server configureren voor het verzenden van logboek bestanden naar uw Azure-werk ruimte via de Log Analytics-agent.
Log Analytics-agent configureren voor het lezen van Apache HTTP-server logboek bestanden.

1. Volg de instructies in https://httpd.apache.org/docs/2.4/logs.html om de locatie van de logboek bestanden in de Apache HTTP-server in te stellen.

1. Selecteer in het navigatie menu van Azure de optie **gegevens connectors** en selecteer vervolgens **Apache HTTP-server (preview)**.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina Apache HTTP server.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u verbinding kunt maken met de Apache HTTP-server met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
