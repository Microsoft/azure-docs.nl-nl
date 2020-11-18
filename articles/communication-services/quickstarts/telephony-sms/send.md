---
title: 'Quickstart: een sms-bericht verzenden'
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over het verzenden van een sms-bericht met Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 52cec861ba81a804081c4b879ab73333a3281e61
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360289"
---
# <a name="quickstart-send-an-sms-message"></a>Quickstart: Een sms-bericht verzenden

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen wilt oplossen met betrekking tot de bezorging van sms'en, kunt u [bezorgingsrapportage met Event Grid inschakelen](./handle-sms-events.md) om bezorgingsgegevens van sms-berichten vast te leggen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u sms-berichten kunt verzenden met Azure Communication Services.

> [!div class="nextstepaction"]
> [Abonneren op sms-gebeurtenissen](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Uw PSTN-oplossing plannen](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Meer informatie over sms](../../concepts/telephony-sms/concepts.md)