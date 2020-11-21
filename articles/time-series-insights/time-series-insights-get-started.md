---
title: Een omgeving maken-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal om een nieuwe Azure Time Series Insights omgeving te maken.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023323"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Maak een nieuwe Azure Time Series Insights gen1-omgeving in de Azure Portal

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt beschreven hoe u een nieuwe Azure Time Series Insights-omgeving maakt met behulp van de Azure Portal.

Met Azure Time Series Insights kunt u aan de slag gaan met het visualiseren en opvragen van gegevens die in azure IoT hubs en Event Hubs in enkele minuten worden gestroomd, zodat u in een paar seconden een query kunt uitvoeren op grote volumes van tijdreeks gegevens.  Het is ontworpen voor de schaal van het Internet der dingen (IoT) en kan terabytes aan gegevens verwerken.

## <a name="steps-to-create-the-environment"></a>Stappen voor het maken van de omgeving

Volg deze stappen om een omgeving te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de knop **+ Een resource maken**.

1. Selecteer de categorie **Internet of Things** en selecteer **Time Series Insights**.

   [![De Azure Time Series Insights omgeving maken](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Selecteer op de pagina **Time Series Insights** **maken**.

1. Vul de vereiste para meters in. De volgende tabel bevat uitleg over elke para meter:

   [![De Azure Time Series Insights resource groep maken](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Instelling|Voorgestelde waarde|Beschrijving
   ---|---|---
   Omgevingsnaam | Een unieke naam | Deze naam vertegenwoordigt de omgeving in [Time Series Explorer](https://insights.timeseries.azure.com)
   Abonnement | Uw abonnement | Als u meerdere abonnementen hebt, kiest u het abonnement dat uw gebeurtenis bron bevat bij voor keur. Azure Time Series Insights kunt automatisch Azure IoT Hub-en Event hub-resources detecteren die in hetzelfde abonnement aanwezig zijn.
   Resourcegroep | Nieuwe maken of bestaande gebruiken | Een resourcegroep is een verzameling Azure-resources die samen worden gebruikt. U kunt een bestaande resource groep kiezen, bijvoorbeeld het item met uw event hub of IoT Hub. Of u kunt een nieuw item maken als deze bron niet is gerelateerd aan de andere resources.
   Locatie | Dichtstbijzijnde bron van uw gebeurtenis | Kies bij voor keur dezelfde Data Center-locatie die uw gebeurtenis brongegevens bevat, om te voor komen dat extra regio's en bandbreedte kosten voor meerdere zones worden toegevoegd en latentie wordt toegevoegd bij het verplaatsen van gegevens uit de regio.
   Prijscategorie | S1 | Kies de benodigde doorvoer. Voor de laagste kosten en de start capaciteit selecteert u S1.
   Capaciteit | 1 | Capaciteit is de vermenigvuldiger is van toepassing op het ingangs tempo, de opslag capaciteit en de kosten die zijn gekoppeld aan de geselecteerde SKU.  U kunt de capaciteit van een omgeving wijzigen nadat deze is gemaakt. Selecteer een capaciteit van 1 voor de laagste kosten.
  
1. Selecteer **maken** om het inrichtings proces te starten. Dit kan een paar minuten duren.

1. Als u het implementatie proces wilt bewaken, selecteert u het **meldings** symbool (pictogram Bell).

   [![De meldingen bekijken](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Controleer de configuratie-instellingen van uw implementatie in het **overzicht** van bronnen.

   [![De Azure Time Series Insights pincode voor het dash board maken](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Optioneel)** Selecteer het **speld pictogram** in de rechter bovenhoek om in de toekomst gemakkelijk toegang te krijgen tot uw Azure time series Insights omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Definieer het beleid voor gegevens toegang](./concepts-access-policies.md) om uw omgeving te beveiligen.

* [Een event hub-gebeurtenis bron toevoegen](./how-to-ingest-data-event-hub.md) aan uw Azure time series Insights-omgeving.

* [Gebeurtenissen verzenden](time-series-insights-send-events.md) naar de bron van de gebeurtenis.

* Bekijk uw omgeving in [Azure time series Insights Explorer](https://insights.timeseries.azure.com).