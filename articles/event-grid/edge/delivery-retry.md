---
title: Leverings-en nieuwe Azure Event Grid IoT Edge | Microsoft Docs
description: Levering en probeer het opnieuw in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171615"
---
# <a name="delivery-and-retry"></a>Leveren en opnieuw proberen

Event Grid biedt een duurzame levering. Er wordt een poging gedaan om elk bericht ten minste één keer te leveren voor elk overeenkomend abonnement. Als het eind punt van een abonnee de ontvangst van een gebeurtenis niet bevestigt of als er een fout is opgetreden, Event Grid nieuwe pogingen op basis van een vast **schema voor opnieuw proberen** en **beleid voor opnieuw proberen**.  Standaard levert de module Event Grid één gebeurtenis tegelijk naar de abonnee. De payload is echter een matrix met één gebeurtenis. U kunt de module meer dan één gebeurtenis tegelijk leveren door de functie output batching in te scha kelen. Zie [output batching](delivery-output-batching.md)voor meer informatie over deze functie.  

> [!IMPORTANT]
>Er is geen ondersteuning voor persistentie voor gebeurtenis gegevens. Dit betekent dat het opnieuw implementeren of opnieuw opstarten van de module Event Grid ertoe leidt dat u gebeurtenissen kwijtraakt die nog niet zijn geleverd.

## <a name="retry-schedule"></a>Schema voor opnieuw proberen

Event Grid wacht een reactie van 60 seconden nadat een bericht is afgeleverd. Als het eind punt van de abonnee niet het antwoord stuurt, wordt het bericht in een van onze back-upwachtrijen voor volgende pogingen in de wachtrij geplaatst.

Er zijn twee vooraf geconfigureerde back-ups van wacht rijen die bepalen op welk schema een nieuwe poging wordt gedaan. Dit zijn:

| Schema | Beschrijving |
| ---------| ------------ |
| 1 minuut | Berichten die hier worden beëindigd, worden elke minuut geprobeerd.
| 10 minuten | Berichten die hier worden beëindigd, worden elke tien minuten geprobeerd.

### <a name="how-it-works"></a>Uitleg

1. Bericht binnenkomt in de Event Grid-module. Er wordt geprobeerd deze direct te leveren.
1. Als er een fout optreedt, wordt het bericht in de wachtrij van 1 minuut in de wacht gezet en na een minuut opnieuw geprobeerd.
1. Als de levering blijft mislukken, wordt het bericht in de wachtrij van 10 minuten geplaatst en wordt elke 10 minuten opnieuw geprobeerd.
1. De leveringen worden uitgevoerd tot het slagen of het opnieuw proberen van beleids limieten is bereikt.

## <a name="retry-policy-limits"></a>Limieten voor beleid opnieuw proberen

Er zijn twee configuraties die het beleid voor opnieuw proberen bepalen. Dit zijn:

* Maximum aantal pogingen
* Time-to-Live (TTL) van gebeurtenis

Er wordt een gebeurtenis verwijderd als een van de limieten van het beleid voor opnieuw proberen is bereikt. Het schema voor nieuwe pogingen is beschreven in de sectie schema opnieuw proberen. De configuratie van deze limieten kan worden uitgevoerd voor alle abonnees of per abonnement. In de volgende sectie vindt u meer informatie.

## <a name="configuring-defaults-for-all-subscribers"></a>Standaard instellingen voor alle abonnees configureren

Er zijn twee eigenschappen: `brokers__defaultMaxDeliveryAttempts` en `broker__defaultEventTimeToLiveInSeconds` die kunnen worden geconfigureerd als onderdeel van de Event grid-implementatie, waarmee de standaard instellingen voor het beleid voor opnieuw proberen voor alle abonnees worden beheerd.

| Eigenschapsnaam | Beschrijving |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Het maximum aantal pogingen om een gebeurtenis te leveren. Standaard waarde: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Gebeurtenis-TTL in seconden waarna een gebeurtenis wordt verwijderd als deze niet wordt bezorgd. Standaard waarde: **7200** seconden

## <a name="configuring-defaults-per-subscriber"></a>Standaard instellingen per abonnee configureren

U kunt ook limieten voor het beleid voor opnieuw proberen opgeven op basis van elk abonnement.
Zie onze [API-documentatie](api.md) voor informatie over het configureren van standaard instellingen per abonnee. Standaard instellingen op abonnements niveau overschrijven de module configuraties.

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld wordt het beleid voor opnieuw proberen ingesteld in de module Event Grid met maxNumberOfAttempts = 3 en gebeurtenis-TTL van 30 minuten

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

In het volgende voor beeld wordt een web Hook-abonnement met maxNumberOfAttempts = 3 en Event TTL van 30 minuten ingesteld

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
