---
title: Gebeurtenissen vastleggen in azure Event Hubs in azure API Management | Microsoft Docs
description: Meer informatie over het vastleggen van gebeurtenissen in azure Event Hubs in azure API Management. Event Hubs is een zeer schaal bare service voor gegevens ingang.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072404"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Gebeurtenissen vastleggen in azure Event Hubs in azure API Management
Azure Event Hubs is een zeer schaalbare service voor inkomende gegevens die miljoenen gebeurtenissen per seconde kan opnemen, voor verwerking en analyse van de enorme hoeveelheden gegevens die worden geproduceerd door verbonden apparaten en toepassingen. Event Hubs fungeert als de "front deur" voor een gebeurtenis pijplijn en wanneer gegevens worden verzameld in een Event Hub, kan deze worden getransformeerd en opgeslagen met behulp van een realtime analyse provider of batches/opslag adapters. Event Hubs koppelt de productie van een gebeurtenissenstroom los van het gebruik van deze gebeurtenissen, zodat de consumenten ervan toegang hebben tot de gebeurtenissen op basis van hun eigen planning.

Dit artikel is een aanvulling op de [integratie van azure API Management met Event hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video en beschrijft hoe u API Management gebeurtenissen registreert met behulp van Azure Event hubs.

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Zie [een event hubs naam ruimte en een event hub maken met behulp van de Azure Portal](../event-hubs/event-hubs-create.md)voor gedetailleerde stappen voor het maken van een event hub en het ophalen van verbindings reeksen die u nodig hebt om gebeurtenissen te verzenden en te ontvangen van en naar de Event hub.

## <a name="create-an-api-management-logger"></a>Een API Management logger maken
Nu u een event hub hebt, is de volgende stap het configureren van een [logger](/rest/api/apimanagement/2019-12-01/logger) in uw API Management-service zodat deze gebeurtenissen kan registreren in de Event hub.

API Management-logboeken worden geconfigureerd met behulp van de [API Management rest API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST). Zie [Logboeken maken](/rest/api/apimanagement/2019-12-01/logger/createorupdate)voor voor beelden van gedetailleerde aanvragen.

## <a name="configure-log-to-eventhub-policies"></a>Logboek-naar-eventhub-beleid configureren

Zodra uw logboek is geconfigureerd in API Management, kunt u het beleid voor eventhub configureren om de gewenste gebeurtenissen te registreren. Het logboek-naar-eventhub-beleid kan worden gebruikt in de sectie inkomend beleid of in de sectie uitgaand beleid.

1. Blader naar de APIM-instantie.
2. Selecteer het tabblad API.
3. Selecteer de API waaraan u het beleid wilt toevoegen. In dit voor beeld voegen we een beleid toe aan de **echo-API** in het product **Unlimited** .
4. Selecteer **Alle bewerkingen**.
5. Selecteer boven aan het scherm het tabblad ontwerpen.
6. Klik in het venster inkomend of uitgaand verkeer op het drie hoekje (naast het potlood).
7. Selecteer de code-editor. Zie [beleid instellen of bewerken](set-edit-policies.md)voor meer informatie.
8. Plaats de cursor in de `inbound` `outbound` sectie of beleids regel.
9. Selecteer in het venster aan de rechter kant het **Geavanceerde beleid**  >  **logboek op EventHub**. Hiermee wordt de `log-to-eventhub` sjabloon voor de beleids verklaring ingevoegd.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Vervang door `logger-id` de waarde die u hebt gebruikt `{loggerId}` in de aanvraag-URL om de logger in de vorige stap te maken.

U kunt elke expressie gebruiken die een teken reeks retourneert als de waarde van het `log-to-eventhub` element. In dit voor beeld wordt een teken reeks in JSON-indeling met de datum en tijd, de service naam, de aanvraag-ID, het IP-adres van de aanvraag en de bewerkings naam vastgelegd.

Klik op **Opslaan** om de bijgewerkte beleids configuratie op te slaan. Zodra het beleid is opgeslagen, is het actief en worden gebeurtenissen geregistreerd in de aangewezen Event hub.

> [!NOTE]
> De Maxi maal ondersteunde bericht grootte die kan worden verzonden naar een Event Hub van dit API Management beleid is 200 kB (KB). Als een bericht dat wordt verzonden naar een Event Hub groter is dan 200 KB, wordt het automatisch afgekapt en wordt het afgekapte bericht overgebracht naar Event hubs.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Bekijk een voor beeld van het logboek in Event Hubs met behulp van Azure Stream Analytics

U kunt een voor beeld van het logboek in Event Hubs bekijken met behulp van [Azure stream Analytics query's](../event-hubs/process-data-azure-stream-analytics.md). 

1. Blader in het Azure Portal naar de Event Hub waarnaar de logboek gebeurtenissen verzendt. 
2. Selecteer onder **functies** het tabblad **proces gegevens** .
3. Op de kaart **real time Insights van gebeurtenissen inschakelen** selecteert u **verkennen**.
4. U moet een voor beeld van het logboek op het tabblad **invoer voorbeeld** kunnen bekijken. Als de weer gegeven gegevens niet actueel zijn, selecteert u **vernieuwen** om de meest recente gebeurtenissen te bekijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure Event Hubs
  * [Aan de slag met Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Berichten ontvangen met EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Programmeer handleiding voor Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Meer informatie over de integratie van API Management en Event Hubs
  * [Verwijzing naar traceer entiteit](/rest/api/apimanagement/2019-12-01/logger)
  * [Naslag informatie voor het aanmelden bij het eventhub-beleid](./api-management-advanced-policies.md#log-to-eventhub)
  * [Bewaak uw Api's met Azure API Management, Event Hubs en Moesif](api-management-log-to-eventhub-sample.md)  
* Meer informatie over [integratie met Azure-toepassing Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png