---
title: Een Event Hubs gebeurtenis bron toevoegen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het toevoegen van een Azure Event Hubs gebeurtenis bron aan uw Azure Time Series Insights omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461131"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Een Event Hub gebeurtenis bron toevoegen aan uw Azure Time Series Insights omgeving

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om een gebeurtenis bron toe te voegen waarmee gegevens worden gelezen van Azure Event Hubs naar uw Azure Time Series Insights omgeving.

> [!NOTE]
> De stappen die in dit artikel worden beschreven, zijn van toepassing op de Azure Time Series Insights gen 1 en Azure Time Series Insights gen 2-omgevingen.

## <a name="prerequisites"></a>Vereisten

- Een Azure Time Series Insights omgeving maken zoals beschreven in [een Azure time series Insights omgeving maken](./tutorial-set-up-environment.md).
- Maak een Event Hub. Lees [een event hubs naam ruimte en een event hub maken met behulp van de Azure Portal](../event-hubs/event-hubs-create.md).
- Er moeten actieve bericht gebeurtenissen naar het Event Hub worden verzonden. Meer informatie over [het verzenden van gebeurtenissen naar Azure Event hubs met behulp van de .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Maak een speciale Consumer groep in de Event Hub waarvan de Azure Time Series Insights omgeving kan worden gebruikt. Elke Azure Time Series Insights gebeurtenis bron moet een eigen toegewezen consumenten groep hebben die niet met een andere consument wordt gedeeld. Als meerdere lezers gebeurtenissen van dezelfde consumenten groep gebruiken, kunnen alle lezers waarschijnlijk fouten vertonen. Er is een limiet van 20 consumenten groepen per Event Hub. Lees de [Event hubs-programmeer gids](../event-hubs/event-hubs-programming-guide.md)voor meer informatie.

### <a name="add-a-consumer-group-to-your-event-hub"></a>Een Consumer groep toevoegen aan uw Event Hub

Toepassingen gebruiken consumenten groepen voor het ophalen van gegevens uit Azure Event Hubs. Als u gegevens op een betrouw bare manier van uw Event Hub wilt lezen, geeft u een speciale consumenten groep op die alleen door deze Azure Time Series Insights omgeving wordt gebruikt.

Een nieuwe consumenten groep toevoegen aan uw Event Hub:

1. Zoek en open in het [Azure Portal](https://portal.azure.com)het event hub-exemplaar in het deel venster **overzicht** van uw event hub naam ruimte. Selecteer **entiteiten > Event hubs** of Zoek uw instantie onder **naam**.

    [![De Event Hub naam ruimte openen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Selecteer in uw Event Hub-exemplaar **entiteiten > consumenten groepen**. Selecteer vervolgens **+ consumenten groep** om een nieuwe consumenten groep toe te voegen.

   [![Event hub-een Consumer groep toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Anders selecteert u een bestaande consumenten groep en gaat u naar de volgende sectie.

1. Voer op de pagina **consumenten groepen** een nieuwe unieke waarde in voor **naam**.  Gebruik dezelfde naam wanneer u een nieuwe gebeurtenis bron maakt in de Azure Time Series Insights omgeving.

1. Selecteer **Maken**.

## <a name="add-a-new-event-source"></a>Een nieuwe gebeurtenis bron toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek uw bestaande Azure Time Series Insights-omgeving. Selecteer in het menu links **alle resources** en selecteer vervolgens uw Azure time series Insights omgeving.

1. Selecteer **gebeurtenis bronnen** en selecteer vervolgens **toevoegen**.

   [![Selecteer onder gebeurtenis bronnen de knop toevoegen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Voer een waarde in voor de **gebeurtenis bron naam** die uniek is voor deze Azure time series Insights omgeving, zoals `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. Voor **bron** selecteert u **Event hub**.

1. Selecteer de juiste waarden voor de **optie importeren**:

   - Als u een bestaande Event Hub hebt in een van uw abonnementen, selecteert u **Event hub gebruiken uit beschik bare abonnementen**. Deze optie is de eenvoudigste benadering.

     [![Selecteer een optie voor het importeren van gebeurtenis bronnen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - In de volgende tabel worden de vereiste eigenschappen voor de optie **Event hub gebruiken van beschik bare abonnementen** beschreven:

       [![Details van abonnement en Event Hub](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnement | Het abonnement waartoe de gewenste Event Hub instantie en naam ruimte behoort. |
       | Event hub-naamruimte | De naam ruimte van de Event Hub waarvan de gewenste Event Hub instantie deel uitmaakt. |
       | Naam van de Event Hub | De naam van de gewenste Event Hub-instantie. |
       | Waarde van Event hub-beleid | Selecteer het gewenste beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang maken op het tabblad Event Hub **configureren** . Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Het gedeelde toegangs beleid voor uw gebeurtenis bron *moet* **Lees** machtigingen hebben. |
       | Event hub-beleids sleutel | Vooraf ingevuld op basis van de geselecteerde Event hub-beleids waarde. |

   - Als de Event Hub extern is voor uw abonnementen of als u geavanceerde opties wilt selecteren, selecteert u **Event hub-instellingen hand matig opgeven**.

       De volgende tabel beschrijft de vereiste eigenschappen voor de optie voor het **opgeven van Event hub-instellingen hand matig** :

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnements-id | Het abonnement waartoe de gewenste Event Hub instantie en naam ruimte behoort. |
       | Resourcegroep | De resource groep waarvan de gewenste Event Hub instantie en naam ruimte behoort. |
       | Event hub-naamruimte | De naam ruimte van de Event Hub waarvan de gewenste Event Hub instantie deel uitmaakt. |
       | Naam van de Event Hub | De naam van de gewenste Event Hub-instantie. |
       | Waarde van Event hub-beleid | Selecteer het gewenste beleid voor gedeelde toegang. U kunt het beleid voor gedeelde toegang maken op het tabblad Event Hub **configureren** . Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Het gedeelde toegangs beleid voor uw gebeurtenis bron *moet* **Lees** machtigingen hebben. |
       | Event hub-beleids sleutel | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Service Bus. Voer hier de primaire of secundaire sleutel in. |

   - Beide opties delen de volgende configuratie opties:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Event Hub-consumentengroep | De Consumer groep die gebeurtenissen van de Event Hub leest. We raden u ten zeerste aan om een speciale consumenten groep voor uw gebeurtenis bron te gebruiken. |
       | Serialisatie-indeling voor gebeurtenissen | Op dit moment is JSON de enige beschik bare serialisatie-indeling. Gebeurtenis berichten moeten de volgende indeling hebben of gegevens kunnen niet worden gelezen. |
       | Naam van de timestamp-eigenschap | Als u deze waarde wilt bepalen, moet u weten wat de bericht indeling is van de bericht gegevens die naar de Event Hub worden verzonden. Deze waarde is de **naam** van de specifieke gebeurtenis eigenschap in de bericht gegevens die u wilt gebruiken als de tijds tempel van de gebeurtenis. De waarde is hoofdletter gevoelig. Als dit veld leeg blijft, wordt de tijd voor het **plaatsen van gebeurtenissen** in de gebeurtenis bron gebruikt als tijds tempel van de gebeurtenis. |

1. Voeg de toegewezen Azure Time Series Insights naam van de Consumer groep toe die u hebt toegevoegd aan uw Event Hub.

1. Selecteer **Maken**.

   Nadat de gebeurtenis bron is gemaakt, begint Azure Time Series Insights automatisch gegevens stromen naar uw omgeving.

## <a name="next-steps"></a>Volgende stappen

- [Definieer gegevens toegangs beleid](./concepts-access-policies.md) voor het beveiligen van de gegevens.

- [Gebeurtenissen verzenden](time-series-insights-send-events.md) naar de bron van de gebeurtenis.

- Toegang tot uw omgeving in de [Azure time series Insights Explorer](https://insights.timeseries.azure.com).
