---
title: Inrichten en beheren van een bedrijfs omgeving van 2 Azure time series | Microsoft Docs
description: Meer informatie over het inrichten en beheren van een Azure Time Series Insights in de bedrijfs wereld 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 7c38c57a8480ef2addde494b94d70bd2eb679373
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016765"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 inrichten en beheren

In dit artikel wordt beschreven hoe u een Azure Time Series Insights Gen2-omgeving maakt en beheert met behulp van de [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Overzicht

Wanneer u een Azure Time Series Insights Gen2-omgeving inricht, maakt u deze Azure-resources:

* Een Azure Time Series Insights Gen2-omgeving die voldoet aan het prijs model voor betalen per gebruik
* Een Azure Storage-account
* Een optionele warme Store voor snellere en onbeperkte query's

> [!TIP]
>
> * Meer informatie [over het plannen van uw omgeving](./how-to-plan-your-environment.md).
> * Meer informatie over het [toevoegen van een event hub bron](./how-to-ingest-data-event-hub.md) of het [toevoegen van een IOT hub-bron](./how-to-ingest-data-iot-hub.md).

U leert het volgende:

1. Koppel elke Azure Time Series Insights-omgeving van de wereld 2 aan een gebeurtenis bron. U geeft ook een time stamp-ID-eigenschap en een unieke consumenten groep op om ervoor te zorgen dat de omgeving toegang heeft tot de juiste gebeurtenissen.

1. Nadat het inrichten is voltooid, kunt u uw toegangs beleid en andere omgevings kenmerken aanpassen aan de behoeften van uw bedrijf.

   > [!NOTE]
   > De eerste stap is optioneel bij het inrichten van een omgeving. Als u deze stap overs laat, moet u later een gebeurtenis bron aan de omgeving koppelen, zodat de gegevens in uw omgeving kunnen worden gestroomd en toegankelijk zijn via query's.

## <a name="create-the-environment"></a>De omgeving maken

Een Azure Time Series Insights-omgeving met 2 maken:

1. Maak een Azure Time Series Insights resource onder *Internet of Things* in [Azure Portal](https://portal.azure.com/).

1. Selecteer **Gen2 (L1)** als **laag**. Geef een omgevings naam op en kies de abonnements groep en de resource groep die u wilt gebruiken. Selecteer vervolgens een ondersteunde locatie voor het hosten van de omgeving.

   [![Maak een Azure Time Series Insights-exemplaar.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Voer een tijd reeks-ID in.

    > [!NOTE]
    >
    > * De tijd reeks-ID is *hoofdletter gevoelig* en *onveranderbaar*. (Deze kan niet worden gewijzigd nadat deze is ingesteld.)
    > * Time Series-Id's kunnen Maxi maal *drie* sleutels hebben. U kunt het beschouwen als een primaire sleutel in een Data Base, die een unieke aanduiding vormt van elke sensor die gegevens naar uw omgeving verzendt. Dit kan één eigenschap zijn of een combi natie van Maxi maal drie eigenschappen.
    > * Meer informatie over [het kiezen van een tijd reeks-id](./how-to-select-tsid.md)

1. Maak een Azure Storage-account door de naam van een opslag account, het soort account en een gewenste [replicatie](../storage/common/redundancy-migration.md?tabs=portal) optie op te wijzen. Als u dit doet, wordt er automatisch een Azure Storage-account gemaakt. Standaard wordt het v2-account voor [algemeen gebruik](../storage/common/storage-account-overview.md) gemaakt. Het account wordt gemaakt in dezelfde regio als de Azure Time Series Insights Gen2-omgeving die u eerder hebt geselecteerd.
U kunt ook uw eigen opslag (BYOS) met een [arm-sjabloon](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) meenemen wanneer u een nieuwe Azure time series Gen2-omgeving maakt.

1. **(Optioneel)** Schakel warme Store in voor uw omgeving als u snellere en onbeperkte query's wilt uitvoeren voor de meeste recente gegevens in uw omgeving. U kunt ook een warme archief maken of verwijderen via de optie **opslag configuratie** in het linkernavigatievenster, nadat u een Azure time series Insights Gen2-omgeving hebt gemaakt.

1. **(Optioneel)** U kunt nu een gebeurtenis bron toevoegen. U kunt ook wachten totdat het exemplaar is ingericht.

   * Azure Time Series Insights ondersteunt [azure IOT hub](./how-to-ingest-data-iot-hub.md) en [Azure Event hubs](./how-to-ingest-data-event-hub.md) als opties voor gebeurtenis bronnen. Hoewel u slechts één gebeurtenis bron kunt toevoegen wanneer u de omgeving maakt, kunt u later nog een gebeurtenis bron toevoegen.

     U kunt een bestaande consumenten groep selecteren of een nieuwe Consumer groep maken wanneer u de bron van de gebeurtenis toevoegt. Houd er rekening mee dat de gebeurtenis bron een unieke consumenten groep voor uw omgeving nodig heeft om gegevens te lezen.

   * Kies de juiste tijds tempel eigenschap. Azure Time Series Insights gebruikt standaard de time-out voor berichten in de wachtrij voor elke bron van de gebeurtenis.

     > [!TIP]
     > De time-outtijd van het bericht is mogelijk niet de beste geconfigureerde instelling voor gebruik in batch-gebeurtenis scenario's of scenario's voor het uploaden van historische gegevens. In dergelijke gevallen moet u controleren of u de beslissing hebt genomen of geen tijds tempel eigenschap wilt gebruiken.

     [![Configuratie tabblad gebeurtenis bron](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Controleer of uw omgeving is ingericht en op de gewenste manier is geconfigureerd.

    [![Tabblad controleren en maken](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>De omgeving beheren

U kunt uw Azure Time Series Insights Gen2-omgeving beheren door gebruik te maken van de Azure Portal. Er zijn enkele belang rijke verschillen tussen een Gen2-omgeving en gen1 S1-of gen1 S2-omgevingen waar u rekening mee moet doen wanneer u uw omgeving beheert via de Azure Portal:

* De Blade **overzicht**  van Azure Portal Gen2 bevat de volgende wijzigingen:

  * De capaciteit wordt verwijderd omdat deze niet van toepassing is op Gen2-omgevingen.
  * De eigenschap **Time Series id** wordt toegevoegd. Hiermee wordt bepaald hoe uw gegevens worden gepartitioneerd.
  * Verwijzings gegevens sets worden verwijderd.
  * Met de weer gegeven URL wordt u omgeleid naar de [Azure time series Insights Explorer](./concepts-ux-panels.md).
  * De naam van uw Azure Storage-account wordt vermeld.

* De Blade **configureren** van de Azure portal wordt verwijderd omdat schaal eenheden niet van toepassing zijn op Azure time series Insights Gen2-omgevingen. U kunt echter **opslag configuratie** gebruiken om de zojuist geïntroduceerde warme Store te configureren.

* De Blade **referentie gegevens** van de Azure portal wordt verwijderd in azure time series Insights Gen2 omdat referentie gegevens concept is vervangen door [TSM (Time Series model)](./concepts-model-overview.md).

[![Azure Time Series Insights Gen2-omgeving in de Azure Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Time Series Insights algemeen beschik bare omgevingen en Gen2 omgevingen door [uw omgeving](./how-to-plan-your-environment.md)te lezen.

* Meer informatie over het [toevoegen van een event hub bron](./how-to-ingest-data-event-hub.md).

* Een [IOT hub-bron](./how-to-ingest-data-iot-hub.md)configureren.