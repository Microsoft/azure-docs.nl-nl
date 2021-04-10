---
title: 'Quick Start: poort een telefoon nummer in azure Communication Services'
description: Meer informatie over het overbrengen van een telefoon nummer in uw communicatie Service-Resource
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729923"
---
# <a name="quickstart-port-a-phone-number"></a>Snelstartgids: poort a-telefoon nummer

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Ga aan de slag met Azure Communication Services door uw telefoon nummer te porteren in uw Azure Communication Services-resource. Gratis en geografisch nummer dat is gebaseerd op de Verenigde Staten komen in aanmerking voor poort. Voor meer informatie over telefoon nummer typen gaat u naar de [conceptuele documentatie van het telefoon nummer](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Een actieve Communication Services-resource.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Uw Azure-resource gegevens verzamelen

Voordat u een poort aanvraag initieert, gaat u naar de Azure Portal en selecteert u uw communicatie Services-resource. Als het `Overview` deel venster is geselecteerd, klikt u op de `JSON View` koppeling in de rechter bovenhoek:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Scherm opname van het selecteren van de JSON-weer gave.":::

De **Azure-id** en **onveranderlijke resource-id** van uw resource vastleggen:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Scherm opname van het selecteren van de JSON-eigenschappen.":::

## <a name="initiate-the-port-request"></a>De poort aanvraag initiëren

Gratis en geografisch nummer dat is gebaseerd op de Verenigde Staten komen in aanmerking voor poort. Gebruik een van de volgende formulieren om uw poort aanvraag in te dienen:

- Voor gratis nummers: [gratis nummer poort aanvraag](https://aka.ms/acs-port-form-tollfree)
- Voor geografische nummers die zijn gebaseerd op de Amerikaanse: [geografische nummer poort aanvraag](https://aka.ms/acs-port-form-geographic)

Wanneer u klaar bent, stuurt u uw voltooide poort aanvraag formulier naar acsporting@microsoft.com . Controleer of het onderwerp van uw e-mail bericht begint met ' ACS Port-In-aanvraag '.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * De meta gegevens van uw communicatie Services-bron ophalen
> * Een aanvraag verzenden naar een poort voor uw telefoon nummer

> [!div class="nextstepaction"]
> [Een sms verzenden](../telephony-sms/send.md)
> [Aan de slag met gesprekken](../voice-video-calling/getting-started-with-calling.md)
