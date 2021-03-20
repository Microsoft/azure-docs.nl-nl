---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83779943"
---
U kunt ook de metrische gegevens weer geven om de prestaties van het apparaat te controleren en in sommige gevallen voor het oplossen van problemen met apparaten.

Voer de volgende stappen uit in de Azure Portal om een grafiek te maken voor de geselecteerde metrische gegevens van het apparaat.

1. Ga voor uw resource in het Azure Portal naar **bewaking > metrische gegevens** en selecteer **metrische gegevens toevoegen**.

    ![Waarde toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. De resource wordt automatisch ingevuld.  

    ![Huidige resource](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Als u een andere resource wilt opgeven, selecteert u de resource. Selecteer op de Blade **een resource selecteren** het abonnement, de resource groep, het resource type en de specifieke resource waarvoor u de metrische gegevens wilt weer geven en selecteer **Toep assen**.

    ![Een andere resource kiezen](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Selecteer in de vervolg keuzelijst een metriek om uw apparaat te bewaken. Zie [metrische gegevens op uw apparaat](#metrics-on-your-device)voor een volledige lijst met deze metrische gegevens.

4. Wanneer een metriek is geselecteerd in de vervolg keuzelijst, kan aggregatie ook worden gedefinieerd. Aggregatie verwijst naar de werkelijke waarde die is geaggregeerd over een bepaalde periode. De geaggregeerde waarden kunnen het gemiddelde, het minimum of de maximum waarde zijn. Selecteer de aggregatie van AVG, Max of min.

    ![Grafiek weer geven](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Als de metrische gegevens die u hebt geselecteerd meerdere exemplaren hebben, is de optie splitsen beschikbaar. Selecteer **splitsing Toep assen** en selecteer vervolgens de waarde die u wilt weer geven van de uitsplitsing.

    ![Splitsing Toep assen](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Als u de uitsplitsing nu alleen voor enkele exemplaren wilt zien, kunt u de gegevens filteren. Als u bijvoorbeeld de netwerk doorvoer alleen wilt bekijken voor de twee verbonden netwerk interfaces op uw apparaat, kunt u die interfaces filteren. Selecteer **filter toevoegen** en geef de naam van de netwerk interface op voor filteren.

    ![Filter toevoegen](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. U kunt de grafiek ook aan het dash board vastmaken voor eenvoudige toegang.

    ![Vastmaken aan dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Als u grafiek gegevens wilt exporteren naar een Excel-spread sheet of een koppeling naar de grafiek wilt ontvangen die u kunt delen, selecteert u de optie delen in de opdracht balk.

    ![Gegevens exporteren](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
