---
title: Een Gen2-omgeving instellen met behulp van de Azure Portal-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over het instellen van een omgeving in Azure Time Series Insights Gen2 met behulp van Azure Portal.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952844"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Een Azure Time Series Insights Gen2-omgeving maken met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een Azure Time Series Insights Gen2-omgeving maakt met behulp van de [Azure Portal](https://portal.azure.com/).

De zelf studie voor het inrichten van de omgeving begeleidt u stapsgewijs door het proces. Meer informatie over het selecteren van de juiste tijd reeks-ID en het weer geven van voor beelden uit twee JSON-nettoladingen.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

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

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Maak een Azure Time Series Insights-exemplaar." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Voer een tijd reeks-ID in.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Een configuratie van een Azure Time Series Insights omgeving maken, vervolg." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * De tijd reeks-ID is *hoofdletter gevoelig* en *onveranderbaar*. (Deze kan niet worden gewijzigd nadat deze is ingesteld.)
   > * Time Series-Id's kunnen Maxi maal *drie* sleutels hebben. U kunt het beschouwen als een primaire sleutel in een Data Base, die een unieke aanduiding vormt van elke sensor die gegevens naar uw omgeving verzendt. Dit kan één eigenschap of een combi natie van Maxi maal drie eigenschappen zijn.
   > * Meer informatie over [het kiezen van een tijd reeks-id](./how-to-select-tsid.md)

1. Maak een Azure Storage-account door de naam van het opslag account en het soort account te selecteren en een [replicatie](../storage/common/redundancy-migration.md?tabs=portal) keuze aan te wijzen. Als u dit doet, wordt er automatisch een Azure Storage-account gemaakt. Standaard wordt het v2-account voor [algemeen gebruik](../storage/common/storage-account-overview.md) gemaakt. Het account wordt gemaakt in dezelfde regio als de Azure Time Series Insights Gen2-omgeving die u eerder hebt geselecteerd.
U kunt ook uw eigen opslag (BYOS) meenemen via een [Azure Resource Manager sjabloon](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) wanneer u een nieuwe Azure time series Gen2-omgeving maakt.

1. **(Optioneel)** Schakel warme Store in voor uw omgeving als u snellere en onbeperkte query's wilt uitvoeren voor de meeste recente gegevens in uw omgeving. U kunt ook een warme archief maken of verwijderen via de optie **opslag configuratie** in het linkernavigatievenster, nadat u een Azure time series Insights Gen2-omgeving hebt gemaakt.

1. **(Optioneel)** U kunt nu een gebeurtenis bron toevoegen. U kunt ook wachten totdat het exemplaar is ingericht.

   * Azure Time Series Insights ondersteunt [azure IOT hub](./how-to-ingest-data-iot-hub.md) en [Azure Event hubs](./how-to-ingest-data-event-hub.md) als opties voor gebeurtenis bronnen. Hoewel u slechts één gebeurtenis bron kunt toevoegen wanneer u de omgeving maakt, kunt u later nog een gebeurtenis bron toevoegen.

     U kunt een bestaande consumenten groep selecteren of een nieuwe Consumer groep maken wanneer u de bron van de gebeurtenis toevoegt. Zorg ervoor dat de gebeurtenis bron een unieke consumenten groep voor uw omgeving gebruikt om gegevens te lezen.

   * Kies de juiste tijds tempel eigenschap. Azure Time Series Insights gebruikt standaard de time-out voor berichten in de wachtrij voor elke bron van de gebeurtenis.

     > [!TIP]
     > De time-outtijd van het bericht is mogelijk niet de beste geconfigureerde instelling voor gebruik in batch-gebeurtenis scenario's of scenario's voor het uploaden van historische gegevens. In dergelijke gevallen moet u controleren of u de beslissing hebt genomen of geen tijds tempel eigenschap wilt gebruiken.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Configuratie tabblad gebeurtenis bron" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Selecteer **controleren + maken** om te bevestigen dat uw omgeving is ingericht en geconfigureerd op de manier die u wilt.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Tabblad controleren en maken" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Time Series Insights algemeen beschik bare omgevingen en Gen2 omgevingen door [uw omgeving](./how-to-plan-your-environment.md)te lezen.
* Meer informatie over [streaming-opname gebeurtenis bronnen](./concepts-streaming-ingestion-event-sources.md) voor uw Azure time series Insights Gen2-omgeving.
* Meer informatie over het [beheren van uw omgeving](./how-to-provision-manage.md).
