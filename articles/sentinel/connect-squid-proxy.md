---
title: Inktvis-proxy gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de pijl inktvis proxy-gegevens connector voor het ophalen van inktvis-proxy Logboeken in azure Sentinel. Bekijk de inktvis-proxy gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090442"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Uw inktvis-proxy verbinden met Azure Sentinel

> [!IMPORTANT]
> De pijl inktvis proxy connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw inktvis-proxy apparaat verbindt met Azure Sentinel. Met de pijl inktvis proxy data connector kunt u eenvoudig uw inktvis-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen pijl inktvis-proxy en Azure-Sentinel maakt gebruik van lokale bestands verwerking door de Log Analytics-agent.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Richting van de proxy logboeken van inktvis naar de Log Analytics-agent  

Configureer de proxy inktvis om logboek bestanden te verzenden naar uw Azure-werk ruimte via de Log Analytics-agent.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **Data connectors** de **pijl inktvis-proxy (preview-versie)** en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **inktvis proxy** connector:

    1. De agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel).

    1. De logboeken configureren die moeten worden verzameld

        - In de geavanceerde instellingen van de werk ruimte voegt u een aangepast logboek type toe, uploadt u een voorbeeld bestand en configureert u als gericht.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder **aangepaste logboeken**, in de `SquidProxy_CL` tabel.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u verbinding kunt maken met de inktvis-proxy met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
