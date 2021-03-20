---
title: "Zelfstudie: Fouten opsporen in API's in Azure API Management met behulp van de tracering van aanvragen"
description: Volg de stappen in deze zelfstudie om tracering in te schakelen en aanvraagverwerkingsstappen te controleren in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542311"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Zelfstudie: Fouten opsporen in uw API's met behulp van de tracering van aanvragen

In deze zelfstudie wordt beschreven hoe u aanvraagverwerking controleert (traceert) in Azure API Management om u te helpen fouten op te sporen en problemen op te lossen in uw API. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een voorbeeld van een aanroep traceren
> * Aanvraagverwerkingsstappen controleren

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API-inspector":::

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Instelling 'Traceren toestaan' verifiëren 

De instelling **Traceren toestaan** voor het abonnement dat voor uw API wordt gebruikt, moet ingeschakeld zijn. Als u het ingebouwde all-access abonnement gebruikt, is de instelling standaard ingeschakeld. Om dit te verifiëren in de portal, gaat u naar uw exemplaar van API Management en selecteert u **Abonnementen**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Traceren toestaan voor abonnement":::

## <a name="trace-a-call"></a>Een aanroep traceren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw exemplaar van API Management.
1. Selecteer **API's**.
1. Selecteer **Demo Conference-API** in uw lijst met API's.
1. Selecteer het tabblad **Testen**.
1. Selecteer de bewerking **GetSpeakers**.
1. Bevestig dat de HTTP-aanvraagheader **Ocp-Apim-Trace: True** en een geldige waarde voor **Ocp-Apim-Subscription-Key** bevat. Als dat niet het geval is, selecteert u **+ Header toevoegen** om de header toe te voegen.
1. Selecteer **Verzenden** om een API-aanroep te maken.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="API-tracering configureren":::

> [!TIP]
> Als **Ocp-Apim-Subscription-Key** niet automatisch is ingevuld in de HTTP-aanvraag, kunt u deze ophalen in de portal. Selecteer **Abonnementen** en open het contextmenu ( **...** ) voor uw abonnement. Selecteer **Sleutels weergeven/verbergen**. U kunt sleutels indien nodig ook opnieuw genereren. Voeg vervolgens een sleutel aan de header toe.

## <a name="review-trace-information"></a>Traceringsgegevens controleren

1. Nadat de aanroep is voltooid, gaat u naar het tabblad **Tracering** in het **HTTP-antwoord**.
1. Selecteer een van de volgende koppelingen om naar gedetailleerde traceringsgegevens te gaan: **Inkomend**, **Back-end**, **Uitgaand**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Antwoordtracering controleren":::

    * **Inkomend**: Toont de oorspronkelijke aanvraag die API Management van de aanroepende functie heeft ontvangen en de beleidsregels die op de aanvraag worden toegepast. Als u bijvoorbeeld beleidsregels hebt toegevoegd in [Zelfstudie: Uw API transformeren en beveiligen](transform-api.md), worden ze daar weergegeven.

    * **Back-end**: Toont de aanvragen die API Management naar de API-back-end heeft verzonden en het antwoord daarop.

    * **Uitgaand**: Toont de beleidsregels die op het antwoord worden toegepast voordat het naar de aanroepende functie wordt teruggestuurd.

    > [!TIP]
    > Bij elke stap wordt ook de verstreken tijd weergegeven vanaf dat de aanvraag is ontvangen door de API Management.

1. Op het tabblad **Bericht** toont de header **ocp-apim-trace-location** de locatie van de traceringsgegevens die in Azure Blob Storage zijn opgeslagen. Ga indien nodig naar deze locatie om de traceringsgegevens op te halen.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Traceringslocatie in Azure Storage":::
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een voorbeeld van een aanroep traceren
> * Aanvraagverwerkingsstappen controleren

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Revisies gebruiken](api-management-get-started-revise-api.md)
