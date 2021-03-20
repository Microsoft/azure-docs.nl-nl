---
title: Analytics op Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker alle chat logboeken en andere telemetrie opslaat, als u app Insights hebt ingeschakeld tijdens het maken van uw QnA Maker-service. Voer de voorbeeld query's uit om uw chat logboeken van app Insights te ontvangen.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 5f149dd6db82b66b45a4c995e2004936481af786
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352419"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Analytische gegevens verkrijgen voor uw knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

QnA Maker alle chat logboeken en andere telemetrie opslaat, als u Application Insights hebt ingeschakeld tijdens het [maken van de QnA Maker service](./set-up-qnamaker-service-azure.md). Voer de voorbeeld query's uit om uw chat logboeken van Application Insights op te halen.

1. Ga naar uw Application Insights-resource.

    ![Uw Application Insights-resource selecteren](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecteer **logboek (Analytics)**. Er wordt een nieuw venster geopend waarin u QnA Maker telemetrie kunt opvragen.

3. Plak de volgende query en voer deze uit.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Selecteer **Uitvoeren** om de query uit te voeren.

    [![Query uitvoeren om vragen, antwoorden en Score van gebruikers te bepalen](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

QnA Maker Managed (preview) maakt gebruik van Azure diagnostische logboek registratie voor het opslaan van de telemetriegegevens en chat Logboeken. Volg de onderstaande stappen om voorbeeld query's uit te voeren om analyses te verkrijgen over het gebruik van uw QnA Maker Knowledge Base.

1. [Schakel logboek registratie van diagnostische gegevens in](../../diagnostic-logging.md) voor uw QnA Maker Managed (preview)-service.

2. In de vorige stap selecteert u naast **controle, RequestResponse en AllMetrics** **traceren** voor logboek registratie

    ![Traceer logboek registratie inschakelen in QnA Maker beheerd (preview-versie)](../media/qnamaker-how-to-analytics-kb/qnamaker-v2-enable-trace-logging.png)

---

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Query's uitvoeren voor andere analyses op uw QnA Maker Knowledge Base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

### <a name="total-90-day-traffic"></a>Totaal 90-dagen verkeer

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Totaal vraag verkeer binnen een bepaalde tijds periode

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Gebruikers verkeer

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latentie distributie van vragen

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Niet-beantwoorde vragen

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

### <a name="all-qna-chat-log"></a>Alle QnA chat-logboek

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Aantal verkeer per Knowledge Base en gebruiker in een bepaalde periode

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Latentie van GenerateAnswer-API

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Gemiddelde latentie van alle bewerkingen

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Niet-beantwoorde vragen

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Capactiy kiezen](./improve-knowledge-base.md)