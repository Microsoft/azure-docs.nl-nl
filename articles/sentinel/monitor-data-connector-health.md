---
title: De status van uw gegevens connectors controleren met deze Azure-Sentinel-werkmap | Microsoft Docs
description: Gebruik de status controle werkmap om de connectiviteit en prestaties van uw data connectors bij te houden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656987"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>De status van uw gegevens connectors controleren met deze Azure Sentinel-werkmap

Met de **Data connectors Health Monitoring-werkmap** kunt u de status, connectiviteit en prestaties van uw data connectors volgen vanuit Azure Sentinel. De werkmap bevat extra monitors, detecteert afwijkingen en biedt inzicht in de status van de gegevens opname van de werk ruimte. U kunt de logica van de werkmap gebruiken om de algemene status van de opgenomen gegevens te controleren en om aangepaste weer gaven en waarschuwingen op basis van regels te maken.

## <a name="use-the-health-monitoring-workbook"></a>De werk blad status controle gebruiken

1. Selecteer in de Azure-Sentinel Portal de optie **werkmappen** in het menu **bedreigingen beheer** .

1. In de galerie met **werkmappen** voert u de *status* in de zoek balk in en selecteert u **status controle van gegevens verzameling** onder de resultaten.

1. Selecteer **sjabloon weer geven** als u de werkmap wilt gebruiken of selecteer **Opslaan** om een Bewerk bare kopie van de werkmap te maken. Wanneer u de kopie hebt gemaakt, selecteert u **opgeslagen werkmap weer geven**.

1. In de werkmap selecteert u eerst het **abonnement** en de **werk ruimte** die u wilt weer geven en definieert u de **time Range** om de gegevens te filteren op basis van uw behoeften. Gebruik de wissel knop **Help weer geven** om in-place uitleg van de werkmap weer te geven.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="landings pagina status controle werk blad Data Connector" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Deze werkmap bevat drie secties met tabbladen:

1. Het tabblad **overzicht** bevat de algemene status van het opnemen van gegevens in de geselecteerde werk ruimte: volume metingen, EPS-tarieven en tijdstip laatste ontvangen logboek.

1. Het tabblad **afwijkingen van gegevens verzameling** helpt u bij het detecteren van afwijkingen in het proces voor het verzamelen van gegevens, op tabel en gegevens bron. Elk tabblad toont afwijkingen voor een bepaalde tabel (het tabblad **Algemeen** bevat een verzameling tabellen). De afwijkingen worden berekend met behulp van de functie **series_decompose_anomalies ()** die een **afwijkende Score** retourneert. [Meer informatie over deze functie](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Stel de volgende para meters in voor de functie die moet worden geëvalueerd:

    - **AnomaliesTimeRange**: Deze tijdkiezer is alleen van toepassing op de weergave van afwijkingen in gegevensverzameling.
    - **SampleInterval**: Het tijdsinterval waarin gegevens worden bemonsterd binnen het opgegeven tijdsbereik. De afwijkingsscore wordt alleen berekend voor de gegevens van het laatste interval.
    - **PositiveAlertThreshold**: Deze waarde definieert de drempelwaarde voor de positieve afwijkingsscore. Decimale waarden worden geaccepteerd.
    - **NegativeAlertThreshold**: Deze waarde definieert de drempelwaarde voor de negatieve afwijkingsscore. Decimale waarden worden geaccepteerd.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="pagina afwijkingen van de gegevens connector status controle-werkmap" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. Op het tabblad **agent informatie** vindt u informatie over de status van de log Analytics agents die op de verschillende machines zijn geïnstalleerd, of Azure VM, een andere Cloud-VM, een on-premises virtuele machine of fysieke VM. U kunt het volgende bewaken:

   - Systeem locatie

   - Heartbeat-status en latentie

   - Beschikbaar geheugen en schijf ruimte

   - Agent bewerkingen

    In deze sectie moet u het tabblad selecteren dat de omgeving van uw computers beschrijft: Kies het tabblad door **Azure beheerde computers** als u alleen de door Azure Arc beheerde computers wilt weer geven. Kies het tabblad **alle computers** om zowel beheerde als niet-Azure-computers weer te geven waarop de log Analytics-agent is geïnstalleerd.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="informatie pagina gegevens connector status controle werkmap" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [voorbereiden van uw gegevens naar Azure Sentinel](quickstart-onboard.md), [verbinding maken met gegevens bronnen](connect-data-sources.md)en [inzicht krijgen in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).