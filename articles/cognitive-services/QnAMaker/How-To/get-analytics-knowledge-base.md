---
title: Analýzy na základě znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Pokud jste povolili App Insights během vytváření služby QnA Maker, QnA Maker ukládá všechny protokoly chatu a další telemetrie. Spusťte Ukázkové dotazy pro získání protokolů chatu z App Insights.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 00b7b88aa4ce0cab2a2379756e40054f27fc633b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131646"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Získání analýz pro znalostní bázi

Pokud jste povolili App Insights během [vytváření služby QnA maker](./set-up-qnamaker-service-azure.md), QnA maker ukládá všechny protokoly chatu a další telemetrie. Spusťte Ukázkové dotazy pro získání protokolů chatu z App Insights.

1. Přejít na prostředek služby App Insights.

    ![Vyberte prostředek Application Insights.](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Vyberte **protokol (Analytics)**. Otevře se nové okno, kde se můžete dotazovat QnA Maker telemetrie.

3. Vložte následující dotaz a spusťte ho.

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

    Vyberte **Spustit** a dotaz spusťte.

    [![Spustit dotaz pro určení otázek, odpovědí a skóre uživatelů](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Spouštění dotazů pro další analýzy na QnA Maker znalostní bázi Knowledge Base

### <a name="total-90-day-traffic"></a>Celkový počet 90 dní provozu

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Celkový provoz dotazů v daném časovém období

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

### <a name="user-traffic"></a>Přenos uživatelů

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

### <a name="latency-distribution-of-questions"></a>Latence při distribuci otázek

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Nezodpovězené otázky

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zvolit capactiy](./improve-knowledge-base.md)
