---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581931"
---
U kunt ook de metrische gegevens weer geven om de prestaties van het apparaat te controleren en in sommige gevallen voor het oplossen van problemen met apparaten.

Voer de volgende stappen uit in de Azure Portal om een grafiek te maken voor de geselecteerde metrische gegevens van het apparaat.

1. Ga voor uw resource in het Azure Portal naar **bewaking > metrische gegevens** en selecteer **metrische gegevens toevoegen**.

    ![Waarde toevoegen](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. De resource wordt automatisch ingevuld.  

    ![Huidige resource](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Als u een andere resource wilt opgeven, selecteert u de resource. Selecteer op de Blade **een resource selecteren** het abonnement, de resource groep, het resource type en de specifieke resource waarvoor u de metrische gegevens wilt weer geven en selecteer **Toep assen**.

    ![Een andere resource kiezen](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. Selecteer in de vervolg keuzelijst een metriek om uw apparaat te bewaken. Zie [metrische gegevens op uw apparaat](#metrics-on-your-device)voor een volledige lijst met deze metrische gegevens.

4. Wanneer een metriek is geselecteerd in de vervolg keuzelijst, kan aggregatie ook worden gedefinieerd. Aggregatie verwijst naar de werkelijke waarde die is geaggregeerd over een bepaalde periode. De geaggregeerde waarden kunnen het gemiddelde, het minimum of de maximum waarde zijn. Selecteer de aggregatie van AVG, Max of min.

    ![Grafiek weer geven](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Als de metrische gegevens die u hebt geselecteerd meerdere exemplaren hebben, is de optie splitsen beschikbaar. Selecteer **splitsing Toep assen** en selecteer vervolgens de waarde die u wilt weer geven van de uitsplitsing.

    ![Splitsing Toep assen](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Als u de uitsplitsing nu alleen voor enkele exemplaren wilt zien, kunt u de gegevens filteren. Als u bijvoorbeeld de netwerk doorvoer alleen wilt bekijken voor de twee verbonden netwerk interfaces op uw apparaat, kunt u die interfaces filteren. Selecteer **filter toevoegen** en geef de naam van de netwerk interface op voor filteren.

    ![Filter toevoegen](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. U kunt de grafiek ook aan het dash board vastmaken voor eenvoudige toegang.

    ![Vastmaken aan dashboard](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Als u grafiek gegevens wilt exporteren naar een Excel-spread sheet of een koppeling naar de grafiek wilt ontvangen die u kunt delen, selecteert u de optie delen in de opdracht balk.

    ![Gegevens exporteren](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
