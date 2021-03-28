---
title: 'Quickstart: een sms-bericht verzenden'
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over het verzenden van een sms-bericht met Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 9514f1616c0d70d7d195af2f052c604973709204
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644301"
---
# <a name="quickstart-send-an-sms-message"></a>Quickstart: Een sms-bericht verzenden

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> Sms-berichten kunnen worden verzonden naar telefoonnummers in de Verenigde Staten. Communication Services biedt nog geen ondersteuning voor sms-berichten vanaf telefoonnummers in andere geografische gebieden.
> Zie voor meer informatie **[telefoon nummer typen](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen wilt oplossen met betrekking tot de bezorging van sms'en, kunt u [bezorgingsrapportage met Event Grid inschakelen](./handle-sms-events.md) om bezorgingsgegevens van sms-berichten vast te leggen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u sms-berichten kunt verzenden met Azure Communication Services.

> [!div class="nextstepaction"]
> [SMS-en bezorgings rapport gebeurtenissen ontvangen](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Telefoon nummer typen](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Meer informatie over sms](../../concepts/telephony-sms/concepts.md)
