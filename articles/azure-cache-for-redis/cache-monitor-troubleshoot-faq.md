---
title: Azure-cache voor redis-controle en veelgestelde vragen over problemen oplossen
description: Meer informatie over de antwoorden op veelgestelde vragen over het bewaken en oplossen van problemen met Azure cache voor redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537521"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure-cache voor redis-controle en veelgestelde vragen over problemen oplossen
In dit artikel vindt u antwoorden op veelgestelde vragen over het bewaken en oplossen van problemen met Azure-cache voor redis.

## <a name="common-questions-and-answers"></a>Veelgestelde vragen en antwoorden
In deze sectie worden de volgende veelgestelde vragen behandeld:

* [Hoe kan ik de status en prestaties van mijn cache controleren?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Waarom kan ik time-outs bekijken?](#why-am-i-seeing-timeouts)
* [Waarom is de verbinding tussen mijn client en de cache verbroken?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hoe kan ik de status en prestaties van mijn cache controleren?
Microsoft Azure cache voor redis-instanties kunnen worden bewaakt in de [Azure Portal](https://portal.azure.com). U kunt metrische gegevens weer geven, metrische grafieken vastmaken aan het start Board, het datum-en tijds bereik van bewakings grafieken aanpassen, metrische gegevens aan de grafieken toevoegen en verwijderen, en waarschuwingen instellen wanneer aan bepaalde voor waarden wordt voldaan. Zie [Azure-cache bewaken voor redis](cache-how-to-monitor.md)voor meer informatie.

Het menu Azure cache for redis **resource** bevat ook verschillende hulpprogram ma's voor het controleren en oplossen van problemen met uw caches.

* **Problemen vaststellen en oplossen** biedt informatie over veelvoorkomende problemen en strategieën voor het oplossen ervan.
* De **resource status** houdt uw resource in de gaten en vertelt u of deze wordt uitgevoerd zoals verwacht. Zie [overzicht van Azure resource Health](../service-health/resource-health-overview.md)voor meer informatie over de Azure resource Health-Service.
* **Nieuwe ondersteunings aanvraag** biedt opties voor het openen van een ondersteunings aanvraag voor uw cache.

Met deze hulpprogram ma's kunt u de status van uw Azure-cache bewaken voor redis-instanties en helpt u bij het beheren van uw cache toepassingen. Zie voor meer informatie de sectie ' ondersteuning voor het oplossen van problemen &-instellingen ' van het [configureren van Azure cache voor redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Waarom kan ik time-outs bekijken?
Time-outs doen zich voor op de client die u gebruikt om te praten met redis. Wanneer een opdracht wordt verzonden naar de redis-server, wordt de opdracht in de wachtrij geplaatst en wordt de redis-server uiteindelijk de opdracht verzameld en wordt deze uitgevoerd. De client kan echter een time-out hebben tijdens dit proces en als er een uitzonde ring optreedt op de aanroepende zijde. Zie [problemen met](cache-troubleshoot-client.md) de time-out op de client en [stack Exchange. redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)voor meer informatie over het oplossen van problemen met time-outs.

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Waarom is de verbinding tussen mijn client en de cache verbroken?
Hier volgen enkele veelvoorkomende redenen voor het verbreken van een cache.

* Oorzaken van de client
  * De client toepassing is opnieuw geïmplementeerd.
  * De client toepassing heeft een schaal bewerking uitgevoerd.
    * In het geval van Cloud Services of Web Apps kan dit worden veroorzaakt door automatisch schalen.
  * De laag van het netwerk aan de client zijde is gewijzigd.
  * Er zijn tijdelijke fouten opgetreden in de client of in de netwerk knooppunten tussen de client en de server.
  * De drempel waarden voor de band breedte zijn bereikt.
  * Het duurt te lang om aan de CPU gebonden bewerkingen te volt ooien.
* Oorzaken die zich aan de server zijde bevindt
  * Op de standaard cache aanbieding heeft de service Azure cache for redis een failover geïnitieerd van het primaire knoop punt naar het replica knooppunt.
  * Het exemplaar waarop de cache is geïmplementeerd, is door Azure bijgewerkt
    * Dit kan zijn voor redis-server updates of algemeen VM-onderhoud.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het controleren en oplossen van problemen met uw Azure-cache voor redis-instanties [Azure cache bewaken voor redis](cache-how-to-monitor.md) en de verschillende richt lijnen voor probleem oplossing.

Meer informatie over de [Veelgestelde vragen over andere Azure-caches voor redis](cache-faq.md).