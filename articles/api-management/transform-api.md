---
title: 'Zelfstudie: Uw API transformeren en beveiligen met Azure API Management | Microsoft Docs'
description: In deze zelfstudie leert u hoe u uw API in API Management kunt beveiligen met beleid voor transformatie en beperking (frequentielimiet).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010228"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Zelfstudie: Uw API transformeren en beveiligen

In de zelfstudie ziet u hoe u uw API kunt transformeren zodat deze geen persoonlijke informatie over de privéback-end weergeeft. U kunt bijvoorbeeld de gegevens verbergen over de technologiestack die wordt uitgevoerd voor de back-end. U kunt ook de oorspronkelijke URL's verbergen die worden weergegeven in de hoofdtekst van het HTTP-antwoord van de API, en deze in plaats hiervan omleiden naar de APIM-gateway.

In deze zelfstudie kunt u ook zien hoe eenvoudig het is om beveiliging toe te voegen voor uw back-end-API, door een frequentielimiet te configureren met Azure API Management. U kunt bijvoorbeeld de frequentie van API-aanroepen beperken, zodat de API niet te veel wordt gebruikt door ontwikkelaars. Zie [API Management-beleid](api-management-policies.md) voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> -   Een API transformeren om antwoordheaders te verwijderen
> -   Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's
> -   Een API beveiligen door beleid voor frequentielimieten (beperking) toe te voegen
> -   De transformaties testen

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Beleidsregels in de portal":::

## <a name="prerequisites"></a>Vereisten

-   Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
-   Inzicht in het [beleidsconcept in Azure API Management](api-management-howto-policies.md).
-   Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
-   Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Een API transformeren om antwoordheaders te verwijderen

In deze sectie wordt beschreven hoe u de HTTP-headers kunt verbergen die u niet wilt weergeven aan gebruikers. Dit voorbeeld laat zien hoe u de volgende headers kunt verwijderen in het HTTP-antwoord:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Het oorspronkelijke antwoord testen

Het oorspronkelijke antwoord zien:

1. Selecteer **API’s** in uw service-exemplaar van API Management.
1. Selecteer **Demo Conference-API** in de API-lijst.
1. Selecteer bovenaan het scherm het tabblad **Testen**.
1. Selecteer de bewerking **GetSpeakers** en selecteer **Verzenden**.

Het oorspronkelijke antwoord moet er ongeveer uitzien als op deze afbeelding:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Oorspronkelijk API-antwoord":::

Zoals u ziet, bevat het antwoord de headers **X-AspNet-Version** en **X-Powered-By**.

### <a name="set-the-transformation-policy"></a>Transformatiebeleid instellen

1. Selecteer **Demo Conference-API** > **Ontwerpen** > **Alle bewerkingen**.
4. Selecteer in de sectie **Uitgaande verwerking** het pictogram code-editor ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Navigeren naar uitgaand beleid" border="false":::

1. Plaats de cursor in het **&lt;uitgaande&gt;** element en selecteer **Fragmenten weergeven** in de rechterbovenhoek.
1. Selecteer in het rechtervenster onder **Transformatiebeleid** twee keer **HTTP-header instellen** (om twee beleidsfragmenten in te voegen).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Beleid voor HTTP-header instellen":::

1. Wijzig de code **\<outbound>** zodat deze er als volgt uitziet:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="HTTP-header instellen":::

1. Selecteer **Opslaan**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's

In deze sectie ziet u hoe u de oorspronkelijke URL's kunt verbergen die worden weergegeven in de hoofdtekst van het HTTP-antwoord van de API, en deze in plaats hiervan kunt omleiden naar de APIM-gateway.

### <a name="test-the-original-response"></a>Het oorspronkelijke antwoord testen

Het oorspronkelijke antwoord zien:

1. Selecteer **Demo Conference-API** > **Testen**.
1. Selecteer de bewerking **GetSpeakers** en selecteer **Verzenden**.

    Zoals u ziet, bevat het antwoord de oorspronkelijke back-end-URL's:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Oorspronkelijke URL's in antwoord":::


### <a name="set-the-transformation-policy"></a>Transformatiebeleid instellen

1.  Selecteer **Demo Conference-API** > **Alle bewerkingen** > **Ontwerpen**.
1.  Selecteer in de sectie **Uitgaande verwerking** het pictogram code-editor ( **</>** ).
1.  Plaats de cursor in het **&lt;uitgaande&gt;** element en selecteer **Fragmenten weergeven** in de rechterbovenhoek.
1.  Selecteer in het rechtervenster onder **Transformatiebeleid** de optie **URL’s in inhoud verbergen**. 
1.  Selecteer **Opslaan**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Een API beveiligen door beleid voor frequentielimieten toe te voegen

In deze sectie wordt beschreven hoe u beveiliging voor uw back-end-API kunt toevoegen door frequentielimieten te configureren. U kunt bijvoorbeeld de frequentie van API-aanroepen beperken zodat de API niet te veel wordt gebruikt door ontwikkelaars. In dit voorbeeld is de limiet ingesteld op 3 aanroepen per 15 seconden voor elke abonnements-id. Na 15 seconden kan een ontwikkelaar de API opnieuw proberen aan te roepen.

1.  Selecteer **Demo Conference-API** > **Alle bewerkingen** > **Ontwerpen**.
1.  Selecteer in de sectie **Inkomende verwerking** het pictogram code-editor ( **</>** ).
1.  Plaats de cursor in het **&lt;inkomende&gt;** element en selecteer **Fragmenten weergeven** in de rechterbovenhoek.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Beleid voor binnenkomende verwerking instellen" border="false":::

1.  Selecteer in het rechtervenster onder **Toegang tot beperkingsbeleid** de optie **+ Aantal oproepen per sleutel beperken**.
1.  Wijzig de code **rate-limit-by-key** (in het element **\<inbound\>** ) in de volgende code:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>De transformaties testen

Als u nu naar de code in de code-editor kijkt, ziet uw beleid er als volgt uit:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

In de rest van deze sectie worden de beleidstransformaties getest die u in dit artikel hebt ingesteld.

### <a name="test-the-stripped-response-headers"></a>De verwijderde anwoordheaders testen

1. Selecteer **Demo Conference-API** > **Testen**.
1. Selecteer de bewerking **GetSpeakers** en selecteer **Verzenden**.

    Zoals u ziet, zijn de headers verwijderd:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Verwijderde anwoordheaders":::

### <a name="test-the-replaced-url"></a>De vervangen URL testen

1. Selecteer **Demo Conference-API** > **Testen**.
1. Selecteer de bewerking **GetSpeakers** en selecteer **Verzenden**.

    Zoals u ziet, is de URL vervangen.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Vervangen URL":::

### <a name="test-the-rate-limit-throttling"></a>De frequentielimiet testen

1. Selecteer **Demo Conference-API** > **Testen**.
1. Selecteer de bewerking **GetSpeakers**. Selecteer drie keer achter elkaar de optie **Verzenden**.

    Nadat de aanvraag 3 keer is verzonden, ontvangt u het antwoord **429 Te veel aanvragen**.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Te veel aanvragen":::

1. Wacht ongeveer 15 seconden en selecteer opnieuw **Verzenden**. Deze keer ontvangt u, als het goed is, het antwoord **200 OK**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> -   Een API transformeren om antwoordheaders te verwijderen
> -   Oorspronkelijke URL's in de hoofdtekst van het API-antwoord vervangen door APIM-gateway-URL's
> -   Een API beveiligen door beleid voor frequentielimieten toe te voegen
> -   De transformaties testen

Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Uw API controleren](api-management-howto-use-azure-monitor.md)
