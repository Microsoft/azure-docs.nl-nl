---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652599"
---
De volgende tabel geeft een lijst van de JMS-functies (Java Message Service) die momenteel door Azure Service Bus worden ondersteund. Er worden ook functies weer gegeven die niet worden ondersteund.


| Functie | API |Status |
|---|---|---|
| Wachtrijen   | <ul> <li> JMSContext. createQueue (String queuenaam) </li> </ul>| **Ondersteund** |
| Onderwerpen   | <ul> <li> JMSContext. createTopic (String topic) </li> </ul>| **Ondersteund** |
| Tijdelijke wacht rijen |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Ondersteund** |
| Tijdelijke onderwerpen |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Ondersteund** |
| Bericht producer/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Ondersteund** |
| Wachtrij browsers |<ul> <li> JMSContext. createBrowser (wachtrij wachtrij) </li> <li> JMSContext. createBrowser (Queue Queue, String messageSelector) </li> </ul> | **Ondersteund** |
| Bericht verbruiker/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (doel bestemming) </li> <li> JMSContext. createConsumer (doel bestemming, teken reeks messageSelector) </li> <li> JMSContext. createConsumer (doel bestemming, teken reeks messageSelector, Booleaans-local)</li> </ul>  <br/> Lokaal wordt momenteel niet ondersteund | **Ondersteund** |
| Gedeelde duurzame abonnementen | <ul> <li> JMSContext. createSharedDurableConsumer (onderwerp, teken reeks naam) </li> <li> JMSContext. createSharedDurableConsumer (onderwerp, teken reeks naam, teken reeks messageSelector) </li> </ul>| **Ondersteund**|
| Niet-gedeelde duurzame abonnementen | <ul> <li> JMSContext. createDurableConsumer (onderwerp, teken reeks naam) </li> <li> createDurableConsumer (onderwerp, teken reeks naam, teken reeks messageSelector, Booleaans-local) </li> </ul> <br/> Lokaal wordt op dit moment niet ondersteund en moet worden ingesteld op ONWAAR | **Ondersteund** |
| Gedeelde niet-duurzame abonnementen |<ul> <li> JMSContext. createSharedConsumer (onderwerp, teken reeks sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (topic, String sharedSubscriptionName, String messageSelector) </li> </ul> | **Ondersteund** |
| Niet-verdeelde niet-duurzame abonnementen |<ul> <li> JMSContext. createConsumer (doel bestemming) </li> <li> JMSContext. createConsumer (doel bestemming, teken reeks messageSelector) </li> <li> JMSContext. createConsumer (doel bestemming, teken reeks messageSelector, Booleaans-local) </li> </ul> <br/> Lokaal wordt op dit moment niet ondersteund en moet worden ingesteld op ONWAAR | **Ondersteund** |
| Bericht kiezer | is afhankelijk van de gebruiker die is gemaakt | **Ondersteund** |
| Leverings vertraging (geplande berichten) | <ul> <li> JMSProducer. setDeliveryDelay (lange deliveryDelay) </li> </ul>|**Ondersteund**|
| Bericht gemaakt |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (serialiseerbaar object) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (teken reeks tekst) </li> </ul>| **Ondersteund** |
| Cross-entiteits transacties |<ul> <li> Connection. createSession (waar, Session.SESSION_TRANSACTED) </li> </ul> | **Ondersteund** |
| Gedistribueerde transacties || Niet ondersteund |
