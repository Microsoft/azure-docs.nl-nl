---
title: Azure Maps metrische gegevens over het gebruik van de API weer geven | Microsoft Azure kaarten
description: Meer informatie over het weer geven van metrische gegevens over Azure Maps-API-gebruik, zoals totaal aantal aanvragen, totaal aantal fouten en beschik baarheid. Zie gegevens filteren en resultaten splitsen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003511"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Metrische gegevens over het gebruik van Azure Maps-API weergeven

In dit artikel wordt beschreven hoe u de metrische gegevens over het gebruik van de API voor uw Azure Maps-account in de [Azure Portal](https://portal.azure.com)kunt weer geven. De metrische gegevens worden in een handige grafiek indeling weer gegeven, samen met een aanpas bare tijds duur.

## <a name="view-metric-snapshot"></a>Moment opname van metrische gegevens weer geven

Op de pagina **overzicht** van uw Maps-account ziet u enkele algemene metrische gegevens. Het bevat momenteel *Totaal aantal aanvragen*, *Totaal aantal fouten* en *Beschik baarheid* gedurende een Selecteer bare tijds duur.

![Overzicht van metrische gegevens over Azure Maps gebruik](media/how-to-view-api-usage/portal-overview.png)

Ga door naar de volgende sectie als u deze grafieken voor uw specifieke analyse wilt aanpassen.

## <a name="view-detailed-metrics"></a>Gedetailleerde metrische gegevens weer geven

1. Meld u aan bij uw Azure-abonnement in de [Portal](https://portal.azure.com).

2. Klik op het menu-item **alle resources** aan de linkerkant en navigeer naar uw *Azure Maps-account*.

3. Zodra uw Maps-account is geopend, klikt u op het menu **metrische gegevens** aan de linkerkant.

4. Kies een van de volgende opties in het deel venster **metrische gegevens** :

   1. **Beschik baarheid** : Hiermee wordt het *gemiddelde* van de API-Beschik baarheid gedurende een bepaalde tijd weer gegeven.
   2. **Gebruik** : hier wordt weer gegeven hoe het *aantal items* voor uw account wordt gebruikt.

      ![Deel venster metrische gegevens over Azure Maps gebruik](media/how-to-view-api-usage/portal-metrics.png)

5. Daarna kunt u het *tijds bereik* selecteren door te klikken op de **laatste 24 uur (automatisch)**. Het tijds bereik is standaard ingesteld op 24 uur. Nadat u op hebt geklikt, ziet u alle geselecteerde Peri Oden. U kunt de *tijd granulatie* selecteren en ervoor kiezen om de tijd weer te geven als *lokaal* of *GMT* in dezelfde vervolg keuzelijst. Klik op **Toepassen**.

    ![Tijds bereik voor Azure Maps meet waarden](media/how-to-view-api-usage/time-range.png)

6. Zodra u uw metrische gegevens hebt toegevoegd, kunt u een **filter toevoegen** uit de eigenschappen die relevant zijn voor die metriek. Selecteer vervolgens de waarde van de eigenschap die u wilt weer geven in de grafiek.

    ![Filter voor metrische gegevens over Azure Maps gebruik](media/how-to-view-api-usage/filter.png)

7. U kunt ook **splitsen Toep assen** op uw metrische gegevens op basis van de geselecteerde metrische eigenschap. Hiermee kan de grafiek worden opgesplitst in meerdere grafieken, voor elke waarde van die eigenschap. In de volgende afbeelding komt de kleur van elke grafiek overeen met de eigenschaps waarde die onder aan de grafiek wordt weer gegeven.

    ![Splitsing van metrische gegevens over Azure Maps gebruik](media/how-to-view-api-usage/splitting.png)

8. U kunt ook meerdere metrische gegevens in dezelfde grafiek bekijken door simpelweg op de knop **metriek toevoegen** bovenaan te klikken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Api's waarvoor u het gebruik wilt bijhouden:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK-instructies](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps van de Android SDK](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentatie voor Azure Maps REST API](/rest/api/maps)