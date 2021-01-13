---
title: Metrische gegevens in Azure Monitor-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het gebruik van Azure monitoring voor het bewaken van Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 74830775a4f31e6f8e486b4d6cc434335b4ee723
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165889"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metrische gegevens van Azure Event Hubs in Azure Monitor

Event Hubs metrische gegevens geven u de status van Event Hubs resources in uw Azure-abonnement. Met een uitgebreide set met metrische gegevens kunt u de algemene status van uw event hubs niet alleen op het niveau van de naam ruimte beoordelen, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van uw event hubs bewaken. Metrische gegevens kunnen ook helpen bij het oplossen van problemen met de hoofd oorzaak zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Azure Monitor biedt uniforme gebruikers interfaces voor bewaking in verschillende Azure-Services. Zie voor meer informatie [bewaking in Microsoft Azure](../azure-monitor/overview.md) en het voor beeld [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op github.

## <a name="access-metrics"></a>Toegangs gegevens

Azure Monitor biedt meerdere manieren om toegang te krijgen tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen, zoals Log Analytics en Event hubs. Zie [bewaking van gegevens die zijn verzameld door Azure monitor](../azure-monitor/platform/data-platform.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld, en u kunt toegang krijgen tot de meest recente 30 dagen. Als u gegevens gedurende een langere periode wilt bewaren, kunt u metrische gegevens archiveren naar een Azure Storage-account. Deze instelling kan worden geconfigureerd in [Diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in azure monitor.


## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u geslaagde aanvragen en inkomende aanvragen kunt weer geven op account niveau:

![Geslaagde metrische gegevens weer geven][1]

U kunt ook rechtstreeks toegang krijgen tot metrische gegevens via de naam ruimte. Hiertoe selecteert u uw naam ruimte en selecteert u vervolgens **metrische gegevens**. Als u de metrische gegevens wilt weer geven die zijn gefilterd op het bereik van de Event Hub, selecteert u de Event Hub en selecteert u vervolgens **metrische gegevens**.

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde, zoals weer gegeven in het volgende voor beeld:

![Filteren met dimensie waarde][2]

## <a name="billing"></a>Billing

Het gebruik van metrische gegevens in Azure Monitor is momenteel gratis. Als u echter andere oplossingen gebruikt waarmee metrische gegevens worden opgenomen, worden deze oplossingen mogelijk gefactureerd. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Azure als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

Met de volgende metrische gegevens krijgt u een overzicht van de status van uw service. 

> [!NOTE]
> Er worden verschillende metrische gegevens overgezet omdat deze onder een andere naam worden geplaatst. Hiervoor moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutel woord ' afgeschaft ' worden niet verder ondersteund.

Alle waarden voor metrische gegevens worden elke minuut naar Azure Monitor verzonden. De granulatie tijd definieert het tijds interval waarvoor metrische waarden worden weer gegeven. Het ondersteunde tijds interval voor alle Event Hubs meet waarden is 1 minuut.

## <a name="azure-event-hubs-metrics"></a>Metrische gegevens van Azure Event Hubs
Zie [Azure Event hubs](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces) voor een lijst met metrische gegevens die door de service worden ondersteund.

> [!NOTE]
> Als er een gebruikers fout optreedt, wordt de metrische gegevens van de **gebruikers fouten** door Azure Event hubs bijgewerkt, maar worden er geen andere diagnostische informatie in het logboek geregistreerd. Daarom moet u details vastleggen over gebruikers fouten in uw toepassingen. U kunt ook de telemetrie converteren die wordt gegenereerd wanneer berichten worden verzonden of ontvangen in Application Insights. Zie [bijhouden met Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights)voor een voor beeld.

## <a name="azure-monitor-integration-with-siem-tools"></a>Integratie met SIEM-hulpprogram ma's Azure Monitor
Uw bewakings gegevens (activiteiten logboeken, Diagnostische logboeken, enzovoort) door sturen naar een Event Hub met Azure Monitor kunt u eenvoudig integreren met Security Information and Event Management-hulpprogram ma's (SIEM). Raadpleeg de volgende artikelen/blog berichten voor meer informatie:

- [Azure-bewakings gegevens streamen naar een Event Hub voor gebruik door een extern hulp programma](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Inleiding tot Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Azure Monitor gebruiken om te integreren met SIEM-hulpprogramma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

In het scenario waarin een SIEM-hulp programma logboek gegevens van een Event Hub verbruikt, als u geen inkomende berichten ziet of als u inkomende berichten ziet, maar geen uitgaande berichten in de grafiek metrische gegevens, volgt u deze stappen:

- Als er **geen inkomende berichten** zijn, betekent dit dat de Azure Monitor-service geen controle-en Diagnostische logboeken naar de Event hub verplaatst. In dit scenario opent u een ondersteunings ticket met het Azure Monitor team. 
- Als er inkomende berichten zijn, maar **geen uitgaande berichten**, betekent dit dat de Siem-toepassing de berichten niet leest. Neem contact op met de SIEM-provider om te bepalen of de configuratie van de Event Hub deze toepassingen juist is.


## <a name="next-steps"></a>Volgende stappen

* Zie het [overzicht van Azure-bewaking](../azure-monitor/overview.md).
* [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -voor beeld op github. 

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met een Event Hubs-zelfstudie
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
