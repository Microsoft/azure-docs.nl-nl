---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1b5905046fdde4d5574fd4d39f40dce1bbf11e32
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561522"
---
De volgende tabellen bevatten quota en limieten die specifiek zijn voor [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Zie [Prijzen voor Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) voor prijsinformatie over Event Hubs.

## <a name="common-limits-for-all-tiers"></a>Algemene limieten voor alle lagen
De volgende limieten gelden in alle lagen. 

| Limiet |  Notities | Waarde |
| --- |  --- | --- |
| Aantal Event Hubs-naamruimten per abonnement |- |100 |
| Aantal event hubs per naamruimte | Volgende aanvragen voor het maken van een nieuwe event hub worden geweigerd. |10 |
| Aantal partities per event hub |- |32 |
| Grootte van een Event Hub-naam |- | 256 tekens |
| Groote van de naam van een consumentengroep |- | 256 tekens |
| Aantal niet-epoche ontvangers per consumentengroep |- |5 |
| Aantal autorisatieregels per naamruimte | Volgende aanvragen voor het maken van een autorisatieregel worden geweigerd.|12 |
| Aantal aanroepen van de methode GetRuntimeInformation |  - | 50 per seconde | 
| Aantal virtuele netwerken (VNet) en IP-configuratieregels | - | 128 | 


## <a name="basic-vs-standard-tiers"></a>Basic- vs. Standard-lagen
De volgende tabel geeft de limieten weer die mogelijk anders zijn voor basic- en standard-lagen. 

| Limiet | Notities | Basic | Standard |
|---|---|--|---|
| Maximumgrootte van Event Hubs-gebeurtenis| &nbsp; | 256 kB | 1 MB |
| Aantal consumentengroepen per event hub | &nbsp; |1 |20 |
| Aantal AMQP-verbindingen per naamruimte | Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |100 |5.000|
| Maximale bewaartermijn van gebeurtenisgegevens | &nbsp; |1 dag |1-7 dagen |
| Maximum aantal doorvoereenheden |Wanneer deze limiet wordt overschreden, worden uw gegevens beperkt en wordt een [server bezet-uitzondering](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) gegenereerd. U kunt een groter aantal doorvoereenheden voor een Standard-laag aanvragen door een [ondersteuningsaanvraag](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) in te dienen. [Extra doorvoereenheden](../articles/event-hubs/event-hubs-auto-inflate.md) zijn beschikbaar in blokken van 20 op basis van een vaste aankoop. |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>Toegewezen laag vs. Standard-laag
De Event Hubs Dedicated-aanbieding wordt gefactureerd tegen een vaste maandelijkse prijs, met een minimum van 4 uur gebruik. De Dedicated-laag biedt alle functies van het Standard-abonnement, maar met schaalcapaciteit voor grote bedrijven en limieten voor klanten met veeleisende werkbelastingen. 

Raadpleeg dit [document](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) voor meer informatie over hoe u toegewezen Event Hubs-clusters maakt met Azure-portal.

| Functie | Standard | Toegewezen |
| --- |:---|:---|
| Bandbreedte | 20 TU's (tot 40 TU's) | 20 CU's |
| Naamruimten |  1 | 50 per CU |
| Event Hubs |  10 per naamruimte | 1000 per naamruimte |
| Ingangsgebeurtenissen | Betalen per miljoen gebeurtenissen | Inbegrepen |
| Berichtgrootte | 1\.000.000 bytes | 1\.000.000 bytes |
| Partities | 32 per Event Hub | 1024 per Event Hub<br/>2000 per CU |
| Consumentengroepen | 20 per Event Hub | Geen limiet per CU, 1000 per event hub |
| Brokered verbindingen | 1000 inbegrepen, maximaal 5000 | 100K inbegrepen en max. |
| Bewaarperiode van berichten | 7 dagen, 84 GB inbegrepen per CU | 90 dagen, 10 TB inbegrepen per CU |
| Vastleggen | Betalen per uur | Inbegrepen |


## <a name="schema-registry-limitations"></a>Beperkingen voor schemaregisters

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limieten die dezelfde zijn voor **standard** en **toegewezen**-lagen 
| Functie | Limiet | 
|---|---|--|
| Maximumlengte van een schemagroepnaam | 50 |  
| Maximumlengte van een schemanaam | 100 |    
| Grootte in bytes per schema | 1 MB |   
| Aantal eigenschappen per schemagroep | 1024 |
| Grootte in bytes per groepeigenschapssleutel | 256 | 
| Grootte in bytes per groepeigenschapswaarde | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limieten die anders zijn voor **standard** en **toegewezen**-lagen 

| Limiet | Standard | Toegewezen | 
|---|---|--|---|
| Grootte van het schemaregister (naamruimte) in megabytes | 25 |  1024 |
| Aantal schemagroepen in een schemaregister of naamruimte | 1 - exclusief de standaardgroep | 1000 |
| Aantal schemaversies in alle themagroepen | 25 | 10.000 |