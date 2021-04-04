---
title: Api's voor het meten van service-micro soft Commercial Marketplace
description: Met de gebruiks gebeurtenis-API kunt u gebruiks gebeurtenissen voor SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace verzenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436059"
---
# <a name="marketplace-metered-billing-apis"></a>Facturerings-Api's met geplaatste Marketplace

De gefactureerde facturerings-Api's moeten worden gebruikt wanneer de uitgever aangepaste meting dimensies maakt voor een aanbieding die in het partner centrum moet worden gepubliceerd. Integratie met de gefactureerde facturerings-Api's is vereist voor een gekochte aanbieding met een of meer plannen met aangepaste dimensies voor het verzenden van gebruiks gebeurtenissen.

Zie voor meer informatie over het maken van aangepaste meet dimensies voor SaaS, [SaaS-data limiet voor gefactureerd](saas-metered-billing.md).

Voor meer informatie over het maken van aangepaste meting dimensies voor een Azure-toepassing aanbieding met een abonnement op een beheerde app raadpleegt u [de installatie Details van uw Azure-toepassings aanbieding configureren](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>Opmerking over het afdwingen van TLS 1,2

De versie van de TLS-versie 1,2 wordt afgedwongen als de minimale versie voor HTTPS-communicatie. Zorg ervoor dat u deze TLS-versie in uw code gebruikt. TLS-versie 1,0 en 1,1 worden afgeschaft en verbindings pogingen worden geweigerd.

## <a name="metered-billing-single-usage-event"></a>Enkele gebruiks gebeurtenis met gecontroleerde facturering

De gebruiks gebeurtenis-API moet worden aangeroepen door de uitgever om gebruiks gebeurtenissen te verzenden voor een actieve resource (geabonneerd) voor het plan dat door de specifieke klant wordt aangeschaft. De gebruiks gebeurtenis wordt afzonderlijk verzonden voor elke aangepaste dimensie van het plan dat door de uitgever is gedefinieerd bij het publiceren van de aanbieding.

Er kan slechts één gebruiks gebeurtenis voor elk uur van een kalenderdag worden verzonden. U kunt bijvoorbeeld op 8: om 05:15 vandaag een gebruiks gebeurtenis verzenden. Als deze gebeurtenis wordt geaccepteerd, wordt de gebeurtenis voor het volgende gebruik geaccepteerd van 9:00 uur vandaag. Als u een extra gebeurtenis tussen 8:15 en 8:59:59 vandaag verzendt, wordt deze als een duplicaat afgewezen. Verzamel alle eenheden die in een uur worden verbruikt en verzend deze vervolgens in één gebeurtenis.

Er kan slechts één gebruiks gebeurtenis worden verzonden voor elk uur van een kalenderdag per resource. Als er in een uur meer dan één eenheid wordt verbruikt, kunt u alle eenheden die in het uur zijn verbruiken, verzamelen en vervolgens in één gebeurtenis verzenden. Gebruiks gebeurtenissen kunnen gedurende de afgelopen 24 uur alleen worden verzonden. Als u een gebruiks gebeurtenis op elk gewenst moment tussen 8:00 en 8:59:59 (en wordt geaccepteerd) en een extra gebeurtenis voor dezelfde dag tussen 8:00 en 8:59:59 verzendt, wordt deze als een duplicaat afgewezen.

**Post**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Query parameters:*

| Parameter | Aanbeveling          |
| ---------- | ---------------------- |
| `ApiVersion` | Gebruik 2018-08-31. |
| | |

*Aanvraag headers:*

| Inhouds type       | `application/json` gebruiken  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en weer gegeven in de antwoord headers. |
| `x-ms-correlationid` | Unieke teken reeks waarde voor de bewerking op de client. Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
| `authorization`   | Een uniek toegangs token dat de ISV identificeert die deze API aanroept. De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd voor <br> <ul> <li> SaaS in [het token ophalen met een HTTP Post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Beheerde toepassing in [verificatie strategieën](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Voor beeld van een aanvraag tekst:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` heeft een andere betekenis voor SaaS-apps en voor beheerde apps die aangepaste meter uitstralen. 

De `resourceId` is de beheerde app voor het plannen van Azure-toepassing beheerde apps `resource group Id` . Een voorbeeld script voor het ophalen hiervan vindt [u in het token Azure Managed Identities gebruiken](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Voor SaaS-aanbiedingen `resourceId` is de SaaS-abonnements-id. Zie [abonnementen weer](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)geven voor meer informatie over SaaS-abonnementen.

### <a name="responses"></a>Antwoorden

Code: 200<br>
OK. De gebruiks emissie is geaccepteerd en opgenomen op de micro soft-zijde voor verdere verwerking en facturering.

Voor beeld van een nettolading van antwoorden: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Code: 400 <br>
Ongeldige aanvraag.

* Ontbrekende of ongeldige aanvraag gegevens.
* `effectiveStartTime` is meer dan 24 uur in het verleden. De gebeurtenis is verlopen.
* Het SaaS-abonnement is niet geabonneerd op de status.

Voor beeld van een nettolading van antwoorden: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Code: 403<br>

Slag. Het autorisatie token is niet gegeven, is ongeldig of verlopen.  Of de aanvraag probeert toegang te krijgen tot een abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Code: 409<br>
Conflicteren. Er is al een gebruiks gebeurtenis gerapporteerd voor de opgegeven resource-ID, effectief gebruiks datum en uur.

Voor beeld van een nettolading van antwoorden: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Gebeurtenis voor batch gebruik met gecontroleerde facturering

Met de gebeurtenis-API voor batch gebruik kunt u gebruiks gebeurtenissen voor meer dan één aangeschafte resource tegelijk verzenden. Ook kunt u er verschillende gebruiks gebeurtenissen voor dezelfde resource mee verzenden, zolang ze voor verschillende kalender uren zijn. Het maximale aantal gebeurtenissen in één batch is 25.

**Post:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Query parameters:*

| Parameter  | Aanbeveling     |
| ---------- | -------------------- |
| `ApiVersion` | Gebruik 2018-08-31. |

*Aanvraag headers:*

| Inhouds type       | `application/json` gebruiken       |
| ------------------ | ------ |
| `x-ms-requestid`     | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet wordt gegeven, wordt er een gegenereerd en gegeven in de antwoord headers. |
| `x-ms-correlationid` | Unieke teken reeks waarde voor de bewerking op de client. Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet is opgenomen, wordt er een gegenereerd en gegeven in de antwoord headers. |
| `authorization`      | Een uniek toegangs token dat de ISV identificeert die deze API aanroept. De indeling is `Bearer <access_token>` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd voor <br> <ul> <li> SaaS in [het token ophalen met een HTTP Post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Beheerde toepassing in [verificatie strategieën](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Voor beeld van een aanvraag tekst:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` heeft een andere betekenis voor SaaS-apps en voor beheerde apps die aangepaste meter uitstralen. 

De `resourceId` is de beheerde app voor het plannen van Azure-toepassing beheerde apps `resource group Id` . Een voorbeeld script voor het ophalen hiervan vindt [u in het token Azure Managed Identities gebruiken](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Voor SaaS-aanbiedingen `resourceId` is de SaaS-abonnements-id. Zie [abonnementen weer](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)geven voor meer informatie over SaaS-abonnementen.

### <a name="responses"></a>Antwoorden

Code: 200<br>
OK. De compressie van het batch-gebruik is geaccepteerd en opgenomen op de micro soft-zijde voor verdere verwerking en facturering. De lijst met antwoorden wordt geretourneerd met de status voor elke afzonderlijke gebeurtenis in de batch. U moet de nettolading van de reactie sequentieel door lopen om inzicht te krijgen in de antwoorden voor elke afzonderlijke gebeurtenis die wordt verzonden als onderdeel van de batch gebeurtenis.

Voor beeld van een nettolading van antwoorden: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Beschrijving van de status code waarnaar wordt verwezen in de `BatchUsageEvent` API-reactie:

| Statuscode  | Description |
| ---------- | -------------------- |
| `Accepted` | Afgewezen. |
| `Expired` | Verlopen gebruik. |
| `Duplicate` | Er is een dubbel gebruik gegeven. |
| `Error` | Foutcode. |
| `ResourceNotFound` | De gegeven gebruiks resource is ongeldig. |
| `ResourceNotAuthorized` | U bent niet gemachtigd om gebruik te maken van deze resource. |
| `InvalidDimension` | De dimensie waarvoor het gebruik wordt door gegeven, is ongeldig voor deze aanbieding/dit plan. |
| `InvalidQuantity` | De door gegeven hoeveelheid is lager dan of gelijk aan 0. |
| `BadArgument` | De invoer ontbreekt of is onjuist. |

Code: 400<br>
Ongeldige aanvraag. De batch bevat meer dan 25 gebruiks gebeurtenissen.

Code: 403<br>
Slag. Het autorisatie token is niet gegeven, is ongeldig of verlopen.  Of de aanvraag probeert toegang te krijgen tot een abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

## <a name="development-and-testing-best-practices"></a>Best practices voor ontwikkelen en testen

Als u de aangepaste meter emissie wilt testen, implementeert u de integratie met de API voor licentie controle, maakt u een plan voor uw gepubliceerde SaaS-aanbieding waarvoor aangepaste dimensies zijn gedefinieerd, met een prijs per eenheid. En publiceer deze aanbieding als preview zodat alleen beperkte gebruikers toegang hebben tot de integratie en deze kunnen testen.

U kunt ook een privé-abonnement gebruiken voor een bestaande Live-aanbieding om de toegang tot dit abonnement te beperken tijdens het testen naar een beperkt publiek.

## <a name="get-support"></a>Ondersteuning krijgen

Volg de instructies in [ondersteuning voor het programma voor commerciële Marketplace in het partner centrum](../support.md) om inzicht te krijgen in de ondersteunings opties voor Publisher en een ondersteunings ticket te openen met micro soft.

## <a name="next-steps"></a>Volgende stappen

Zie [Veelgestelde vragen over api's](./marketplace-metering-service-apis-faq.md)voor de licentie controle service voor meer informatie over de api's voor de meter service.
