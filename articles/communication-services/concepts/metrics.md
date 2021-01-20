---
title: Metrische definities voor Azure Communication Service
titleSuffix: An Azure Communication Services concept document
description: Dit document bevat definities van metrische gegevens die beschikbaar zijn in de Azure Portal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e7ed4ba181d3730ebbea7725451567c93433afb8
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251094"
---
# <a name="metrics-overview"></a>Overzicht van metrische gegevens

Azure Communication Services biedt momenteel metrische gegevens voor chat en sms. [Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) kan worden gebruikt om uw eigen grafieken uit te zetten, afwijkingen in uw metrische waarden te onderzoeken en uw API-verkeer te begrijpen met behulp van de metrische gegevens die chat- en sms-aanvragen verzenden.

## <a name="where-to-find-metrics"></a>Waar u metrische gegevens kunt vinden

Chat- en sms-services in Azure Communication Services verzenden metrische gegevens voor API-aanvragen. Deze metrische gegevens kunt u vinden op de blade Metrische gegevens onder uw Communication Services-resource. U kunt ook permanente dashboards maken met behulp van de blade werkmappen onder uw Communication Services-resource.

## <a name="metric-definitions"></a>Metrische definities

Er zijn twee typen aanvragen die worden weergegeven in de metrische gegevens van Communication Services: **Chat-API-aanvragen** en **Sms-API-aanvragen**.

De metrische gegevens van zowel chat- als sms-API-aanvragen bevatten drie dimensies die u kunt gebruiken voor het filteren van uw metrische gegevens. Deze dimensies kunnen worden geaggregeerd met behulp van het aggregatietype `Count` en ondersteunen alle standaard Azure-aggregatie-tijdreeksen, inclusief `Sum`, `Average`, `Min`en `Max`.

Meer informatie over ondersteunde aggregatietypen en aggregaties van tijdreeksen vindt u in [Geavanceerde functies van Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md#aggregation)

- **Bewerking** - Alle bewerkingen of routes die kunnen worden aangeroepen op de ACS chat-gateway.
- **Statuscode** - De reactie van de statuscode die na de aanvraag is verzonden.
- **StatusSubKlasse** - De statuscodereeks die na het antwoord is verzonden. 


### <a name="chat-api-request-metric-operations"></a>Metrische bewerkingen voor de aanvraag van de chat-API

De volgende bewerkingen zijn beschikbaar voor de metrische gegevens van de chat API-aanvraag:

| Bewerking/route    | Beschrijving                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Hiermee wordt een bericht opgehaald door middel van bericht-id. |
| ListChatMessages     | Hiermee wordt een lijst met chatberichten van een thread opgehaald. |
| SendChatMessage      | Hiermee wordt een chatbericht naar een thread. verzonden. |
| UpdateChatMessage    | Hiermee wordt een chatbericht bijgewerkt. |
| DeleteChatMessage    | Hiermee wordt een chatbericht verwijderd. |
| GetChatThread        | Hiermee wordt een chat-thread opgehaald. |
| ListChatThreads      | Hiermee wordt de lijst met chat-threads van een gebruiker opgehaald. |
| UpdateChatThread     | Hiermee worden de eigenschappen van een chat-thread bijgewerkt. |
| CreateChatThread     | Hiermee wordt een chat-thread gemaakt. |
| DeleteChatThread     | Hiermee wordt een thread verwijderd. |
| GetReadReceipts      | Hiermee worden leesbevestigingen voor een thread opgehaald. |
| SendReadReceipt      | Hiermee wordt een gebeurtenis voor een leesbevestiging naar een thread verzonden, namens een gebruiker. |
| SendTypingIndicator           | Hiermee wordt een type-gebeurtenis naar een thread gepost, namens een gebruiker. |
| ListChatThreadParticipants    | Hiermee worden de leden van een thread opgehaald. |
| AddChatThreadParticipants     | Hiermee worden leden aan een thread toegevoegd. Als de leden al bestaan, gebeurt er niets. |
| RemoveChatThreadParticipant   | Een lid verwijderen uit een thread. |

:::image type="content" source="./media/chat-metric.png" alt-text="Metrische gegevens van chat API-aanvraag.":::

Als er een aanvraag wordt ingediend bij een bewerking die niet wordt herkend, ontvangt u een antwoord met de waarde "Onjuiste route".

### <a name="sms-api-requests"></a>Sms-API-aanvragen

De volgende bewerkingen zijn beschikbaar voor de metrische gegevens van de sms-API-aanvraag:

| Bewerking/route    | Beschrijving                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Hiermee wordt een sms-bericht verzonden. |
| SMSDeliveryReportsReceived     | Hiermee worden sms-bezorgingsrapporten opgehaald |
| SMSMessagesReceived      | Hiermee worden sms-berichten opgehaald. |


:::image type="content" source="./media/sms-metric.png" alt-text="Metrische gegevens van sms-API-aanvraag.":::

### <a name="authentication-api-requests"></a>Verificatie-API-aanvragen

De volgende bewerkingen zijn beschikbaar voor de metrische gegevens van de Verificatie-API-aanvraag:

| Bewerking/route    | Beschrijving                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Hiermee wordt een identiteit gemaakt die één gebruiker vertegenwoordigt. |
| DeleteIdentity       | Hiermee wordt een identiteit verwijderd. |
| CreateToken          | Hiermee wordt een toegangstoken gemaakt. |
| RevokeToken          | Hiermee worden alle toegangstokens ingetrokken die vóór een bepaald tijdstip zijn gemaakt voor een identiteit. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Metrische gegevens van Data Platform](../../azure-monitor/platform/data-platform-metrics.md)
